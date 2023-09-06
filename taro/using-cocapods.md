### 为 Xcode 项目添加 Pods

## 事前准备

1. 在[Specs](https://github.com/CocoaPods/Specs)或者[cocapods.org](https://cocoapods.org/)中查看需要用的库，确定是否可用。
2. 在计算机上[安装 CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3).

## 安装

- 创建`Podfile`文件，并添加依赖：

```
target 'MyApp' do
  pod 'AFNetworking', '~> 3.0'
  pod 'FBSDKCoreKit', '~> 4.9'
end
```

- 在项目目录中运行 `$ pod install`
- 打开`App.xcworkspace`并编译

## 和新项目一起安装 CocoaPods

遵循以下步骤，创建一个使用 CocoaPods 的新项目

- 像通常一样在 Xcode 中创建一个新项目
- 打开一个命令行界面，用`$ cd`进入项目目录
- 创建一个 Podfile 文件，这一步可以通过`$ pod init`完成
- 打开 Podfile 文件。文件首行需要指定平台和支持的版本

```
platform :ios, '9.0'
```

- 为了使用 CocoaPods，开发者需要定义一个 Xcode 目标来链接。举个例子，如果开发者要编写一个 ios 应用程序，这将会是应用的名字。通过`target '$TARGET_NAME' do`和`end`创建一个一个目标章节。
- 在 target 代码块中使用一行`pod '$PODNAME'`来添加代码块。

```
target 'MyApp' do
  pod 'ObjectiveSugar'
end
```

- 保存 Podfile 并运行`$ pod install`
- 打开自动生成的`MyApp.xcworkspace`，这就是开发要用的项目文件。

### 集成到现有项目

在现有工作区中集成 CocoaPods 需要在 Podfile 中增加额外的一行。可以简单的在 target 代码块外部指定`.xcworkspace`的文件名，例如如下：

```
  workspace 'MyWorkspace'
```

### 何时使用`pod install`和`pod update`?

很多用户对于何时使用`pod install`和何时使用`pod update`非常疑惑。特别的，他们总是在需要使用`pod update`的时候使用`pod install`

开发者可以在[这篇文章](https://guides.cocoapods.org/using/pod-install-vs-update.html)中发现何时使用这两条命令的区别

### 开发者是否需要将 pods 文件夹归入版本控制系统？

是否将 pods 文件夹加入版本控制取决去开发者，因项目和工作流程而异。我们推荐开发者将 Pods 文件夹加入版本控制，并且不要将其加入`.gitignore`中。但是这最终取决于开发者：

加入版本控制的好处：

- 在 clone 代码后，项目可以立即编译运行，甚至可以不在电脑上安装 CocoaPods。也无需运行`pod install`和英特网连接。
- Pod artifacts 总是存在，甚至在一个 Pod 被删除了（Pod 代码仓库被作者删除）。
- Pod artifacts 总是能保证在初始的安装和后续的 clone 项目中是相同的。

不加入版本控制的好处：

- 总体项目仓库会更小
- 只要 Pods 的源代码可以获取，CocoaPods 一般就能重新创建相同的安装。技术上来说，当 Podfile 不使用提交 SHA 时，无法保证运行`pod install`会获取并创建相同的 artifacts。特别是在 Podfile 中使用 zip 文件。
- 执行任何的版本控制行为，例如合并分支，不会造成任何的冲突。

### 什么是`Podfile.lock`?

这个文件在第一次运行`pod install`后被生成，并且记录任何被安装的 Pod 版本。例如，想象一下下面的依赖：

```
pod 'Reskit'
```

运行 `pod install` 会安装当前版本的 ResKit，`Podfile.lock`会被生成，来表示特定的被安装的版本（例如 RestKit 0.10.3）。由于`Podfile.lock`文件，随后在不同的计算机上运行`pod install`也会同样注册 RestKit 0.10.3，甚至一个新版本存在的情况下。CocoaPods 会在`Podfile.lock`中控制 Pod 版本，除非依赖在 Podfile 被更新
