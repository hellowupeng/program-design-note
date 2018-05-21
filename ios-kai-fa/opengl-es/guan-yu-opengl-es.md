# 关于 OpenGL ES

开放图形库（OpenGL）用于可视化2D和3D数据。它是一个多用途开放标准图形库，支持2D和3D数字内容创建，机械和建筑设计，虚拟样机，飞行模拟，视频游戏等应用。您可以使用OpenGL配置3D图形管道并向其提交数据。顶点被转换并点亮，组装成图元，并被光栅化以创建2D图像。OpenGL旨在将函数调用转换为可发送到底层图形硬件的图形命令。由于该底层硬件专用于处理图形命令，所以OpenGL绘图通常非常快速。

### 用于嵌入式系统的OpenGL（OpenGL ES）是OpenGL的简化版本，它消除了冗余功能，提供了一个易于学习和易于在移动图形硬件中实现的库。![](/assets/OpenGL for Embedded Systems.png)概览

OpenGL ES允许应用程序利用底层图形处理器的强大功能。iOS设备上的GPU可以执行复杂的2D和3D绘图，以及最终图像中每个像素的复杂阴影计算。如果您的应用程序的设计要求需要最直接，最全面地访问GPU硬件，则应该使用OpenGL ES。OpenGL ES的典型客户端包括呈现3D图形的视频游戏和模拟。

OpenGL ES是一个低级的，以硬件为中心的API。虽然它提供了最强大和最灵活的图形处理工具，但它的学习曲线陡峭，对应用程序的整体设计也有重大影响。对于需要高性能图形以进行更多专业用途的应用程序，iOS提供了几个更高级别的框架：

* Sprite Kit框架提供了一个为创建2D游戏而优化的硬件加速动画系统。
* 核心图像（Core Image）框架为静止和视频图像提供实时过滤和分析。

* Core Animation为所有iOS应用程序提供了硬件加速的图形渲染和动画基础结构，以及一个简单的声明式编程模型，使得实现复杂的用户界面动画变得非常简单。

* 您可以使用UIKit框架中的功能为Cocoa Touch用户界面添加动画，基于物理的动态特效和其他特殊效果。

##### OpenGL ES是一个在iOS中实现的平台中立的API

由于OpenGL ES是基于C语言的API，它非常便携且获得了广泛支持。作为C API，它与Objective-C Cocoa Touch应用程序无缝集成。OpenGL ES规范没有定义窗口层;相反，托管操作系统必须提供函数来创建一个接受命令的OpenGL ES渲染上下文和一个帧缓冲区，在该帧缓冲区中写入任何绘图命令的结果。在iOS上使用OpenGL ES需要使用iOS类来设置和呈现绘图表面，并使用平台无关的API来呈现其内容

##### GLKit提供绘图表面和动画支持

由UIKit框架定义的视图和视图控制器控制iOS上视觉内容的表示。 GLKit框架提供了这些类的OpenGL ES感知版本。当您开发OpenGL ES应用程序时，您可以使用GLKView对象来渲染OpenGL ES内容。您还可以使用GLKViewController对象来管理您的视图并支持动画化其内容。

##### iOS支持可选的渲染目标

除了绘制内容以填充整个屏幕或视图层次结构的一部分外，还可以将OpenGL ES帧缓冲区对象用于其他呈现策略。iOS实现标准的OpenGL ES framebuffer对象，您可以将其用于渲染到屏幕外缓冲区或用于OpenGL ES场景中其他位置的纹理。另外，iOS上的OpenGL ES支持渲染到核心动画层（CAEAGLLayer类），然后您可以将其与其他图层结合以构建应用的用户界面或其他视觉显示。

##### 应用需要额外的性能调整

图形处理器是针对图形操作进行优化的并行设备。为了在您的应用中获得出色的性能，您必须仔细设计应用，将数据和命令提供给OpenGL ES，以便图形硬件与您的应用并行运行。一个调整得不好的应用会迫使CPU或GPU等待另一个完成处理命令。

您应该设计您的应用程序以有效使用OpenGL ES API。完成构建应用程序后，使用 Instruments 来微调应用程序的性能。如果您的应用程序在OpenGL ES中受到瓶颈，请使用本指南中提供的信息来优化您的应用程序的性能。

##### OpenGL ES可能不适用于后台应用程序

在后台运行的应用程序可能不会调用OpenGL ES函数。如果您的应用在后台访问图形处理器，它将自动由iOS终止。为了避免这种情况，您的应用程序应该先移交之前提交给OpenGL ES的所有待处理命令，然后再将其移至后台中，并避免调用OpenGL ES，直到移回到前台。

##### OpenGL ES对多线程应用程序增加了额外的限制

设计应用程序以利用并发性可以帮助提高应用程序的性能。如果您打算将并发性添加到OpenGL ES应用程序，则必须确保它不会同时从两个不同的线程访问相同的上下文。

### 如何使用此文档

### 前提条件

### 参见

###### 参考资料

1. [OpenGL ES Programming Guide](https://developer.apple.com/library/content/documentation/3DDrawing/Conceptual/OpenGLES_ProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008793-CH1-SW1)



