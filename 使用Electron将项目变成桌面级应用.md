## 将react项目打包成桌面应用

#### 使用Electron将项目变成桌面级应用

1. 安装electron依赖包

   ```
   yarn add electron
   ```

   安装失败就安装指定版本的，如

   ```
   yarn add electron@6.0.1
   ```

   安装出现An unexpected error occurred: "expected workspace package to exist for \"table\""这个错误不清楚，使用npm可以安装

   ```
   npm install electron@6.0.1
   ```

2. 在根目录下(与src同级)创建一个main.js文件作为入口文件，这个文件控制主进程，运行在一个完整的Node.js环境中，负责控制应用生命周期，显示原生界面，执行特殊操作并管理渲染器进程。

   ![image-20210714125410929](https://gitee.com/zhao_xuanchao/images/raw/master/img/image-20210714125410929.png)

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

   就可以启动了。

   

#### 将electron打包成exe文件

官网推荐有三种方式：electron-forge、electron-builder、electron-react-boilerplate

具体看看官网介绍。https://www.electronjs.org/docs/tutorial/boilerplates-and-clis

##### electron-forge打包

1. 安装electron-forge脚手架

   ```
   yarn add --dev @electron-forge/cli
   ```

2. 使用import命令设置Forge的脚手架

   ```
   yarn electron-forge import
   ```

3. 使用make命令创建应用程序

   ```
   yarn make
   ```

完成后会创建一个out文件夹，exe文件在win32-x64文件夹下，make文件夹下有setUp安装包。

注意事项：

1.在执行make命令前要确保package.json文件下有author和description，这是make的必填项，不然会报错。

```
{
  "name": "msonline",
  "version": "0.1.0",
  "private": true,
  "author": "zxc",		//必填
  "description": "test01",	//必填
  "main": "main.js",
  "dependencies": {
    ...
  },
  ...
}
```

2.yarn命令添加后不能用npx来设置import命令设置脚手架，会报错

3.设置导出的exe文件图标和名称在package.json里config中的forge

```
"config": {
    "forge": {
      "packagerConfig": {
        "name":"deskApp",	
        "icon":"src/pokeball.ico"
      }
    }
  }
```

icon要为.ico格式图片，不然会设置失败



这种方式创建的setUp文件安装时是没有界面的，无法自定义安装路径。



##### electron-builder打包

1. 安装electron-builder

   安装的默认版本要node版本14.0.0以上；我们当前使用的为12.x.x，可以安装22.11.2的版本

   ```
   yarn add electron-builder@22.11.2
   ```

   这样就可以安装成功了。

   注：这个版本可以正常使用，更早的版本有的安装后会无法执行打包命令。如果失败可以尝试npm安装

2. 配置启动条件

   package.json中author和description是必须填写的

   ```
   {
     "name": "msonline",
     "version": "0.1.0",
     "private": true,
     "author": "zxc",		//必填
     "description": "test01",	//必填
     "main": "main.js",
     "dependencies": {
       ...
     },
     ...
   }
   ```

   在package.json中配置打包命令。

   ```
   "scripts": {
       "pack": "electron-builder --dir",		//新增
       "dist": "electron-builder",				//新增
       "start": "react-scripts start",
       "build": "react-scripts build",
       "test": "react-scripts test",
       "eject": "react-scripts eject",
       "electron": "electron ."
     },
   ```

   pack命令只会在项目目录下生成一个文件的压缩包，不会进行打包。

   dist命令是打包命令。

   

   上述版本安装成功后在package.json中会出现devDependencies对象，里面有electron-builder的版本，将dependencies中的electron也移到devDependencies对象下；

   ```
   "devDependencies": {
       "electron":"6.0.1",
       "electron-builder": "^22.11.2"
     },
   ```

   不然执行命令会报错。

   

   build对象为打包的配置目录；

   详细参数参考官方文档：https://www.electron.build/configuration/configuration

   ```
   "build":{
       "productName": "todoAPP",		//APP名称
       "appId": "com.xxx.xxxxx",		//appId
       "copyright": "xxxx",
       "asar":true,
       "afterPack":"afterPack.js",		//打包后执行的文件，项目根目录下，和main.js同级
       "directories":{					//构建资源路径，默认为dist目录下
           "buildResources":"build",
           "output":"dist"				//输出位置
       },
       "win":{							//windows相关选项
           "target": [{
               "target":"nsis"
           }],
           "icon":"build/favicon.ico",	//图标
           "asarUnpack":[				//不会压缩进app.asar的资源
               "src/**",
               "README.md"
           ]
       },
       "nsis":{						nsis选项
           "oneClick":false,			//创建一键安装程序还是辅助
           "allowElevation":true,		//是否需要更高权限
           "allowToChangeInstallationDirectory":true,	//是否允许用户自定义安装位置
           "installerIcon":"build/install_favicon.ico",//安装程序图标
           "uninstallerIcon":"build/uninstall_favicon.ico",//卸载程序图标
           "createDesktopShortcut": true,	//是否创建桌面快捷方式
           "createStartMenuShortcut": true	//是否创建开始菜单快捷方式
       }
   }
   ```

   

3. 打包

   在项目已启动的情况下执行dist命令进行打包

   ```
   yarn start
   yarn dist
   ```

   可能遇到的错误：

   1.“ Application entry file "build\electron.js" in the "D:\app\demo\todoapp\dist\win-unpacked\resources\app.asar" does not exist. Seems like a wrong configuration.”

   这个错误是因为程序入口被修改，在package.json中的build下extends改为null

   ```
   "build": {
       "productName": "todoAPP",	
       "appId": "com.xxx.xxxxx",	
       "copyright": "xxxx",
       "win": {
         "target": [
           "nsis",
           "zip"
         ]
       },
       "extends":null		//新增
     }
   ```

   2.“Get "https://github.com/electron-userland/electron-builder-binaries/releases/download/nsis-3.0.4.1/nsis-3.0.4.1.7z": read tcp 10.1.2.220:59372->52.74.223.119:443: wsarecv: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond.”

   这样的错误可能是网络问题，要保证网络的前提下删掉当前dist文件夹重新打包。
   
   3.打包后安装到其他电脑白屏。
   
   这是因为打包地址为本地的localhost，只需要在main.js中将地址改为对应的线上地址再打包就可以了



##### 使用NullsoftInstall软件打包

需要基于electron-builder打包出的文件进行打包，具体步骤参考

https://www.jianshu.com/p/1dbb96bc8f37





推荐使用electron-builder打包：可设置选项较多，如可选安装位置；打包快。



#### 打包中遇到的问题

##### 1.main.js中配置的loadURL地址问题

可以配置为url地址：如

```
mainWindow.loadURL("http://localhost:3001")
```

也可配置为Build后的html页面，将项目build后的文件，如：

```
mainWindow.loadURL(
    url.format({
      protocol: "file:",
      slashes: true,
      pathname: path.join(__dirname, "./build/index.html"),
    })
  );
```

为url地址打包后的应用内容会随着地址内容改变而改变，若为build后的html文件则需要更新后才能随着更新。

##### 2.build后打开安装后的应用出现Not allowed to load local resource错误问题

该问题可能为需要的build文件未打包进app.asar文件中，在package.json文件中添加对应配置：

```
build:{
	"files":[
		"build/**/*",
		"main.js",
		"src/preload.js"
	]
}
```

##### 3.自动更新问题

在本地使用nodejs+express当作更新服务器测试

注意本地设置的当作静态服务的地址，如：

```
app.use(express.static(path.join(__dirname, "./public")));
```

该命令将当前服务目录下public文件夹设置为存放地址，

```
autoUpdater.setFeedURL(http://10.1.2.220:8000);
```

直接监听该端口号即可

##### 4.在App.js中添加自动更新监听是ipcRenderer无法使用问题

在src目录下新建一个preload.js文件，

```
global.electron = require("electron");
global.ipcRenderer = require("electron").ipcRenderer;
global.electornUpdater = require("electron-updater");
global.isElectron = require("is-electron");
```

在main.js中将该文件添加为预加载文件

```
mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    // 预加载内容
    webPreferences: {
      preload: path.join(__dirname, "./src/preload.js"),
    },
  });
```

引用时：

```
const ipcRenderer = window.ipcRenderer;
```

##### 5.js,css文件找不到

查看文件地址，如果地址不正确在package.json中添加homepage参数

```
"homepage": "./",
```

