# 视图控制器

### 概览

视图控制器是应用内部结构的基础。

UIViewController类定义了用于管理视图，处理事件，从一个视图控制器转换到另一个视图控制器以及与应用程序的其他部分协调的方法和属性。你可以继承UIViewController（或其某个子类）并添加实现应用程序行为所需的自定义代码。

两种类型的视图控制器：

* 内容视图控制器：管理应用内容的一个独立部分，是你创建的视图控制器的主要类型。
* 容器视图控制器：从子视图控制器收集信息，以便于导航的方式呈现它或以不同方式呈现这些视图控制器的内容。

大多数应用程序是两种视图控制器的混合体。

###### 视图管理

视图控制器最重要的作用是管理视图层次结构。每个视图控制器都有一个包含所有视图控制器内容的根视图。视图控制器始终具有对其根视图的引用，并且每个视图都具有对其子视图的强引用。

视图控制器和它的视图之间的关系：![](/assets/Relationship between a view controller and its views.png)

> 注意：通常的做法是使用 outlets 访问视图控制器视图层次结构中的其他视图。因为视图控制器管理其所有视图的内容，outlets 让你存储需要视图的引用。当视图从 storyboard 加载时，outlets 会自动连接到实际的视图对象。

###### 数据封装

###### 用户交互

###### 资源管理

###### 适应性

##### 视图控制器角色

##### 视图控制器层次结构

##### 设计技巧

### 视图控制器定义

##### 定义子类

##### 实现容器视图控制器

##### 支持辅助功能

##### 保存和恢复状态

### 呈现（Presentation）和过渡（Transition）

##### 呈现视图控制器

##### 使用 Segues

##### 自定义过渡（Transition）动画

##### 创建自定义呈现（Presentation）

### Adaptivity 和 Size 变化

##### 自适应模型

##### 构建自适应界面

###### 参考资料

1. [View Controller Programming Guide for iOS](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/index.html#//apple_ref/doc/uid/TP40007457-CH2-SW1)



