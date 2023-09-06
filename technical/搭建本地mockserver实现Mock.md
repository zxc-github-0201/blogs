### 搭建本地 mock server 实现 Mock

在后端接口未完成时，可本地搭建 Mock 服务，返回与后端沟通过的数据格式。

插件：mockjs 或 better-mock、express、express-http-proxy

1.安装上述插件；

2.  在项目根目录下新建 mock 文件夹；

    mock 文件夹下新建 index.js(用来生成本地服务)和 utils.js(用来导出添加请求头等方法)文件；

    mock 文件夹下新建 src 文件夹(此目录下为需要 mock 的接口导出文件)；

    3.根据需要在 utils.js 中导出需要的方法

```
// utils.js
/* eslint-disable import/no-commonjs */
const fs = require("fs");
const _ = require("lodash");
const path = require("path");

const CWD = process.cwd();
// mock接口返回数据格式
const wrapWithCode = res => ({
  code: 200,
  message: "成功",
  result: res
});
// 添加请求头
const addCORSHeader = res => {
  res.append("Access-Control-Allow-Origin", "*");
  res.append("Access-Control-Allow-Headers", "*");
  res.append("Access-Control-Allow-Methods", "PUT,POST,GET,DELETE,OPTIONS");
  // res.append(
  //   "Authorization",
  //   " Bearer eyJhbGciOiJIUzUxMiJ9.eyJjcmVhdGVkIjoxNjQxNDUxMzIwMTk2LCJleHAiOjE2NDIwNTYxMjAsImFjY291bnQiOiIzNDAxIn0.LEerxRMz2ayy13HvlNXlYHRf1SB8gpN6jCRr1UUeIhDCJibuKFfS1_6IvLUhH4uH8KjtNee8V7wR7m8YUmMd6A"
  // );
  res.header("Access-Control-Allow-Credentials", "true");
};

const addJsonHeader = res => {
  res.append("Content-Type", "application/json;charset=utf-8");
};
// 获取需要mock接口路径
const getRouteItems = () => {
  // join第二个参数为根文件夹名称，此处为mock;第三个参数为mock文件夹下文件目录名称，此处为src
  const ROUTE_PATH = path.join(CWD, "mock", "src");
  const routeItems = [];
  for (const routeFile of fs.readdirSync(ROUTE_PATH)) {
    const routeMap = require(path.join(ROUTE_PATH, routeFile));
    _.forEach(routeMap, (value, key) => {
      const [method, apiPath] = key.split(" ");
      routeItems.push({
        method: method.toLowerCase(),
        path: apiPath,
        callBack:
          typeof value === "function"
            ? value
            : (req, res) => {
                res.json(wrapWithCode(value));
              }
      });
    });
  }

  // eslint-disable-next-line no-shadow
  const count = path => path.split("").filter(c => c === ":").length;
  /**
   * 带参路由的路由最后注册
   */
  routeItems.sort((a, b) => count(a.path) - count(b.path));

  return routeItems;
};

module.exports = {
  wrapWithCode,
  addCORSHeader,
  addJsonHeader,
  getRouteItems
};

```

4.在 index.js 中配置本地 server 文件

```
// index.js
/* eslint-disable import/no-commonjs */
const express = require("express");
const proxy = require("express-http-proxy");
const _ = require("lodash");

const url = "https://tools.wolwobiotech.com"; // 后端接口地址只能为域名，后面不能加/..

const { addCORSHeader, addJsonHeader, getRouteItems } = require("./utils");

const app = express();

// 添加请求头
app.use((req, res, next) => {
  addCORSHeader(res);
  addJsonHeader(res);
  next();
});

app.options("*", function(req, res) {
  console.log("mock options", req.originalUrl);
  res.send({ msg: "mock-server：模拟数据" });
});

// app.use((req, res, next) => {
//   console.log(req.originalUrl);
//   next();
// });
// 将src目录下所有的mock接口文件注册
_.forEach(getRouteItems(), ({ method, path, callBack }) => {
  console.log(`注册${path}`);
  app[method](path, callBack);
});

// 接口请求地址为本地server地址，再将所有接口请求代理转发
app.use(
  "/",
  proxy(url, {
    proxyReqOptDecorator: (proxyReqOpts, originalReq) => {
      // 打印出 正在代理转发的请求
      console.log("proxy ", proxyReqOpts, originalReq.originalUrl);
      return proxyReqOpts;
    }
  })
);
本地server设置为8080端口
app.listen("8080", () =>
  console.log("mock server is running at http://localhost:8080/")
);
```

5.在 src 下导出本地 mock 的接口

如 src 下创建 api.js 文件

```
/* eslint-disable import/no-commonjs */
const Mock = require("better-mock");
const { wrapWithCode } = require("../utils");

module.exports = {
  "GET /no-entry-staff/getTechnicalTitle": (req, res) => {
    res.json(
      wrapWithCode(
        Mock.mock({
          data: [
            {
              technicalTitleName: "建造师er级",
              profession: "建造师",
              level: "二级",
              obtainTime: 1631232000000,
              company: "我武生物"
            }
          ]
        }).data
      )
    );
  }
};

```

这里只导出了一个返回固定数据的接口，其他接口请求将重定向到后端地址进行正常的接口请求，也可以根据 mockjs 文档的语法规范返回随机数据。

6.在 package.json 的 scripts 中添加 mock 命令

```
// package.json
// nodemon插件是用来改变src下Mock接口时自动重启
"mock": "nodemon ./mock"
```

7.启动本地 server 后将后端请求地址设置为本地 server
