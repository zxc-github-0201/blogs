---
layout: page
title: android
parent: taro
---

[原文](https://developer.android.com/jetpack/guide)

### 应用结构入门

这篇帮助文章包含构建健壮的工业级别代码的最佳实践和推荐的目录结构。

本篇章建设读者已经拥有了基础的安卓框架基础知识。如果你是安卓的新手开发者，请先查看[Developer guides](https://developer.android.com/guide)，并学习其中提及的相关概念。

如果你对于应用程序的结构感兴趣，并且希望从 Kotlin 的角度学习本篇中的内容，请查看[Developing Android Apps with Kotlin](https://www.udacity.com/course/developing-android-apps-with-kotlin--ud9012)

## 移动应用的用户体验

在通常情况下，桌面应用拥有从桌面或者启动器启动的单一入口，然后像一个独石(monolithic)，单一应用一般运行。在另一方面，安卓程序拥有更加复杂的结构。一个典型的安卓程序包含多个应用组件，包括 activities，fragments， services， content providers 和 broadcast receivers。

开发者在应用的 `manifest`中声明大部分的应用组件。 然后安卓系统以此决定如何将开发者的程序集成到设备中。假设一个构建良好的安卓应用包含多个组件并且用户会在短时间内使用多个应用，那么应用需要设配不同设备的工作流和任务。

例如： 假设用户在使用最喜欢的社交软件分享照片的场景

- 1. 应用触发唤起相机的请求。安卓系统唤起相机系统来响应请求。此时，用户离开社交软件，但是他们的切换软件的体验是无缝的。
- 2. 相机应用可能触发其他应用请求，例如唤起文件管理器等。
- 3. 最终，用户返回社交软件并且分享照片。

在删除过程中的任何时候，用户可能会接到一个电话或者通知。在响应了这些终端后，用户希望返回相机分享的过程。这个应用期望行为在移动应用上非常常见，所以开发者的应用必须能正确的处理这些流程。

特别注意，移动设备是资源受限的系统，所以操作系统可能在任何时候杀死应用进程以开启新的应用。

考虑到上述的应用场景，开发者的应用必须可以独立的，无顺序的启动，并且操作系统和用户可以在任何时候关闭应用。应为这些事件并不是在开发者控制范围内的，开发者不应该在应用组件中存储任何的应用数据，并且开发者的应用不应该依赖于其他应用。

### 通用的结构原则

如果你不能使用应用组件来存储应用数据，那么该怎么设计应用呢？

## 关注点分离

最重要的原则是[关注点分离](https://en.wikipedia.org/wiki/Separation_of_concerns)。常见的错误是在一个 Activity 或者 Gragment 中写所有的代码。这些基于界面的类应当只包含界面的逻辑，来响应界面和系统操作。保持这些类足够的干净，开发者可以避免生命周期相关的问题。

需要注意的事开发者没有 Activity 和 Fragment 的实现。另外，这些类也只是安卓系统和应用质检的胶水类。因为低内存等问题，操作系统可以根据用户操作随时关闭他们。为了提供更加令人满意的用户体验和可管理的应用维护体验，开发者最好尽量减少对他们的依赖。

## 从模型(Model)驱动界面

另一个重要的原则是从开发者应当从模型启动界面，最好是持久模型。模型的定义式可以响应数据的应用组件。他们和 View 以及应用的其他组件是相互独立的，所以他们不会受到应用的生命周期和其他影响。

由于以下原因，持久化是理想的：

- 操作系统在清除应用资源的时候，用户不会丢失数据
- 在网络挂了的时候，应用还可以继续工作

通过基于模型构建应用，并良好的组织应用的数据，可以让应用更加可测试和连贯(consistent)。

## 推荐的额应用结构

本章将演示如何通过用户端到端测试并使用[Architecture Components](https://developer.android.com/jetpack#architecture-components)组织应用

建设我们正在构建展示用户信息的界面。我们通过私有的后台和 REST API 获取数据。

## 概览

首先，考虑如下的图表，他展示了所有的模块的交互情况。

![模块交互](../../src/image/frontend/final-architecture.png)

注意到每个组件只依赖于他下层的一个组件。力图 activities 和 fragments 只依赖于一个 ViewModel。存储库(Repository)是唯一依赖于多个其他类的类。在此示例中，存储库依赖于持久数据模型和远程后端数据源。

此设计创造了一致且愉快的用户体验。无论用户在最后一次关闭后，返回到应用多少次，他们总能见到应用程序本地维护的数据。如果数据过时了，应用程序的储存库就会在后台进行更新。

## 创建用户界面

用户界面包含一个 fragment 和 UserProfileFragment，以及响应的布局文件，user_profile_layout.xml。

要启动用户界面，我们的数据模型需要保证以下的数据元素：

- User ID： 用户的唯一表示。最好通过 fragment 参数传递信息到 fragment。如果安卓系统杀死了程序，信息也可以存储，故 ID 可以在下次启东市保留。
- User object： 用户信息的详情数据

我们基于 ViewModel 结构组件，使用 UserProfileViewModel 保持信息。

      ViewModel对象提供了UI组件，例如activity或者fragment，数据，并且包含了业务逻辑代码，与model通信。例如ViewModel可以通过调起其他的组件来获取数据，并且他可以通过转发用户请求来修改数据。ViewModel对UI组件是无关的，所以他不会受到配置文件修改的影响，例如旋转设备的时候重新生成了activity

我们现在可以定义如下文件：

- user_profile.xml： 屏幕的 UI 布局。
- UserProfileFragment： UI 控制器展示数据
- UserProfileViewModel： 用来准备数据和响应用户操作的类

下列的代码片段展示了上述文件的初始内容。

UserProfileViewModel

```java
class UserProfileViewModel : ViewModel() {
   val userId : String = TODO()
   val user : User = TODO()
}
```

UserProfileFragment

```java
class UserProfileFragment : Fragment() {
   // To use the viewModels() extension function, include
   // "androidx.fragment:fragment-ktx:latest-version" in your app
   // module's build.gradle file.
   private val viewModel: UserProfileViewModel by viewModels()

   override fun onCreateView(
       inflater: LayoutInflater, container: ViewGroup?,
       savedInstanceState: Bundle?
   ): View {
       return inflater.inflate(R.layout.main_fragment, container, false)
   }
}
```

### `gradle ar`编译报错

```
java.lang.IllegalAccessError: class org.gradle.internal.compiler.java.ClassNameCollector (in unnamed module @0x18c93146) cannot access class com.sun.tools.javac.code.Symbol$TypeSymbol (in module jdk.compiler) because module jdk.compiler does not export com.sun.tools.javac.code to unnamed module @0x18c93146
```

类似错误信息，可能是 java 的版本不对，导致没有相应的库，react-native 使用 jdk15，mac 可以通过 jenv 来设置默认的 java 环境，[官网](https://www.jenv.be/)

### `taro-rn`安卓端进行`codepush`流程

## 用`@tarojs/cli`初始化 taro 项目

```bash
taro init TaroCodePushDemo
```

## 安装`code-push-cli`和`code-push-react-native`

```bash
yarn global add code-push-cli
yarn add code-push-react-native
```

## 注册`appcenter`账号，[官网](https://appcenter.ms/)

## 生成`code-push`上的 App 和 deployment

```bash
# 登录
appcenter login
# 创建应用
appcenter apps create -d TaroCodePushDemo-android -p React-Native -o Android
# 创建部署
appcenter codepush deployment add -a ${username}/TaroCodePushDemo Staging
# 查看所有的部署
appcenter codepush deployment list -a ${username}/TaroCodePushDemo -k
#
```

## 配置`code-push-react-native`

- 在 build/app/下创建 react.gradle，复制 node_modules/react-native/react.gradle 内容到 build/app/react.gradle 中，并进行如下修改

```java
    // React js bundle directories
    def jsBundleDir = file("$buildDir/generated/assets/react/release")
    def resourcesDir = file("$buildDir/generated/res/react/release")

    def jsBundleFile = file("$jsBundleDir/$bundleAssetName")
    def jsSourceMapsDir = file("$buildDir/generated/assets/react/release")
```

```java
commandLine(
  "npx",
  "taro",
  "build",
  "--type",
  "rn",
  "--platform",
  "android",
  "--reset-cache"
);
```

- 在 build/app/下创建 codepush.gradle，复制 node_modules/react-native-code-push/codepush.gradle 内容到 build/app/codepush.gradle 中，并进行如下修改

- 在 `android/settings.gradle` 文件中，增加如下行

```java
...
include ':app', ':react-native-code-push'
project(':react-native-code-push').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-code-push/android/app')
```

- 在 `android/app/build.gradle` 文件中，增加如下：

```java
...
apply from: "./react.gradle"
apply from: "./codepush.gradle"
...
```

- 更新 `MainApplication.java` 文件

```java
...
// 1. Import the plugin class.
import com.microsoft.codepush.react.CodePush;

public class MainApplication extends Application implements ReactApplication {

    private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
        ...

        // 2. Override the getJSBundleFile method in order to let
        // the CodePush runtime determine where to get the JS
        // bundle location from on each app start
        @Override
        protected String getJSBundleFile() {
            return CodePush.getJSBundleFile();
        }
    };
}
```

- 通过 `appcenter codepush deployment list -a ${username}/TaroCodePushDemo -k` 查看 DeploymentKey， 修改 `android/app/src/main/res/values/strings.xml`

```xml
<resources>
    <!-- <string name="app_name">taroDemo</string> -->
    <string moduleConfig="true" name="CodePushDeploymentKey">${DeploymentKey}</string>
</resources>
```

- 修改`src/app.ts`文件

```javascript
import codePush from "react-native-code-push";
import { Component } from "react";
import "./app.scss";

class App extends Component {
  componentDidMount() {}

  componentDidShow() {}

  componentDidHide() {}

  componentDidCatchError() {}

  // this.props.children 是将要会渲染的页面
  render() {
    return this.props.children;
  }
}
export default codePush(App);
```

## 编译并安装

```bash
# 编译
cd android
./gradlew assemble
# 安装
cd ../
adb install android/app/build/outputs/apk/release/app-release.apk
```

## 测试 codepush

- 修改`src/pages/index/index.ts`文件
- 运行命令

```bash
npx taro build --type rn --reset-cache
appcenter codepush release -a ${username}/TaroCodePushDemo -c android/app/build/generated/assets/react/release/index.android.bundle -t "*"
```
