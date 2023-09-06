### 文档简述

该文档记录 `minio` 节点来做 `gitlab-runner` 的缓存`s3`服务

### minio 安装方式

- 安装方式区别

|   方式   |                    备注                    |
| :------: | :----------------------------------------: |
|  docker  |            单节点方式，无法拓展            |
|   helm   |     自动化比较弱，能满足日常需求，灵活     |
| operator | 自动化最完全的方式，上手成本较高，不太灵活 |

- 目前采用`helm`方式安装

### 安装 minio 并连接 runner

- 创建`pv`和`pvc`，下面的参数需要自行替换，或者打包成 helm，用 value 部署

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: {{ .Values.pv.name }}
spec:
  capacity:
    storage: {{ .Values.minio.persistence.size }}
  volumeMode: Filesystem
  accessModes:
  - {{ .Values.minio.persistence.accessMode }}
  persistentVolumeReclaimPolicy: Delete
  storageClassName: {{ .Values.minio.persistence.storageClass }}
  local:
    path: {{ .Values.pv.hostPath }}
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - {{ .Values.pv.hostName }}
------------
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: {{ .Values.minio.persistence.storageClass }}
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
```

- 安装 [minio]详见[安装](https://github.com/minio/minio/tree/master/helm/minio)

- 配置 minio 的 `ingress`，让 `minio` 可以被外网访问, 在 minio 的 web 界面申请`AccessKey`和`SecretKey`

- 在 runner 的配置中加入

```yaml
gitlabUrl: http://repo.wolwobiotech.com

runners:
  secret: gitlab-runner-secret
  privileged: true
  runUntagged: true
  name: "k8s-runner"
  tags: "k8s,frontend"
  locked: false
  config: |
    [[runners]]
          [runners.kubernetes]
            namespace = "{{.Release.Namespace}}"
            image = "node"
            privileged = true
            extra_hosts = ["repo.wolwobiotech.com:172.28.1.3", "maven.wolwobiotech.com:172.28.1.3", "registry.wolwobiotech.com:172.28.1.3", "k8s-minio-api.wolwobiotech.com:172.28.1.3"]
          [runners.cache]
            Type = "s3"
            Path = "cache"
            Shared = true
            [runners.cache.s3]
              ServerAddress = <minio-server-address>
              AccessKey = <access-key>
              SecretKey = <secret-key>
              BucketName = "runners-cache"
              BucketLocation = "wolwo-minio"
```

### 相关文档

- [install-runner](https://docs.gitlab.com/runner/install/kubernetes.html)
- [runner-config](https://docs.gitlab.com/runner/executors/kubernetes.html)
- [minio-helm](https://docs.min.io/docs/deploy-minio-on-kubernetes.html)
