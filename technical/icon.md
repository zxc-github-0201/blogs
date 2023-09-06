---
layout: page
title: iconfont使用
parent: 技术文档
---

# iconfont 的使用

- 阿里 iconfont 网站图标的一些使用方法与补充说明

## 1. Unicode 方式

- 用 icon 生成 woff2，woff，ttf 的字体
- 用 font-face 加载字体

  ```css
  @font-face {
    font-family: "iconfont";
    src: url("iconfont.woff2?t=1620624824800") format("woff2"), url("iconfont.woff?t=1620624824800")
        format("woff"), url("iconfont.ttf?t=1620624824800") format("truetype");
  }
  ```

- 自定义 class 使用 iconfont 的样式

  ```css
  .iconfont {
    font-family: "iconfont" !important;
    font-size: 16px;
    font-style: normal;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }
  ```

- 使用

  html 中使用 unicode 编码时，&#表示 10 进制&#x 表示 16 进制，这里的 span 标签可以用别的标签替代

  ```html
  <span class="iconfont">&#x33;</span>
  ```

## 2. Font class 方式

- unicode 方式的变种，使用起来代码可读性好一些
- 直接生成带有 content 的 class 文件

  ```css
  @font-face {
    font-family: "iconfont"; /* Project id 1955018 */
    src: url("iconfont.woff2?t=1620624824800") format("woff2"), url("iconfont.woff?t=1620624824800")
        format("woff"), url("iconfont.ttf?t=1620624824800") format("truetype");
  }

  .iconfont {
    font-family: "iconfont" !important;
    font-size: 16px;
    font-style: normal;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  .iconfh:before {
    content: "\e642";
  }

  .iconsousuo:before {
    content: "\e620";
  }
  ```

- 使用

  ```html
  <span class="iconfont iconxxx" />
  ```

## 3. Symbol 方式

- 生成 icon 加载的 js 文件，代码具体内容是动态加载 svg 到 html 中
- symbol 作用是创建可引用的图形对象，后续通过 use 来实例化

  ```html
  <svg>
    <symbol id="iconfh" viewBox="0 0 1024 1024">
      <path
        d="M714.83 962c-12.535 0-25.07-4.749-34.689-14.247L274.478 547.142c-9.389-9.281-14.681-21.913-14.681-35.124s5.292-25.843 14.681-35.124L680.141 76.258c19.395-19.166 50.662-18.997 69.814 0.434 19.164 19.406 18.973 50.649-0.434 69.814L379.429 512.018l370.092 365.487c19.406 19.165 19.598 50.408 0.434 69.814C740.301 957.106 727.572 962 714.83 962z"
      ></path>
    </symbol>
  </svg>
  ```

- svgfont 全局的 style，1em 宽高让元素大小通过 fontsize 来控制，currentColor 可以让元素颜色通过 color 来控制

  ```html
  <style>
    .svgfont {
      display: inline-block;
      width: 1em;
      height: 1em;
      fill: currentColor;
      vertical-align: -0.1em;
      font-size: 16px;
    }
  </style>
  ```

- 加载到页面中

  ```html
  <script src="./iconfont.js"></script>
  ```

- 使用

  ```html
  <svg class="svgfont" aria-hidden="true">
    <use xlink:href="#icon-xxx"></use>
  </svg>
  ```

## 4. createFromIconfontCN 方式

- 使用@ant-design/icons 库提供的 createFromIconfontCN 函数
- 基本原理和上面相同
- 安装， 就算安装了 antd 也不可省略， vscode 的 ts 代码补全一部分通过 package.json 查找， 不安装会补全不了

  ```bash
  yarn add @ant-design/icons
  ```

- 生成自己的 Icon 组件

  ```typescript
  import { createFromIconfontCN } from "@ant-design/icons";

  export const MyIcon = createFromIconfontCN({
    // 在 iconfont.cn 上生成
    scriptUrl: "//at.alicdn.com/t/font_1295503_cb5tdsw52u5.js",
  });
  ```

- createFromIconfontCN 内部会加载 js，并且挂载 css，生成和上述一致的 react 组件
