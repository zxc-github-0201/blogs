---
layout: page
title: puppeteer
parent: 技术文档
---

### 介绍

- puppeteer 是一个 Node 的库，通过[DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/)提供抽象的 API 用以操作 Chromium 或者 chrome 内核的浏览器。

### 作用

- 生成页面截图或者 PDF
- 爬取 SPA 应用或者 SSR 应用
- 自动化表单提交，UI 测试，键盘输入等
- 提供现代化自动测试环境，通过最新版的 JS 和浏览器特性，在最新版的 Chrome 中直接运行测试代码
- 捕获网站时间线的追踪分析数据来诊断性能问题
- 测试 Chrome 拓展

### 其他相关文旦

- [性能测试相关](https://addyosmani.com/blog/puppeteer-recipes/#devtools-profile)
- [API 文档](https://github.com/puppeteer/puppeteer/blob/v10.2.0/docs/api.md)
- [awesome-puppeteer](https://github.com/transitive-bullshit/awesome-puppeteer/blob/master/readme.zh.md)
- [性能指标](https://hondrytravis.com/docs/performance/page_performance_index/#lcp%E3%80%81fid%E3%80%81tbt%E3%80%81cls)

### 爬虫方式

- 通过列举各个页面地址直接进入，前置条件：每个页面都需要满足能直接通过连接访问，部分页面暂时没有做到
- 通过给按钮加上特殊的 html 属性生成爬取路径，前置条件：统一项目中的按钮基础组件
- 通过 jest，写在各个页面的`_test`文件夹下，前置条件：进一步整合项目路径

### 目前优化方案

-
