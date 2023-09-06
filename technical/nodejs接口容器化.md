### 把一个 Node.js web 应用程序给 Docker 化

- 参考网址 https://nodejs.org/zh-cn/docs/guides/nodejs-docker-webapp/

- 创建一个空文件，命名为 Dockerfile 新建文件 名字为 Dockerfile 无需扩展类型
- FROM node:12
  WORKDIR /usr/src/app
  <!-- copy你的 package.json 所在地  -->

  COPY package\*.json ./

  RUN npm install

  COPY . .
  EXPOSE 8080
  <!-- 该项目命令 -->

  CMD [ "node", "app .js" ]

- 在 Dockerfile 的同一个文件夹中创建一个 .dockerignore 文件，带有以下内容：
  node_modules
  npm-debug.log
- 运行 docker build . -t <your username>/node-web-app
- docker images 查看
- CI :
    <!--  -->
  variables:
  DOCKER_HOST: tcp://docker:2375
  DOCKER_TLS_CERTDIR: ''

include:

- project: 'frontend/npm-registry'
  file: '/templates/ci-scripts.yml'

services:

- docker:19.03.12-dind

stages:

- build

build:
image:
docker:19.03.12
stage:
build
extends:

- .docker_registry
  script:

- docker build -t $CI_REGISTRY/$CI_PROJECT_NAMESPACE/npm-registry/$CI_PROJECT_NAME:$CI_COMMIT_SHORT_SHA .
- docker push $CI_REGISTRY/$CI_PROJECT_NAMESPACE/npm-registry/$CI_PROJECT_NAME:$CI_COMMIT_SHORT_SHA
- docker tag $CI_REGISTRY/$CI_PROJECT_NAMESPACE/npm-registry/$CI_PROJECT_NAME:$CI_COMMIT_SHORT_SHA $CI_REGISTRY/$CI_PROJECT_NAMESPACE/npm-registry/$CI_PROJECT_NAME:latest
- docker push $CI_REGISTRY/$CI_PROJECT_NAMESPACE/npm-registry/$CI_PROJECT_NAME:latest

<!--  -->

- 完成上述 之后
- yarn version
- git push --tags
- git push
