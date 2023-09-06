---
layout: page
title: rancher
parent: 技术文档
---

## 概念区别

- [Rancher 和 RKE 却别](https://rancher.com/blog/2019/2019-02-04-rancher-vs-rke/)

## error 的排查过程

- gitlab 的 log，位于 gitlab 服务器上的

## 在 gitlab 中添加 rancher 中的 k3s

- 不要遵循 github 的文档创建用户
- 在 rancher 的 ui 面板，点击右上角，`用户头像`，`API&KEY`，`添加Key`
- 获得`api地址`，`beartoken`
- 在 rancher 的 ui 面板上方菜单，选择`全局`，进入`系统设置`，`显示cacerts`
- 获得`CA证书`

## 进入 coreDns

- coreDns 用于 k8s 的 dns 解析
- coreDns 基于 scratch，[dockerFile](https://github.com/coredns/coredns/blob/master/Dockerfile)，内部没有 shell
- 在 rancher 中可以使用 sidecar 访问，[文档](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/workloads/add-a-sidecar/)

## dns 错误，无法解析域名问题

- 在 rancher 的 config 中找到 coredns 的配置
- 如果 coredns 采用了`8.8.8.8`做上游，可以将`forward . /etc/resolv.conf`修改为`forward . 223.5.5.5`或者用`nano`(不要使用`vim`)修改主机上的`/etc/resolv.conf`文件
- 对于 `repo.wolwobiotech.com` 的解析需要添加到`172.28.1.3`
  ```
  hosts {
      172.28.1.3 repo.wolwobiotech.com
      fallthrough
  }
  ```

## rancher 和 nginx 反向代理

- nginx 配置

```
server {
 listen 443 ssl;
  server_name rancher.wolwobiotech.com;
  include uselessLog.conf;
  include ssl.conf;
  location / {
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-NginX-Proxy true;
    proxy_set_header X-Forwarded-Port $server_port;
    proxy_set_header Upgrade $http_upgrade;
    proxy_pass https://rancher;
  }

  error_log /var/log/rancher_error.log warn;

}
```

## 安装 minio

- data.yaml
  ```yaml
  apiVersion: v1
  kind: PersistentVolume
  metadata:
    name: data
  spec:
    accessModes:
      - ReadWriteOnce
    capacity:
      storage: 10Gi
    hostPath:
      path: /data
      type: DirectoryOrCreate
    persistentVolumeReclaimPolicy: Retain
    volumeMode: Filesystem
  status:
    phase: Available
  ```
- 创建 pv
  ```bash
  kubectl apply -f data.yaml
  ```
- 添加 minio 镜像
  ```bash
  helm repo add minio https://helm.min.io/
  ```
- minio.yaml
  ```yaml
  persistence:
    size: 5Gi
  securityContext:
    enabled: false
  ```
- 安装 minio
  ```bash
  helm repo update
  helm install -f minio.yaml minio minio/minio
  ```
- 添加 minio 的 ingress 入口

## chrome 不信任 rancher 证书

- 将 k8s 的 secret 导出成 crt
  ```
  kubectl get secret tls-rancher-ingress -n cattle-system -o=jsonpath='{@.data.ca\.crt}' | base64 -D > rancher.crt
  ```

## gitlab-runner 安装

- [cluster-management-project](https://docs.gitlab.com/ee/user/clusters/management_project.html#cluster-management-project)

- [runner 无法拉取 git 目录](https://docs.gitlab.com/runner/executors/kubernetes.html#troubleshooting)

## etcd 不健康

- `docker ps -a -f=name=etcd$` 查看 etcd 状态
- `docker logs etcd` 查看 etcd 的 log

## rke 重装

- 关闭所有 node 上的 docker container

```
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

- 删除`cluster.rkestate`文件和`kube_config_cluster.yml`文件
- 删除每个 control plane node 上的配置文件

```
rm -f /etc/kubernetes/ssl/kube-service-account-token-key.pem
rm -f /etc/kubernetes/ssl/kube-service-account-token.pem
cp /etc/kubernetes/ssl/kube-apiserver-key.pem /etc/kubernetes/ssl/kube-service-account-token-key.pem
cp /etc/kubernetes/ssl/kube-apiserver.pem /etc/kubernetes/ssl/kube-service-account-token.pem
```

- rke up
