### 记K8S证书过期导致CI报错问题

#### 产生原因：

k8s证书过期，集群无法使用

#### 解决方式：

1. 确认证书是否过期

   ```
   ## 登录vip服务器（172.29.1.100）
   cd /etc/kubernetes/pki && openssl x509 -in apiserver.crt -text -noout
   kubeadm certs check-expiration
   ```

   ![image-20230824164600486](https://raw.githubusercontent.com/honins/imgbed/master/img/image-20230824164600486.png)

2. 更新证书

   kubeadm certs renew --help   可以查看用法

   ```
   kubeadm certs renew all
   ```

   更新后确认证书是否更新，确认更新后重启Kubelet

   ```
   systemctl restart kubelet
   ```

   ![image-20230824164701852](https://raw.githubusercontent.com/honins/imgbed/master/img/image-20230824164701852.png)

   在其他master节点中重复上述操作，我们的k8s集群共dev5（172.29.1.200）、dev6（172.29.1.41）、dev7（172.29.1.42）三个master节点

3. 更新配置文件

   更新证书会更新admin.conf文件，将admin.conf文件中users下凭据更新到本地.kube/config中，更新后完成。

