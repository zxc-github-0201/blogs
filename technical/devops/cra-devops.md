### 文档简介

该文档记录 create-react-app 应用如何启用 gitlab 的 devops 功能

### gitlab 生成 devops 应用

- 创建 gitlab 项目，通过模板`JodeJs Express`
- 在根目录新建`.gitlab-ci.yml`文件，关闭`Postgresql`等其他参数

  ```yaml
  variables:
    POSTGRES_ENABLED: "false"
    STAGING_ENABLED: "true"
    CODE_QUALITY_DISABLED: "true"
    SAST_DISABLED: "true"
    SECRET_DETECTION_DISABLED: "true"
    TEST_DISABLED: "true"
    AUTO_DEVOPS_DEPLOY_DEBUG: "true"

  include:
    - template: Auto-DevOps.gitlab-ci.yml
    - template: Jobs/Deploy.latest.gitlab-ci.yml
  ```

- 具体参数，详见[devops](https://docs.gitlab.com/ee/topics/autodevops/customize.html#build-and-deployment)
- 引用`Jobs/Deploy.latest.gitlab-ci.yml` 可以避免调用 `bitnami` 的资源，导致网路无法访问，ci 中断

- 在根目录新建 `dockerfile`文件，内容如下
  ```dockerfile
    FROM node:16 as build
    WORKDIR /app
    COPY  package*.json ./
    RUN yarn install --frozen-lockfile
    ENV NODE_ENV=production
    COPY . ./
    RUN yarn build
    FROM nginx:stable-alpine
    COPY --from=build /app/build /usr/share/nginx/html
    COPY nginx/nginx.conf /etc/nginx/conf.d/default.conf
    EXPOSE 5000
    CMD ["nginx", "-g", "daemon off;"]
  ```
- 上传项目，gitlab 会自动在 `ci/cd` 中部署应用

### TODO

- 如何在 CI/CD 期间支持`.env`的配置
- 如何在项目运行期间支持`.env`的配置
- 如何自己编写`dotenv`实现
