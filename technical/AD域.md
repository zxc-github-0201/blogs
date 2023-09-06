---
layout: page
title: AD域
parent: 技术文档
---

### AD 域作用

- AD 域全称 Active Directory，用来提供统一的对象管理服务

### LDAP

- LDAP(Lightweight Directory Access Protocol)，轻量目录访问协议
- DN 全称 distinguish name，分为 DC(Domain Component)域名组件、OU（Organization Unit）组织单位，CN (Common Name)通用名称
- 例如，wolwo 公司，行政部，信息组的张三，则 DC=wolwo，DC=com，OU=行政部，OU=信息组，CN=张三，访问路径为 wolwo.com/行政部/信息组/张三

### LDAPJS

- [文档](http://ldapjs.org/client.html#search)
- client Search 代码， `必须分页，不分页无法搜索，文档没写明这一点`

```javascript
const { createClient } = require("ldapjs");

const bind = (client, dn, secret) =>
  new Promise((resolve, reject) => {
    client.bind(dn, secret, (err) => {
      if (err) {
        return reject(err);
      }
      return resolve();
    });
  });

const unbind = (client) =>
  new Promise((resolve, reject) => {
    client.unbind((err) => {
      if (err) {
        return reject(err);
      }
      return resolve();
    });
  });

const search = (client, base, opts, debug = false) =>
  new Promise((resolve, reject) => {
    const logger = (...args) => debug && console.log(...args);
    const items = [];
    client.search(base, opts, (err, res) => {
      if (err) {
        return reject(err);
      }
      res.on("error", (err) => {
        logger("error-------------------");
        logger(err);
        logger("error-------------------");
        reject(err);
      });
      res.on("searchEntry", (entry) => {
        logger("searchEntry-------------------");
        logger(JSON.stringify(entry.object));
        items.push(entry.object);
        logger("searchEntry-------------------");
      });
      res.on("searchReference", (referral) => {
        logger("searchReference-------------------");
        logger(referral);
        logger("searchReference-------------------");
      });
      res.on("searchRequest", (searchRequest) => {
        logger("searchRequest-------------------");
        logger(searchRequest);
        logger("searchRequest-------------------");
      });
      res.on("end", (result) => {
        logger("end-------------------");
        logger(result);
        logger("end-------------------");
        resolve(items);
      });
    });
  });

async function main() {
  const client = createClient({
    url: ["ldap://10.0.1.20:389"],
  });
  try {
    client.on("error", (err) => {
      console.log(err);
    });
    await bind(client, "cn=test,cn=Users,dc=wolwobiotech,dc=com", "123456");
    const searchResult = await search(client, "dc=wolwobiotech,dc=com", {
      // filter: "(CN=XXX)",
      scope: "sub",
      filter: "(sAMAccountName=*)",
      paged: true,
      sizeLimit: 200,
    });
    console.log(searchResult);
    await unbind(client);
  } catch (error) {
    console.log(error);
    await unbind(client);
  }
}

main();
```

- 关于[scope 解释](https://ldapwiki.com/wiki/LDAP%20Search%20Scopes)

### 登录方案

- 登录方案一

  ```mermaid
  sequenceDiagram
    participant 前端
    participant 后端
    participant LDAP服务器
    前端->>LDAP服务器: 登录用户DN信息
    LDAP服务器->>前端: GUID
    前端->>后端: GUID
    后端->>前端: token
  ```

- 登录方案二

  ```mermaid
  sequenceDiagram
    participant 前端
    participant 后端
    participant LDAP服务器
    前端->>后端: 登录用户DN信息
    后端->>LDAP服务器: 登录用户DN信息
    LDAP服务器->>后端: GUID
    后端->>前端: token
  ```
