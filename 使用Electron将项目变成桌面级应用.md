#### 使用Electron将项目变成桌面级应用

1. 安装electron依赖包

   ```
   yarn add electron
   ```

   

2. 在根目录下(与src同级)创建一个main.js文件作为入口文件，这个文件控制主进程，运行在一个完整的Node.js环境中，负责控制应用生命周期，显示原生界面，执行特殊操作并管理渲染器进程。

   ![image-20210714125410929](C:\Users\VDI\AppData\Roaming\Typora\typora-user-images\image-20210714125410929.png)

3. 设置main.js文件

   ```
   // Modules to control application life and create native browser window
   const {app, BrowserWindow} = require('electron')
   const path = require('path')
   
   // 打开窗口方法
   function createWindow () {
     // 控制窗口大小
     const mainWindow = new BrowserWindow({
       width: 800,
       height: 600,
       // 预加载内容
       webPreferences: {
         preload: path.join(__dirname, 'preload.js')
       }
     })
   
     // 加载应用----react 打包
     // mainWindow.loadURL(url.format({
     //   pathname: path.join(__dirname, './build/index.html'),
     //   protocol: 'file:',
     //   slashes: true
     // }))
   
     // 开发应用项目启动是从localhost:3000端口创建
     mainWindow.loadURL('http://localhost:3000/')
     // 打开控制台
     // mainWindow.webContents.openDevTools()
   }
   
   // MAC系统下没有窗口时不会退出，在没有窗口时打开新窗口
   app.whenReady().then(() => {
     createWindow()
     
     app.on('activate', function () {
       // On macOS it's common to re-create a window in the app when the
       // dock icon is clicked and there are no other windows open.
       if (BrowserWindow.getAllWindows().length === 0) createWindow()
     })
   })
   
   // 关闭窗口时完全退出应用
   app.on('window-all-closed', function () {
     if (process.platform !== 'darwin') app.quit()
   })
   ```

   可以自定义窗口大小以及从启动的本地端口创建或者从html文件创建

4. 设置package.json文件

   设置好main.js后在package.json中设置它为启动入口，设置main为main.js

   ```
   {
     "name": "msonline",
     "version": "0.1.0",
     "private": true,
     "author": "zxc",
     "description": "test01",
     "main": "main.js",	//添加
     "dependencies": {
       ...
     }
   ```

   设置electron的启动

   ```
   "scripts": {
       "start": "electron-forge start",
       "build": "react-scripts build",
       "test": "react-scripts test",
       "eject": "react-scripts eject",
       "electron": "electron ."		//添加
     },
   ```

5. 启动应用

   设置好后可以启动应用查看了

   如果是html文件加载的话可以直接启动，从localhost:3000启动的话先启动本地端口

   ```
   yarn electron
   ```

   就可以启动了

