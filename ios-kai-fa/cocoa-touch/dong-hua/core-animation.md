# 核心动画（Core Animation）

核心动画是 iOS 和 OS X 上可用的图形渲染和动画基础结构，用于为应用的视图和其他视觉元素设置动画。使用 Core Animation，绘制动画的每个帧所需的大部分工作都是为您完成的。你所要做的就是配置一些动画参数（如开始点和结束点）并告诉 Core Animation 开始。Core Animation 完成剩下的工作，将大部分实际绘图工作交给 onboard 图形硬件来加速渲染。这种自动图形加速可实现高帧率和流畅的动画效果，而不会增加 CPU 的负担并减慢应用程序的运行速度。

如果您正在编写 iOS 应用程序，则无论您是否了解，都使用了 Core Animation。如果您正在编写 OS X 应用程序，则可以非常轻松地利用 Core Animation。Core Animation 位于 AppKit 和 UIKit 之下，并紧密集成到 Cocoa 和 Cocoa Touch 的视图工作流中。当然，Core Animation 也具有接口，可以扩展应用视图所显示的功能，并让您更好地控制应用的动画。

