# 用于构建适用于iOS的OpenGL ES应用程序的清单

OpenGL ES规范定义了一个独立于平台的API，用于使用GPU硬件渲染图形。实现OpenGL ES的平台为执行OpenGL ES命令提供渲染上下文，用于保存渲染结果的帧缓冲区以及呈现帧缓冲区内容的一个或多个渲染目标。在iOS中，EAGLContext类实现了渲染上下文。 iOS只提供一种类型的帧缓冲区，OpenGL ES帧缓冲区对象以及`GLKView`和`CAEAGLLayer`类实现渲染目标。

在iOS中构建OpenGL ES应用程序需要考虑几个问题，其中一些是OpenGL ES编程通用的，其中一些针对iOS。按照此清单和下面的详细部分开始使用：

1. 确定哪些版本的OpenGL ES为您的应用程序设置了正确的功能，并创建OpenGL ES上下文。

2. 在运行时验证设备是否支持您要使用的OpenGL ES功能。

3. 选择渲染OpenGL ES内容的位置。

4. 确保您的应用在iOS中正常运行。

5. 实现你的渲染引擎。

6. 使用Xcode和Instruments调试您的OpenGL ES应用程序并调整它以获得最佳性能。

### 选择支持哪些OpenGL ES版本

确定您的应用是否应该支持OpenGL ES 3.0，OpenGL ES 2.0，OpenGL ES 1.1或多个版本。

* OpenGL ES 3.0是iOS 7中的新功能。它增加了许多新功能，可以实现更高性能，通用GPU计算技术和更复杂的视觉效果，以前只能在桌面级硬件和游戏控制台上实现。

* OpenGL ES 2.0是iOS设备的基准配置文件，具有基于可编程着色器的可配置图形管道。

* OpenGL ES 1.1只提供了一个基本的固定功能图形管道，并且在iOS中主要用于向后兼容。

### 验证OpenGL ES功能

要检查OpenGL ES 3.0扩展，请使用`glGetIntegerv`和`glGetStringi`函数，如下面的代码示例所示：

```
BOOL CheckForExtension(NSString *searchName)
{
    // Create a set containing all extension names.
    // (For better performance, create the set only once and cache it for future use.)
    int max = 0;
    glGetIntegerv(GL_NUM_EXTENSIONS, &max);
    NSMutableSet *extensions = [NSMutableSet set];
    for (int i = 0; i < max; i++) {
        [extensions addObject: @( (char *)glGetStringi(GL_EXTENSIONS, i) )];
    }
    return [extensions containsObject: searchName];
}
```

### 选择一个渲染目的地

在iOS中，帧缓冲区对象存储绘图命令的结果。（iOS不实现窗口系统提供的帧缓冲区）您可以以多种方式使用帧缓冲区对象的内容：

* GLKit框架提供了一个视图，该视图绘制OpenGL ES内容并管理其自己的帧缓冲区对象，以及支持动画OpenGL ES内容的视图控制器。使用这些类创建全屏视图或将您的OpenGL ES内容合并到UIKit视图层次结构中。

### 与iOS集成

### 实现渲染引擎

### 调试和分析



