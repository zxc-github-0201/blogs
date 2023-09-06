### 记修复服务器重启后导致的k8s中minio及jenkins服务失败问题

#### 产生原因：

服务器重启后，k8s中的jenkins及minio服务在重启时所用到的pv挂载失败，导致服务无法启动。

#### 排查过程：

首先了解创建pvc的流程（[概念可参考文档中k8s内容](https://doc.wolwobiotech.com/#/frontEnd/technical/devops/k8s)）：

![persistent_process](https://raw.githubusercontent.com/honins/imgbed/master/img/persistent_process.png)

其中pv的创建流程如下：

![kube_csi_create_pv](https://raw.githubusercontent.com/honins/imgbed/master/img/kube_csi_create_pv.jpg)



经过排查，本次服务失败是由于pv创建后csi创建云盘失败导致，原因是csi container节点重启后由于node节点网络问题拉取镜像失败。

#### 解决方式：

找到错误的k8s node节点，为docker设置proxy，修复网络问题导致的docker镜像下载失败问题

```
# 文件 /usr/lib/systemd/system/docker.service
[Service]
Environment="HTTP_PROXY=YOUT_PROXY"
Environment="HTTPS_PROXY=YOUT_PROXY"
```

本次通过修改地址为我的主机端口：10.1.2.220:7890

修改后重启

```bash
$ systemctl daemon-reload
$ systemctl restart docker
```

