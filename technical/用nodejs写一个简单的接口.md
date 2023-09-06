### 用 nodejs 写一个简单的接口

- 新建一个文件夹，切换至该文件夹
- npm init
- 新建 app.js 文件
- 如果使用 sqlserver 与 express
- 安装依赖 yarn add mssql ;
  yarn add express
  yarn add body-parser
- app.js 文件中写如下代码
  <!--   导入express -->

  const express = require('express');
    <!-- bodyParser  当前端正确传了数据但是后端ctx.body的数据为空时  使用这个 -->

  const bodyParser = require('body-parser');
  const app = express();
  <!-- 解决跨域 -->

  const cors = require('cors');
  app.use(bodyParser.urlencoded({ extended: false }));
  app.use(bodyParser.json());
  app.use(cors());
  app.use(express.urlencoded({ extended: false }));
  <!-- 配置接口路由 -->

  const userRouter = require('./router/user');
  app.use('/api', userRouter);
  <!-- 监听 -->

  app.listen(3007, () => {
  console.log(`运行在这个服务器上 http://127.0.0.1:3007`);
  });

- 新建其他文件夹 写代码 参考http://repo.wolwobiotech.com/frontend/fish
