### 使用jenkins、semantic-release实现自动打tag

1.https://k8s-jenkins.wolwobiotech.com/job/frontend/

在该目录下新建pipline；配置参考wolwo项目；

2.在gitlab项目仓库中配置webhooks;

地址为https://k8s-jenkins.wolwobiotech.com/project/frontend/项目名称；

token在jenkins创建项目中 advance中点击generate生成；

Trigger选择第一项push events ,填写正式分支如：production;

选中Enable SSL verification；

3.在项目中配置.releaserc文件及jenkinsfile文件；

参考wolwo项目