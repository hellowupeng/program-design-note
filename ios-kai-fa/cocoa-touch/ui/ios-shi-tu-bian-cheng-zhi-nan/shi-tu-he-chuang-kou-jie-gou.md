# 视图和窗口结构

视图和窗口呈现应用程序的用户界面并处理与该界面的交互。UIKit和其他系统框架提供了许多视图，您可以在很少或根本不需要修改的情况下使用它。您还可以为需要以不同于标准视图允许的内容呈现内容的地方定义自定义视图。

无论您使用系统视图还是创建您自己的自定义视图，都需要了解`UIView`和`UIWindow`类提供的基础架构。这些类提供了用于管理视图布局和展示的先进设施。了解这些设施的工作方式对于确保您的视图在您的应用程序发生更改时能够正常运行非常重要。

### 视图架构基础

你可能想要做的大多数事情都是通过视图对象--`UIView`类的实例完成的。视图对象在屏幕上定义一个矩形区域，并处理该区域中的绘图和触摸事件。视图还可以充当其他视图的父项，并协调这些视图的布局和大小。UIView类负责管理视图之间的这些关系的大部分工作，但您也可以根据需要自定义默认行为。

视图与Core Animation图层一起工作来处理视图内容的渲染和动画。UIKit中的每个视图都由层对象（通常是`CALayer`类的一个实例）支持，该对象管理视图的后备存储并处理与视图相关的动画。你执行的大多数操作应该通过`UIView`接口。但是，在需要更多地控制视图的渲染或动画行为的情况下，您可以通过其图层执行操作。

查看示例有助于理解视图和图层之间的关系。图1-1显示了ViewTransitions示例应用程序的视图体系结构以及与底层Core Animation图层的关系。应用程序中的视图包括一个窗口（也是视图），充当容器视图的通用UIView对象，图像视图，一个用于显示控件的工具栏和一个 bar button item（本身不是视图，但是内部管理了一个视图）。每个视图都有一个相应的图层对象，可以从该视图的`layer`属性中进行访问（由于bar button item不是视图，因此无法直接访问其图层。）。这些图层对象的后面是Core Animation渲染对象，最后是用于管理屏幕上实际位的硬件缓冲区。

图1-1 示例应用程序中视图的体系结构：

![](/assets/Architecture of the views in a sample application.png)

核心动画层对象的使用对性能有重要影响。视图对象的实际绘图代码尽可能少地调用，并且在调用代码时，结果由Core Animation缓存并在稍后重用。重用已经呈现的内容消除了更新视图通常需要的昂贵的绘图周期。在内容可以被操纵的动画中，重用这些内容尤为重要。这种重复使用比创建新内容要便宜得多。

##### 查看层次结构和子视图管理

除了提供自己的内容之外，视图还可以充当其他视图的容器。当一个视图包含另一个视图时，两个视图之间会创建一个父子关系。关系中的子视图称为 subview，父视图称为 superview。这种类型的关系的创建对应用程序的外观和应用程序的行为都有影响。

从视觉上来说，子视图的内容遮盖了其父视图的全部或部分内容。如果子视图是完全不透明的，那么子视图占用的区域完全遮蔽了父对象的相应区域。如果子视图部分透明，则来自两个视图的内容在被显示在屏幕上之前被混合在一起。每个父视图都将其子视图存储在有序数组中，并且该数组中的顺序也会影响每个子视图的可见性。如果两个兄弟子视图彼此重叠，则最后添加的子视图（或移动到子视图数组的末尾）会出现在另一个之上。

父视图 - 子视图关系也会影响多个视图行为。更改父视图的大小会产生连锁效应，从而导致任何子视图的大小和位置也发生变化。当您更改父视图的大小时，可以通过适当地配置视图来控制每个子视图的调整大小行为。影响子视图的其他更改包括隐藏父视图，更改父视图的alpha（透明度）或将数学变换应用于父视图的坐标系。

视图层次结构中的视图排列也决定了您的应用程序如何响应事件。当在特定视图内发生触摸时，系统将带有触摸信息的事件对象直接发送到该视图进行处理。但是，如果视图不处理特定的触摸事件，它可以将事件对象传递给它的父视图。如果父视图不处理事件，它将事件对象传递给它的父视图，以此类推到响应者链。特定视图也可以将事件对象传递给干预响应者对象，如视图控制器。如果没有对象处理事件，它最终会到达应用程序对象，通常会丢弃它。

##### 视图绘制周期

UIView类使用按需绘制模型来呈现内容。当视图第一次出现在屏幕上时，系统会要求它绘制其内容。系统捕获此内容的快照并将该快照用作视图的视觉表示。如果你永远不改变视图的内容，视图的绘图代码可能永远不会再被调用。大多数涉及视图的操作都会重用快照图像。如果您更改内容，则会通知系统视图已更改。该视图然后重复绘制视图并捕获新结果的快照的过程。

当视图的内容发生变化时，您不会直接重绘这些更改。而是使用`setNeedsDisplay`或`setNeedsDisplayInRect：`方法使视图无效。这些方法告诉系统，视图的内容发生了变化，需要在下一次机会中重新绘制。在启动任何绘图操作之前，系统会等待当前运行循环的结束。这种延迟使您有机会使多个视图失效，从您的层次结构中添加或删除视图，隐藏视图，调整视图大小以及一次重新定位视图。然后，您所做的所有更改都会同时反映出来。

当需要渲染视图的内容时，实际绘图过程会根据视图及其配置而变化。系统视图通常实现私有绘图方法来呈现其内容。这些相同的系统视图通常会公开可用于配置视图实际外观的接口。对于自定义`UIView`子类，通常会覆盖视图的`drawRect：`方法，并使用该方法绘制视图的内容。还有其他方法可以提供视图的内容，例如直接设置底层的内容，但覆盖`drawRect：`方法是最常用的技术。

##### 内容模式



##### 可伸展视图

##### 内置动画支持

### 视图几何和坐标系统

### 视图的运行时交互模型

### 高效使用视图的技巧


