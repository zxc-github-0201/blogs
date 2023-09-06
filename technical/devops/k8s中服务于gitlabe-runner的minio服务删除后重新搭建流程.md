### k8s中服务于gitlabe runner的minio服务删除后重新搭建流程

1. 首先确认旧的minio chart已经卸载

   ```powershell
   # 查看命名空间下helm列表
   $ helm list -n devops
   # 卸载旧的minio chart
   $ helm uninstall minio
   # 卸载后可查看是否有遗留资源,如果有手动删除
   $ kubectl get all -n devops -l release=minio
   $ kubectl delete <类型> <名称> -n devops
   
   ```

2. 为新的minio创建新的pv及pvc

   ```powershell
   # 新建一个pvc 文件可参考gitlab中cluster-management项目中devops/minio/minio-pvc.yaml文件
   $ kubectl apply -f <文件路径> -n devops
   ```

   创建后，storageclass会自动为服务创建pv并绑定

3. 创建新的minio pod

   secret可参考[minio-helm](https://github.com/minio/minio/tree/master/helm/minio)文档，配置文件如gitlab中cluster-management项目中devops/minio/values.yaml文件

   ```powershell
   # 安装新的minio chart，
   $ helm install minio --set persistence.existingClaim=<上一步中创建的pvc名称> --set replicas=0 --set existingSecret=<创建的secret>  -f <配置文件路径，在这里主要给minio配置web页面> minio/minio -n devops
   ```

4. 通过上述配置文件成功后可查看minio-web页面，如上地址为[k8s-minio-web.wolwobiotech.com](k8s-minio-web.wolwobiotech.com)，登陆后新建bucket，可使用我们runner已经配置好的“runners-cache”，然后在左侧"User => Access Keys"中申请access key，建议直接设置为gitlab中cluster-management项目中devops/runner/runner-value.yaml文件中的access key及secret key，这样不需要重启runner服务