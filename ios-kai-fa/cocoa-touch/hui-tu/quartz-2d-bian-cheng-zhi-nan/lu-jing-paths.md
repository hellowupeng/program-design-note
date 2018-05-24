# 路径（Paths）

路径定义了一个或多个形状或子路径。子路径可以由直线，曲线或两者组成。它可以打开或关闭。子路径可以是简单的形状，例如线条，圆形，矩形或星形，也可以是更复杂的形状，如山脉或抽象涂鸦的轮廓。图3-1显示了一些可以创建的路径。直线（在图的左上方）可以是虚线的;线条也可以是实心的。弯曲的路径（在中间顶部）由多条曲线组成，并且是一条开放的路径。同心圆被描边，但未被填充。加利福尼亚州是一条封闭的路径，由许多曲线组成，道路既被描边又被填充。星星说明了填充路径的两种选择，本章后面将介绍这些选项。

图3-1 Quartz支持基于路径的绘图：

### ![](/assets/Quartz supports path-based drawing.png) 路径创建和路径绘画

路径创建和路径绘制是独立的任务。首先你创建一条路径。当你想渲染一条路径时，你需要Quartz来绘制它。如图3-1所示，您可以选择描边路径，填充路径，或者同时描边和填充路径。您还可以使用路径来限制创建实际上剪切区域的路径边界内的其他对象的绘制。

图3-2显示了已绘制的路径，其中包含两个子路径。左边的子路径是一个矩形，而右边的子路径是由直线和曲线组成的抽象形状。每个子路径都被填充并且它的轮廓被描边。

图3-2 包含两个形状或子路径的路径

![](/assets/A path that contains two shapes, or subpaths.png)

图3-3 显示了独立绘制的多个路径。每条路径都包含一条随机生成的曲线，其中一些曲线被填充，另一些则被描边。绘图被剪辑区域限制为圆形区域。

图3-3 裁剪区域限制绘图

![](/assets/A clipping area constrains drawing.png)

### 构建块

子路径由线条（lines），弧线（arcs）和曲线（curves）构建而成。Quartz 还提供了很多方便的函数，用一个函数调用添加矩形和椭圆。点也是重要的路径构建块，因为点定义了形状的起始位置和结束位置。

##### 点（Points）

点是指定用户空间中位置的x和y坐标。您可以调用函数`CGContextMoveToPoint`来指定新子路径的起始位置。Quartz会跟踪当前点，这是用于路径构建的最后一个位置。例如，如果您调用函数`CGContextMoveToPoint`在（10,10）处设置位置，则会将当前点移动到（10,10）。如果你画一条50单位长的水平线，那么线上的最后一点，即（60,10）就成为当前点。直线\(Lines\)，圆弧\(arcs\)和曲线\(curves\)总是从当前点开始绘制。

大多数情况下，您通过传递Quartz函数的两个浮点值来指定x和y坐标来指定点。某些函数要求您传递一个`CGPoint`数据结构，该结构包含两个浮点值。

##### 线（Lines）

一条线由其端点定义。它的起点总是被假定为当前点，所以当你创建一条线时，你只能指定它的结束点（endpoint）。您可以使用函数`CGContextAddLineToPoint`将单行（line）附加到子路径。

通过调用函数`CGContextAddLines`，可以将一系列连接线添加到路径中。第一点必须是第一条线的起点;剩余的点是结束点。Quartz在第一个点开始一个新的子路径，并将一条直线段连接到每个结束点。

##### 弧（Arcs）

圆弧是圆形段。Quartz提供了两个创建弧的函数。函数`CGContextAddArc`从圆中创建一个曲线段。您可以指定圆的中心，半径和径向角（以弧度表示）。您可以通过指定2 pi的径向角度来创建一个完整的圆。图3-4显示了独立绘制的多个路径。每个路径包含一个随机生成的圆;一些被填充，另一些被描边。

图3-4 多条路径;每个路径都包含一个随机生成的圆

![](/assets/Multiple paths; each path contains a randomly generated circle.png)

`CGContextAddArcToPoint`函数是您想要圆角矩形的角的理想选择。Quartz使用您提供的端点来创建两条相切线。您还可以提供Quartz切割圆弧的圆的半径。圆弧的中心点是两个半径的交点，每个半径都垂直于两条切线中的一条。圆弧的每个端点（endpoint）都是其中一条切线上的切点，如图3-5所示。圆的红色部分是实际绘制的。

图3-5用两条切线和一个半径定义弧

![](/assets/Defining an arc with two tangent lines and a radius.png)

如果当前路径已经包含子路径，则Quartz将从当前点到弧的起点追加一条直线段。如果当前路径为空，则Quartz会在圆弧的起点处创建一个新的子路径，并且不会添加最初的直线段。

##### 曲线（Curves）

二次方和三次方贝塞尔（Bézier）曲线是代数曲线，可以指定任意数量的有趣的曲线形状。通过将多项式应用于起点和终点以及一个或多个控制点来计算这些曲线上的点。以这种方式定义的形状是矢量图形的基础。一个公式比一系列位存储更紧凑，并且具有曲线可以在任何分辨率下重新创建的优点。

图3-6显示了通过独立绘制多条路径创建的各种曲线。每条路径都包含随机生成的曲线;一些被填充，另一些被描边。

图3-6多条路径;每条路径都包含随机生成的曲线

![](/assets/Multiple paths; each path contains a randomly generated curve.png)

给出二次方和三次Bézier曲线的多项式以及如何从公式生成曲线的细节在很多描述计算机图形的数学文本和在线资源中讨论。这些细节不在这里讨论。

使用`CGContextAddCurveToPoint`函数可以使用控制点和指定的端点从当前点追加三次方Bézier曲线。图3-7显示了由图中显示的当前点，控制点和端点产生的三次Bézier曲线。两个控制点的位置决定曲线的几何形状。如果控制点都在起点和终点之上，则曲线向上拱起。如果控制点都低于起点和终点，则曲线向下拱起。

图3-7贝塞尔三次曲线使用两个控制点:

![](/assets/A cubic Bézier curve uses two control points.png)

您可以通过调用函数`CGContextAddQuadCurveToPoint`并指定控制点和端点（endpoint），从当前点追加二次方Bézier曲线。图3-8显示了使用相同端点但控制点不同的两条曲线。控制点确定曲线的方向。用二次Bézier曲线创建尽可能多的有趣形状，因为二次曲线只能使用一个控制点。例如，使用单个控制点创建分频器是不可能的。

图3-8 二次方贝塞尔曲线使用一个控制点

![](/assets/A quadratic Bézier curve uses one control point.png)

##### 关闭子路径（Closing a Subpath）

要关闭当前子路径，应用程序应调用`CGContextClosePath`。此函数添加从当前点到子路径起点的线段并关闭子路径。以子路径起点为终点的直线，圆弧和曲线实际上并不关闭子路径。您必须显式调用`CGContextClosePath`来关闭子路径。

一些Quartz函数将路径的子路径视为由应用程序关闭。这些命令对待每个子路径，就像应用程序调用`CGContextClosePath`关闭它一样，隐式地将一个线段添加到子路径的起始点。

在关闭一个子路径后，如果你的应用程序进行了额外的调用来将线，弧或曲线添加到路径中，Quartz将从刚刚关闭的子路径的起点开始一个新的子路径。

##### 椭圆（Ellipses）

椭圆本质上是一个压扁的圆。您可以通过定义两个焦点创建一个，然后绘制位于某个距离的所有点，以便将椭圆上任意一点到一个焦点的距离与该点到另一个焦点的距离相加得到的值始终相同。图3-9显示了独立绘制的多个路径。每个路径包含一个随机生成的椭圆;一些被填充，另一些被描边。

图3-9多条路径;每个路径包含一个随机生成的椭圆

![](/assets/Multiple paths; each path contains a randomly generated ellipse.png)

您可以通过调用`CGContextAddEllipseInRect`函数向当前路径添加一个椭圆。您提供了一个定义椭圆边界的矩形。Quartz用一系列贝塞尔曲线近似椭圆。椭圆的中心是矩形的中心。如果矩形的宽度和高度相等（即正方形），则椭圆是圆形的，其半径等于矩形宽度（或高度）的一半。如果矩形的宽度和高度不相等，则它们定义椭圆的长轴和短轴。

添加到路径的椭圆开始于移至操作，并以关闭子路径操作结束，所有移动均以顺时针方向。

##### 矩形（Rectangles）

您可以通过调用`CGContextAddRect`函数向当前路径添加一个矩形。您提供了一个`CGRect`结构，其中包含矩形的原点及其宽度和高度。

添加到路径的矩形以移动操作开始，以结束子路径操作结束，所有移动均以逆时针方向定向。

通过调用函数`CGContextAddRects`并提供`CGRect`结构数组，可以将多个矩形添加到当前路径。图3-10显示了独立绘制的多个路径。每个路径包含一个随机生成的矩形;一些被填充，另一些被描边。

图3-10 多条路径;每个路径都包含一个随机生成的矩形

![](/assets/Multiple paths; each path contains a randomly generated rectangle.png)

### 创建一条路径

当你想在图形上下文中构建一个路径时，你可以通过调用`CGContextBeginPath`函数来发信号通知Quartz。接下来，通过调用函数`CGContextMoveToPoint`来设置路径中第一个形状或子路径的起点。在建立第一个点后，可以将线条，弧线和曲线添加到路径中，请注意以下事项：

* 在开始一个新路径之前，调用函数`CGContextBeginPath`。

* 直线，圆弧和曲线从当前点开始绘制。空路径没有当前点;您必须调用`CGContextMoveToPoint`来为第一个子路径设置起始点，或者调用一个为您隐式执行此操作的便利函数。

* 如果要关闭路径中的当前子路径，请调用函数`CGContextClosePath`将一个段连接到子路径的起始点。后续路径调用会开始一个新的子路径，即使您没有明确设置新的起点。

* 绘制弧线时，Quartz会在当前点与弧线的起点之间绘制一条线。

* 添加椭圆和矩形的Quartz例程为路径添加一个新的封闭子路径。

* 您必须调用绘画函数来填充或描边路径，因为创建路径不会绘制路径。

绘制路径后，将从图形上下文中刷新路径。您可能不想轻易失去自己的路径，特别是如果它描绘了一个想要反复使用的复杂场景。出于这个原因，Quartz提供了两种用于创建可重用路径的数据类型 - `CGPathRef`和`CGMutablePathRef`。你可以调用`CGPathCreateMutable`函数来创建一个可变的`CGPath`对象，你可以在其中添加直线，弧线，曲线和矩形。Quartz提供了一组`CGPath`函数，它们与Building Block中讨论的函数并行。路径函数在`CGPath`对象上运行，而不是在图形上下文中运行。这些功能是：

* `CGPathCreateMutable`，取代`CGContextBeginPath`

* `CGPathMoveToPoint`，取代`CGContextMoveToPoint`

* `CGPathAddLineToPoint`，取代`CGContextAddLineToPoint`

* `CGPathAddCurveToPoint`，取代`CGContextAddCurveToPoint`

* `CGPathAddEllipseInRect`，取代`CGContextAddEllipseInRect`

* `CGPathAddArc`，取代`CGContextAddArc`

* `CGPathAddRect`，取代`CGContextAddRect`

* `CGPathCloseSubpath`，取代`CGContextClosePath`

当你想把路径附加到图形上下文时，你可以调用函数`CGContextAddPath`。路径保持在图形上下文中，直到Quartz描绘它。您可以通过调用`CGContextAddPath`再次添加路径。

### 绘制一条路径

您可以通过描边或填充或两者来绘制当前路径。描边（_Stroking_）绘制一条路径线。填充（_Filling_）绘制路径中包含的区域。Quartz具有可以让您描边路径，填充路径，或者同时描边和填充路径的函数。描边线（宽度，颜色等）的特征，填充颜色和Quartz用来计算填充区域的方法都是图形状态的一部分。

##### 影响描边的参数

您可以通过修改表3-1中列出的参数来影响路径的描边。这些参数是图形状态的一部分，这意味着您为参数设置的值会影响所有后续的描边，直到您将参数设置为其他值。

表3-1 影响Quartz如何描边当前路径的参数：

* 线宽（Line width）：`CGContextSetLineWidth`

* Line join：`CGContextSetLineJoin`

* Line cap：`CGContextSetLineCap`

* 斜接限制（Miter limit）：`CGContextSetMiterLimit`

* Line dash pattern：`CGContextSetLineDash`

* 描边颜色空间（Stroke color space）：`CGContextSetStrokeColorSpace`

* 描边颜色（Stroke color）：`CGContextSetStrokeColor`、`CGContextSetStrokeColorWithColor`

* 描边模式（Stroke pattern）：`CGContextSetStrokePattern`

线宽是线的总宽度，以用户空间为单位表示。

线连接（line join）指定Quartz如何绘制连接线段之间的连接点。Quartz支持表3-2中描述的线连接样式。默认样式是斜接连接。

表3-2 线路连接样式：

* Miter join

![](/assets/Miter join.png)

* Round join

![](/assets/Round join.png)

* Bevel join

![](/assets/Bevel join.png)

线帽指定CGContextStrokePath用于绘制线的端点的方法。

Quartz支持表3-3中描述的线帽样式。

* Butt cap
  ![](/assets/Butt cap.png)
* Round cap

  ![](/assets/Round cap.png)

* Projecting square cap

  ![](/assets/Projecting square cap.png)

闭合的子路径将起始点视为连接的线段之间的连接点;起点使用选定的线连接（line-join）方法呈现。相反，如果通过添加连接到起点的线段来关闭路径，则路径的两端都将使用选定的线头方法绘制。

线 dash pattern 允许您沿着描边路径绘制分段线。您可以通过指定破折号数组（dash array）和破折号相位（dash phase）作为`CGContextSetLineDash`的参数来控制沿着该线的破折号段（dash segments）的大小和位置：

```
void CGContextSetLineDash (
    CGContextRef ctx,
    CGFloat phase,
    const CGFloat lengths[],
    size_t count
);
```

length 参数的元素指定了 dashes 的宽度，在线条的涂漆和未涂漆部分之间交替。phase 参数指定 dash pattern 的起点。图3-11显示了一些线条 dash patterns。

![](/assets/Examples of line dash patterns.png)

描边颜色空间确定Quartz解释描边颜色值的方式。您还可以指定封装颜色和颜色空间的Quartz颜色（`CGColorRef`数据类型）。

##### 用于描边路径的函数



##### 填充路径

##### 设置混合模式

### 剪切到一条路径



