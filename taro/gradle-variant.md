---
layout: page
title: gradle变种
parent: taro
---

[原文](https://docs.gradle.org/current/userguide/variant_model.html)

### 理解变种选择

Gradle 的依赖管理引擎，variant aware。在传统的依赖管理引擎中，例如 maven，依赖会被绑定到 GAV 坐标中。这意味着组件的一系列传递依赖只由 GAV 坐标决定。无论 artifact 是什么，编译所用的依赖都是相同的。另外选择不同的 artifact 需要用到 classifiers，所以相当笨重。该模型的一个问题是他不能提供联系性的全局图，因为 calssifiers 没有通用的语意。因为引擎不知道 classifer 名字在语意上的关联，没有办法阻止 jdk7 和 jdk8 是用了同一个模组。

![component-model-maven.png](../../src/image/frontend/component-model-maven.png "component-model-maven")

Gradle，在 GAV 坐标模型之外引入了变种(variant)的模型。变种对应同一个组件在不同视图(View)下的 GAV 坐标。在 Gradle 模型中，artifacts 被关联到变种上，而非模型。这意味着在实践中，不同的 artifacts 可以拥有不同的依赖。

![component-model-gradle.png](../../src/image/frontend/component-model-gradle.png "component-model-gradle")

这一中间层级，将 artifacts 和依赖关联到了变种上，而非组件，允许 Gradle 配置每个 artifact 的用途。

然而，这产生了一个问题，如何选择变种：Gradle 如何知道使用哪个变种。实践中，变种通过[attributes](https://docs.gradle.org/current/userguide/variant_attributes.html#variant_attributes)被选择，通过语意关联帮助引擎完成一致的分析结果。

由于历史与原因，Gradle 区别了两种不同的组件：

- 本地组件，从源代码构建。[variants are mapped to outgoing configurations](https://docs.gradle.org/current/userguide/variant_model.html#sec:abm_configuration_attributes)
- 外部组件，在仓库中发布，这种情况下，当模块是通过 Gradle Module Metadata 发布，变重视原生支持的，如果通过 Maven 发布，则[变种是通过 metadata 生成的](https://docs.gradle.org/current/userguide/variant_model.html#sec:mapping-maven-ivy-to-variants)

在两种情况下 Gradle 都会执行变种感知选择。

### 配置和变种属性

本地组件将变体公开为[消耗配置](https://docs.gradle.org/current/userguide/declaring_dependencies.html#sec:resolvable-consumable-configs)， 但模块解析发生时，引擎通过选择一个消耗配置选择一个外向组件的变种。

这里有两个需要注意的例外情况：

- 生产者不发布消耗配置
- 消费者指定了特定的配置

当产生这种情况时，变种模式将被跳过。

属性被用在可解析到的配置（消费者）和消耗配置（生产者）两者上面。在其他的配置上添加属性对此没有影响，作为属性不会在配置之间继承。

依赖解析引擎的作用就是寻找一个有消费者表达约束的生产者给出的合适变种。

这就是 attributes 如何运行的，他们的角色即为组件选择合适变种。

变种和配置的区别：

- 对于额外的组件，术语是使用文字变种，而非配置文件。配置是变种的超集。

- 这意味着额外的组件提供拥有属性的变种。然而有时，因为历史原因，词汇配置会被限制在 DSL 中，或者因为 lvy 也有对应的配置概念。

### 可视化的变种信息

Gradle 提供一个叫`outgoingVariants`的报告任务来展示项目的变种，一起展示的还有变种的能力，属性和 artifacts。这在概念上和依赖洞察类似。

通常来说，`outgoingVariants`打印出关于所有变种的信息。它提供一个额外的参数`--variant <variantName>`来选择单个变种打印。同样它接受`--all`来包韩硕传统的和弃用的配置。
