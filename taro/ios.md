### 管理应用的生命周期

[原文](https://developer.apple.com/documentation/uikit/app_and_environment/managing_your_app_s_life_cycle?language=objc)

在应用处于前台或者后台的时候，响应系统的推送，处理其他与系统相关的事件。

## 概览

当前的应用状态决定了它可以做什么与不能做什么。例如，一个前台的应用拥有用户的注意，所以他有系统资源，包括 cpu 在内，的高优先级。相反，后台的应用总是尽可能少的工作，尽量无操作，因为他是不再屏幕中的。当你的应用从一种转台切换到另外一种状态时，开发者必须调整相应的调整程序的行为。

当应用转台改变，UIKit 会通知开发者，通过调用 appropriate delegate 对象:

- ios 13 以及之后，使用 UISceneDelegate 对象在基于场景的应用中响应生命周期事件。
- ios 12 以及之前，使用 UIApplicationDelegate 对象回应生命周期事件。

## 响应基于场景的生命周期事件

如果开发者的应用支持场景，UIKit 会为每个场景提供单独的生命周期事件。场景代表了开发者应用界面运行在设备上的实例。用户可以为一个应用创建多个场景，单独的展示或者隐藏他们。应为每个场景有其独立的生命周期，每个都可以在运行时有不同的状态。例如，一个场景可能在前台，其他在后台，或者挂起。

# 重要

场景支持是一个选择性的功能。通过添加`UIApplication SceneManifest`键到应用的`Info.plist`中来添加支持，详细描述见[Specifying the Scenes Your App Supports](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/specifying_the_scenes_your_app_supports?language=objc)

下图展示了应用的场景转换。当用户或者系统获取了一个应用的新场景时，UIKit 创建并将它添加到独立的状态中。用户请求的场景快速切换到前台，从他们消失时候的屏幕开始。一个几桶请求场景，技术上来说会移动到后台，以便于它可以处理事件。举例来说，系统可能调用后台的场景到前台，这样他就可以处理事件。例如，系统可以在后台启动场景来处理一个位置信息。当用户关闭了一个应用界面的时候，UIKit 将会移动相关的场景到后台并且最终处于暂停状态。UIKit 在任何时候都可以取消一个后台或者挂起的场景来释放他们的资源，返回资源给独立的场景。

![scene-state](/src/image/scene-state@2x.png)

使用场景转换来履行如下的任务：

- 当 UIKit 连接到应用的场景时，配置应用的初始状态，并且读取场景需要的数据
- 当转换到前台活动状态时，配置应用的界面，并准备和用户相互作用。详情见 [Preparing Your UI to Run in the Foreground](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_foreground?language=objc)
- 根据离开前台时候的状态，存储数据，结束应用行为。详情见[Preparing Your UI to Run in the Background](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background?language=objc)
- 通过进入后台状态，完成关键任务，释放尽可能多的内存空间，准备为应用存储快照。详情见[Preparing Your UI to Run in the Background](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background?language=objc)
- 场景取消连接的时候，清理任何场景相关的共享数据。
- 另外的场景相关的事件，开发者必须响应通过 UiApplicationDelegate 对象来启动应用。对于应用如何启动，详见[Responding to the Launch of Your App](https://developer.apple.com/documentation/uikit/app_and_environment/responding_to_the_launch_of_your_app?language=objc)

响应基于应用的生命周期事件：

在 iOS12 以及更早的版本中，应用中不支持场景，UIKit 分发所有的时间到 UIApplicationDelegate 对象。应用 delegate 管理所有的应用窗口，包括那些在分离屏幕中的窗口。结果是，应用的状态将会影响整个 UI，包括屏幕上的内容。

如下图展示了涉及到应用 delegate 对象的数据转换过程。在启动以后，系统将应用放到非活跃或者后台状态，这取决于界面是否出现在屏幕上。当启动应用到前台时，系统自动转换应用的状态到活跃状态。这之后，状态在活跃态和后台状态波动，直到程序结束。

![app-state](/src/image/app-state@2x.png)

使用应用的转移来完成下述的任务：

- 启动时，初始化应用数据结构和界面。详情见 [Responding to Launch of Your App](https://developer.apple.com/documentation/uikit/app_and_environment/responding_to_the_launch_of_your_app?language=objc)

- 在启动时，完成配置界面并且准备和用户交互。详情见[Preparing Your UI to Run in the Foreground](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_foreground?language=objc)

- 停用时，存储数据并且结束应用行为，详情见[Preparing Yout UI to RUn in the Background](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background?language=objc)

- 进入到后台状态时，结束重要任务，释放尽可能多的内存，准备应用的快照。详情见[Preparing your UI to Run in the Background](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background?language=objc)

- 终止时，立即停止所有的工作，释放所有的共享资源。详情见[applicationWillTerminate:](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623111-applicationwillterminate?language=objc)

# 响应其他的通知事件

额外的处理生命周期事件，应用必须准备处理下表格中的事件。使用 UIApplicationDelegate 对象来处理下述大部分事件。在有些时候，开发者可能也可以通过通知来处理他们，这允许开发者从应用的其他地方来响应事件。

| :--: | :--: |
|Memory warnings | Received when your app’s memory usage is too high. Reduce the amount of memory your app uses; see Responding to Memory Warnings.|
|Protected data becomes available/unavailable|Received when the user locks or unlocks their device. See applicationProtectedDataDidBecomeAvailable: and applicationProtectedDataWillBecomeUnavailable:.|
|Handoff tasks|Received when an NSUserActivity object needs to be processed. See application:didUpdateUserActivity:.|
|Time changes|Received for several different time changes, such as when the phone carrier sends a time update. See applicationSignificantTimeChange:.|
|Open URLs|Received when your app needs to open a resource. See application:openURL:options:.|
