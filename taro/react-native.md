---
layout: page
title: react-native
parent: taro
---

### rn 项目启动流程

- 启动`Metro`，`react-native start`
- 启动 App，`react-native run-ios`

### 安卓实体机调试

- usb 连接，进入系统设置，关于本机，点击版本号 5 次，进入开发者模式
- 打开 usb 调试
- `adb devices -l`查看设备是否存在
- `react-native run-android`

### `./gradlew assemble` 命令结果

- [参考](https://www.jianshu.com/p/db62617cbbff)

### `gradle assemble`不打包 bundle 和 hash 的问题

- 增加 codepush 后，`./gradlew assemble`打包项目，运行后通过 logcat 排查，发现`Unable to get the hash of the binary's bundled resources - "codepush.gradle" may have not been added to the build definition.`

- `./gradlew assemble --dry-run` 发现 codepush.gradle 中的 generateBundledResourcesHash 任务没有执行。
- 将`gradle.properties`中的`org.gradle.configureondemand`更改为 false
- 重新执行`./gradlew assemble`
