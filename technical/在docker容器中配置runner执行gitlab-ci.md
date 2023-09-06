### 在docker容器中配置runner执行gitlab-ci

准备工作：将docker、gitlab-runner安装好。

#### 创建docker容器：

```
docker run -d --name gitlab-runner --restart always \
  --add-host=repo.wolwobiotech.com:172.28.1.3 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /src/gitlab-runner:/etc/gitlab-runner \
  gitlab/gitlab-runner:latest
```

注意：--add-host=repo.wolwobiotech.com:172.28.1.3在我们网络环境下不配置这个在注册时会失败

##### docker-compose.yml创建容器：

在文档中添加一个service；

使用up命令创建容器：

```
docker-compose up gitlab-runner2
```

start命令启动容器：

```
docker-compose start gitlab-runner2
```

#### 注册：

进入docker容器内

```
docker exec -it gitlab-runner bash
```

运行注册命令

```
gitlab-runner register
```



![image-20210806172455775](https://gitee.com/zhao_xuanchao/images/raw/master/img/image-20210806172455775.png)

填写地址、token、描述、标签（可选）、执行器、执行器版本

执行器为docker



然后退出容器

```
exit
```



#### 修改配置文件

在我们的环境下，注册完成了还不能直接用，需要修改下配置。

进入runner配置文件

```
vim /src/gitlab-runner/config.toml
```

由于我们域名的无法直接解析，需要在配置文件中新增一行配置

```
extra_hosts = ["repo.wolwobiotech.com:172.28.1.3"]
dns = ["223.5.5.5","223.6.6.6"]
network_mode = "host"
```

这样runner就配置完成了，可以在仓库的setting -> CI/CD的runner中查看

#### gitlab-ci.yml文件

可以前端用一下文件测试

```
image: node:12

cache:
  key: ${CI_COMMIT_SHA}_node_modules
  untracked: true
  paths:
    - node_modules/
  policy: pull-push


stages:
  - prepare
  - build
  - deploy

install_dependencies:
  stage: prepare
  script:
    - yarn install 

build_code:
  stage: build
  environment: $CI_COMMIT_BRANCH
  script:
    - yarn run build
  artifacts:
    name: "${CI_COMMIT_SHA}_build"
    untracked: true
    paths:
      - build/
    expire_in: 3 hrs

```

注：cache配置是由于第二步依赖第一步的结果，写的缓存文件，具体使用参考官方文档：

https://docs.gitlab.com/ee/ci/caching/



要同时运行多个pipeline要注册多个runner
