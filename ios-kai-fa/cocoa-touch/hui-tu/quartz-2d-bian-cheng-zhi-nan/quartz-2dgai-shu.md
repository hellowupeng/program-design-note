# Quartz 2D概述

Quartz 2D是一个二维绘图引擎，可在iOS环境中和内核以外的所有Mac OS X应用程序环境中访问。您可以使用Quartz 2D应用程序编程接口（API）访问基于路径的绘图，透明绘画，着色，绘制阴影，透明度图层，PDF文档生成和PDF元数据访问等功能。Quartz 2D尽可能利用图形硬件的强大功能。

在Mac OS X中，Quartz 2D可以与所有其他图形和成像技术 - Core Image，Core Video，OpenGL和QuickTime一起使用。

同样，在iOS中，Quartz 2D可以使用所有可用的图形和动画技术，例如Core Animation，OpenGL ES和UIKit类。

### The Page

Quartz 2D使用画家的模型进行成像。在画家的模型中，每个连续的绘画操作都将“绘画”层应用于输出“画布”，通常称为页面。通过附加绘图操作叠加更多涂料，可以修改页面上的涂料。在页面上绘制的对象不能被修改，除非覆盖更多的绘画。该模型允许您从少量强大的基元构建极其复杂的图像。

下图显示了画家模型的工作原理。为了在图的顶部获得图像，左边的形状先被绘制，然后是实体形状。实体形状覆盖第一个形状，遮蔽了除第一个形状外的所有形状。图形底部以相反顺序绘制形状，首先绘制实体形状。正如你所看到的，在画家的模型中，绘画顺序很重要。

画家的模型:

![](/assets/painters_model.gif)

该页面可能是一张真正的纸张（如果输出设备是打印机）;它可能是一张虚拟的纸张（如果输出设备是PDF文件）;它甚至可能是一个位图图像。页面的确切性质取决于您使用的特定图形上下文。

### 绘图目的地：图形上下文（The Graphics Context）

图形上下文是一种不透明的数据类型（CGContextRef），封装了Quartz用于将图像绘制到输出设备（如PDF文件，位图或显示器上的窗口）的信息。图形上下文中的信息包括图形绘制参数和页面上绘画的设备特定表示。Quartz中的所有对象都被绘制或包含在图形上下文中。

您可以将图形上下文视为绘图目的地。使用Quartz进行绘制时，所有设备特定的特征都包含在您使用的特定类型的图形上下文中。换句话说，您可以简单地通过为相同的Quartz绘图例程序列提供不同的图形上下文来将相同的图像绘制到不同的设备上。您不需要执行任何设备特定的计算;Quartz 为你做。

Quartz 绘图目的地：

![](/assets/draw_destinations.gif)

这些图形上下文可用于您的应用程序：

* 位图图形上下文允许您将RGB颜色，CMYK颜色或灰度绘制到位图中。位图是像素的矩形阵列（或光栅），每个像素代表图像中的一个点。

* PDF图形上下文允许您创建PDF文件。在PDF文件中，您的绘图被保存为一系列命令。PDF文件和位图之间有一些显着差异：

  * 与位图不同，PDF文件可能包含多个页面。

  * 当您从不同设备上的PDF文件中绘制页面时，生成的图像将针对该设备的显示特性进行优化。

  * PDF文件本质上是独立于分辨率的 - 它们被绘制的大小可以无限增大或减小，而不会牺牲图像细节。位图图像的用户感知质量与打算查看位图的分辨率相关联。

* 窗口图形上下文是一个您可以使用它来绘制到窗口中的图形上下文。请注意，由于Quartz 2D是图形引擎而不是窗口管理系统，因此您可以使用其中一个应用程序框架来获取窗口的图形上下文。

* 图层上下文（CGLayerRef）是与另一图形上下文关联的屏外绘图目的地。对于离屏绘图而言，图层上下文可能比位图图形上下文更好。

* 当您想要在Mac OS X中打印时，将内容发送到由打印框架管理的PostScript图形上下文。

### Quartz 2D不透明数据类型

除了图形上下文之外，Quartz 2D API还定义了各种不透明的数据类型。因为API是Core Graphics框架的一部分，所以对它们进行操作的数据类型和例程使用CG前缀。

Quartz 2D根据应用程序运行的不透明数据类型创建对象，以实现特定的绘图输出。下图显示了当您将绘图操作应用于Quartz 2D提供的三个对象时可以实现的各种结果。例如：

* 您可以通过创建PDF页面对象来旋转和显示PDF页面，将旋转操作应用于图形上下文，并要求Quartz 2D将页面绘制到图形上下文中。

* 您可以通过创建一个图案对象来绘制图案，定义构成图案的形状，并设置Quartz 2D将图案作为绘制到图形上下文时使用。

* 您可以通过创建阴影对象来填充具有轴向或径向阴影的区域，提供一个函数来确定阴影中每个点的颜色，然后要求Quartz 2D使用阴影作为填充颜色。

不透明数据类型是在Quartz 2D中绘制图元的基础:

![](/assets/drawing_primitives.gif)

Quartz 2D中可用的不透明数据类型包括以下内容：

* [CGPathRef](https://developer.apple.com/documentation/coregraphics/cgpathref)，用于矢量图形来创建您填充或描边的路径。

* [CGImageRef](https://developer.apple.com/documentation/coregraphics/cgimageref)，用于根据您提供的样本数据表示位图图像和位图图像蒙版。

* [CGLayerRef](https://developer.apple.com/documentation/coregraphics/cglayer)，用于表示可用于重复绘图（例如用于背景或图案）和用于屏外绘图的绘图层。

* [CGPatternRef](https://developer.apple.com/documentation/coregraphics/cgpattern)，用于重复绘图。

* [CGShadingRef](https://developer.apple.com/documentation/coregraphics/cgshadingref) 和 [`CGGradientRef`](https://developer.apple.com/documentation/coregraphics/cggradient)，用于绘制渐变。

* [CGFunctionRef](https://developer.apple.com/documentation/coregraphics/cgfunctionref)，用于定义采用任意数量的浮点参数的回调函数。在为阴影创建渐变时使用此数据类型。

* [CGColorRef](https://developer.apple.com/documentation/coregraphics/cgcolorref) 和 [`CGColorSpaceRef`](https://developer.apple.com/documentation/coregraphics/cgcolorspace)，用于通知Quartz如何解释颜色。

* [CGImageSourceRef](https://developer.apple.com/documentation/imageio/cgimagesource) 和 [`CGImageDestinationRef`](https://developer.apple.com/documentation/imageio/cgimagedestination)，用于将数据移入和移出Quartz。

* [CGFontRef](https://developer.apple.com/documentation/coregraphics/cgfontref) 用于绘制文本。

* [`CGPDFDictionaryRef`](https://developer.apple.com/documentation/coregraphics/cgpdfdictionaryref),[`CGPDFObjectRef`](https://developer.apple.com/documentation/coregraphics/cgpdfobjectref),[`CGPDFPageRef`](https://developer.apple.com/documentation/coregraphics/cgpdfpage),[`CGPDFStream`](https://developer.apple.com/documentation/coregraphics/cgpdfstreamref),[`CGPDFStringRef`](https://developer.apple.com/documentation/coregraphics/cgpdfstringref), 和[`CGPDFArrayRef`](https://developer.apple.com/documentation/coregraphics/cgpdfarrayref), 提供对PDF元数据的访问。

* GPDFScannerRef 和 CGPDFContentStreamRef，解析PDF元数据。

* CGPSConverterRef，用于将PostScript转换为PDF。它在iOS中不可用。

### 图形状态

Quartz根据当前图形状态下的参数修改绘制操作的结果。图形状态包含的参数，除此之外被视为绘制例程的参数。绘制到图形上下文的例程会查询图形状态以确定如何呈现其结果。例如，当您调用函数来设置填充颜色时，您正在修改存储在当前图形状态中的值。当前图形状态的其他常用元素包括线宽（line width），当前位置（current position）和文本字体大小（text font size）。

图形上下文（graphics context）包含一个图形状态（graphics states）堆栈。当Quartz创建一个图形上下文时，堆栈是空的。保存图形状态时，Quartz会将当前图形状态的副本压入堆栈。当您恢复图形状态时，Quartz弹出堆栈顶部的图形状态。弹出状态成为当前图形状态。

为了保存当前的图形状态，使用函数`CGContextSaveGState`将当前图形状态的副本推送到堆栈上。要恢复先前保存的图形状态，请使用函数`CGContextRestoreGState`将当前图形状态替换为堆栈顶部的图形状态。

请注意，并非当前绘图环境的所有方面都是图形状态的元素。例如，当前路径不被视为图形状态的一部分，因此在调用函数`CGContextSaveGState`时不会保存。调用此函数时保存的图形状态参数在下面列出。

与图形状态关联的参数：

* 当前变换矩阵（CTM）

* 剪切区域（Clipping area）

* 线条：宽度，连接，上限，短划线，斜接限制（Line: width, join, cap, dash, miter limit）

* 曲线估计精度（平坦度）

* 颜色：填充和笔画设置（Color: fill and stroke settings）

* Alpha值（透明度）

* 渲染意图

* 色彩空间：填充和笔触设置（Color space: fill and stroke settings）

* 文本：字体，字体大小，字符间距，文本绘图模式

* 混合模式

### Quartz 2D 坐标系统

### 内存管理：对象所有权



