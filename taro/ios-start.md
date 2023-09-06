### ios 对象关系图

```mermaid
graph TD;
  UIKit --> UIApplicationMain --> UIApplication --> willFinishLaunchingWithOptions --> didFinishLaunchingWithOptions
  UIApplicationMain --> UIApplicationDelegate
```

### ios 对象基本解释

- UIKit，用于 iOS 或者 tvOS 应用程序构建与管理图形化、事件驱动的用户界面框架。
- UIApplicationDelegate，协议，用于管理 App 的共享行为，App 的根对象。
- UIApplication，ios 中运行应用程序的几种控制和协调点。
- UIApplicationMain，ios 应用启动时，调用的第一个函数，会创建单例的 UIApplication 对象。

### delegate 机制

在 ios 中 delegate 相当于委托人，如初始类的 AppDelegate ，实现了 UIApplicationDelegate 方法，当中的 application:didFinishLaunchingWithOptions 方法，会在 UIKit 创建完成 UIApplication 后 回调触发。具体的类图如下:

```mermaid
classDiagram

class Delegate {
  +Processer processer
  +callback(args) void
}

class Processer {
  +start(args) int
}

Delegate o-- Processer
```

### react-native 对接 企业微信

```mermaid
sequenceDiagram
  User->>App: 点击第三方登录
  App->>App: WWKApi.sendReq
  App->>iOS: UIApplication.shared.open(WeWorkUrl)
  iOS->>WeWork: 切换前台应用
  User->>WeWork: 点击登录
  WeWork->>iOS: UIApplication.shared.open(App)
  iOS->>App: 切换前台应用
  App->>App: Notification收到"RCTOpenURLNotification"
  App->>App: App.handleOpenURL
  App->>App: App.onResp
```

### react-native-wechat ios 逻辑

```mermaid
sequenceDiagram
  participant Rnw as react-native-wechat-ios
  App->>Js: nativeShareToTimeLine
  Js->>Rnw: shareToTimeLine
  Rnw->>Rnw: shareToWeixinWithData
  Rnw->>Rnw: shareToWeixinWithMediaMessage
  Rnw->>Js: callBack
  WeWork->>Rnw: onResp
  Rnw->>Js: self.bridge.eventDispatcher
  Js->>App: emitter.once
```
