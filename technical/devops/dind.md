### 什么是 dind

`dind`是`docker in docker`的简写，意为在 docker 容器中运行 docker 命令，对应了一组特定的`docker`的`docker`镜像。在`docker hub`上这些镜像的 tag 都带有`dind`。

### 为什么使用 dind

一般使用在 ci 上，例如以`docker`的方式启动了`jenkins`或者`gitlab-runner`等容器，但是在容器的流水线中需要使用`docker`命令，如用`docker build`打包镜像，此时需要一个跑在`docker`中的`docker daemon`
以下会使用`gitlab-runner`举例。

### 关于 dind 的几种情况

## 不使用 tls

## 使用 tls

## 替代方案

可以在`gitlab-runner`容器启动时添加参数`-v /var/run/docker.sock:/var/run/docker.sock`，由于`docker`的`client`会通过`docker.sock`文件通过`unix`域套接字和`docker daemon`通过类似`RESTFUL API`进行通信。此时容器内部的`docker client`相当于直接是用了主机的`docker daemon`。

注意：此时会使用主机的`resolv.conf`和`hosts`文件来解析地址
