### 术语列表

## scheme

scheme 是一系列的设置，定义了项目要编译的 target，编译的配置，产品启动时的执行环境变量。当开发者打开了一个存在的项目（或者创建了一个新的项目），Xcode 会自动为每个 target 创建一个 scheme。默认的 scheme 用项目名命名。

## target

target 指定了一个需要编译的产品，例如 ios，watchOs，maxOs 应用。当开发者从一个模板新建了一个应用时，target 会自动添加。如果开发者在创建项目时，选择单元测试或者界面测试，项目会包含主应用的 target 和测试应用的 target。一个依赖于 ios 应用的 watchOS 应用，拥有该 ios 应用的 target，WatchKit 扩展和 watchKit 应用。macOS 应用可以拥有多个 target。在项目编辑器中选择一个 target，以查看和编辑配置。在项目编辑器中点击添加按钮来创建一个新的应用，框架或者库，测试，应用拓展的 target 增加到项目中。

target 的设置可以在如下的项目编辑器中编辑：

- 通常。通用的编辑 target 设置，例如 bundle id， version， build number。通常设置存在在其他的 target 设置目录中，但是为了方便使用，这里也被包含了进去。
- 签名和能力。应用的 code 签名设置和应用的服务能力，例如 iCloud，推送和游戏中心。
- 资源标签。项目的自定义的标签用以管理一系列的按需资源。
- 信息。target 的`Info.plist`中的设置。
- 构建设置。设置指定了 target 如何构建。
- 构建阶段。设置用以描述构建时执行的动作，例如编译源代码，复制资源文件。
- 构建规则。定义了用于构建特定类型的构建的规则。

## destination

destination 用来决定应用要被构建后在哪里运行。

## workspace

workspace 作为一个容器包含了多个相关的项目。Xcode 可以探查多个 target 之间的隐式链接，并且在构建阶段按照顺序运行。距离来说，Xcode 会先编译一个框架，后编译一个链接到框架的应用。

### 构建流程

- Check Pod Manifest.lock 检测`Podfile.lock`文件和`Manifest.lock`文件是否一致
- Start Packager 打开 Metro
- Copy Bundle Resources 复制 assets，bundle 等文件到 ipa 中
