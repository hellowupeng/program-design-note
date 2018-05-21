# 设置图层对象（Setting Up Layer Objects）

图层对象是核心动画的核心。图层管理您的应用的视觉内容，并提供修改该内容的风格（style）和视觉外观（visual appearance）的选项。虽然iOS应用程序已自动启用图层支持，但OS X应用程序的开发人员必须先明确启用它，才能利用性能优势。

### 在您的应用程序中启用核心动画支持

在iOS应用程序中，Core Animation始终处于启用状态，并且每个视图都由一个图层支持。在OS X中，应用程序必须通过执行一些操作来显式启用Core Animation支持。

### 更改与视图关联的图层对象

默认情况下，支持图层的视图会创建CALayer类的实例，并且在大多数情况下，您可能不需要不同类型的图层对象。但是，Core Animation提供了不同的图层类，每个图层类都提供了您可能会觉得有用的专用功能。选择不同的图层类可能使您能够以简单的方式提高性能或支持特定类型的内容。例如，CATiledLayer类针对以有效方式显示大图像进行了优化。

##### 更改UIView使用的图层类

您可以通过覆盖视图的layerClass方法并返回不同的类对象来更改iOS视图使用的图层类型。大多数iOS视图创建一个CALayer对象，并将该图层用作其内容的后备存储。对于你自己的大部分视图，这个默认选择是一个很好的选择，你不需要改变它。但是您可能会发现在某些情况下，不同的图层类更合适。例如，您可能想在以下情况下更改图层类：

* 您的视图使用Metal或OpenGL ES绘制内容，在这种情况下，您将使用CAMetalLayer或CAEAGLLayer对象。

* 有一个专门的图层类可以提供更好的性能。

* 您想要利用一些专门的核心动画层类，如粒子发射器（particle emitters）或复制器（replicators）。

更改视图的图层类非常简单;你所要做的就是重写layerClass方法，并返回你想要使用的类对象。在显示之前，视图调用layerClass方法并使用返回的类为其自身创建一个新的图层对象。一旦创建，视图的图层对象就不能改变。

指定iOS视图的图层类：

```
+ (Class) layerClass {
   return [CAMetalLayer class];
}
```

##### 更改NSView使用的图层类

##### 图层托管允许您更改OS X中的图层对象

##### 不同的图层类提供专门的行为

核心动画定义了许多标准的图层类，每个类都是为特定用例而设计的。CALayer类是所有图层对象的根类。它定义了所有图层对象必须支持的行为，并且是层级视图所使用的默认类型。

CALayer子类及其用途:

* [CAEmitterLayer](https://developer.apple.com/documentation/quartzcore/caemitterlayer)

用于实现基于Core Animation的粒子发射器系统。发射器层对象控制粒子的生成及其原点。

* [CAGradientLayer](https://developer.apple.com/documentation/quartzcore/cagradientlayer)

用于绘制填充图层形状的颜色渐变（在任何圆角的范围内）。

* [CAMetalLayer](https://developer.apple.com/documentation/quartzcore/cametallayer)

用于使用Metal设置和销售用于渲染图层内容的可绘制纹理。

* [`CAEAGLLayer`](https://developer.apple.com/documentation/quartzcore/caeagllayer)/[`CAOpenGLLayer`](https://developer.apple.com/documentation/quartzcore/caopengllayer)

用于设置使用OpenGL ES（iOS）或OpenGL（OS X）呈现图层内容的后备存储和上下文。

* [CAReplicatorLayer](https://developer.apple.com/documentation/quartzcore/careplicatorlayer)

当您想自动复制一个或多个子图层时使用。复制器为您制作副本，并使用您指定的属性来更改副本的外观或属性。

* [CAScrollLayer](https://developer.apple.com/documentation/quartzcore/cascrolllayer)

用于管理由多个子层组成的大型可滚动区域。

* [CAShapeLayer](https://developer.apple.com/documentation/quartzcore/cashapelayer)

用于绘制一个立方体贝塞尔样条曲线。形状图层对于绘制基于路径的形状是有利的，因为它们总是会形成一条清晰的路径，与绘制到图层后台存储区的路径相反，在缩放时看起来不太好。但是，清晰的结果确实涉及渲染主线程上的形状并缓存结果。

* [CATextLayer](https://developer.apple.com/documentation/quartzcore/catextlayer)

用于呈现纯文本或属性字符串。

* [CATiledLayer](https://developer.apple.com/documentation/quartzcore/catiledlayer)

用于管理大图像，该图像可以分成更小的图块并单独渲染，支持放大和缩小内容。

* [CATransformLayer](https://developer.apple.com/documentation/quartzcore/catransformlayer)

用于渲染真正的3D图层层次结构，而不是其他图层类实现的拼合图层层次结构。

* QCCompositionLayer

用于渲染Quartz Composer组合。 （仅限OS X）

### 提供图层的内容

图层是管理您的应用提供的内容的数据对象。图层的内容由包含要显示的可视化数据的位图组成。您可以通过以下三种方式之一提供该位图的内容：

* 直接将图像对象分配给图层对象的内容属性。（这种技术最适用于从未或很少改变的图层内容。）

* 将委托对象分配给图层，并让委托绘制图层的内容。这种技术最适合可能定期更改的图层内容，并且可以由外部对象（如视图）提供。）

* 定义一个图层子类并重写其中一个绘图方法以自行提供图层内容。（如果您必须创建自定义图层子类，或者要更改图层的基本绘制行为，则此技术是适当的。）

唯一需要担心为图层提供内容的时间是您自己创建图层对象时。如果您的应用程序仅包含层次支持的视图，则不必担心使用上述任何技术提供图层内容。图层支持的视图以尽可能高效的方式自动为其关联图层提供内容。

##### 使用图像作为图层的内容

由于图层只是用于管理位图图像的容器，因此可以将图像直接分配给图层的内容属性。为图层分配图像非常简单，您可以指定想要在屏幕上显示的确切图像。该图层使用您直接提供的图像对象，并且不会尝试创建该图像的自己的副本。如果您的应用在多个位置使用相同的图像，此行为可以节省内存。

您分配给图层的图像必须是CGImageRef类型。分配图像时，请记住提供分辨率与本机设备分辨率相匹配的图像。对于具有Retina显示器的设备，这可能还需要您调整图像的contentsScale属性。

##### 使用委托来提供图层的内容

如果图层的内容动态更改，则可以使用委托对象在需要时提供和更新该内容。在显示时间，图层会调用委托的方法来提供所需的内容：

* 如果委托实现displayLayer：方法，则该实现负责创建位图并将其分配给图层的内容属性。

* 如果您的委托实现了drawLayer：inContext：方法，Core Animation将创建一个位图，创建一个图形上下文以绘制该位图，然后调用委托方法来填充位图。

委托对象必须实现displayLayer：或drawLayer：inContext：方法。如果委托实现displayLayer：和drawLayer：inContext：方法，则该层仅调用displayLayer：方法。

覆盖displayLayer：方法最适用于应用程序偏好加载或创建要显示的位图的情况。下面显示了displayLayer：delegate方法的示例实现。在这个例子中，委托使用一个辅助对象来加载和显示它所需要的图像。委托方法根据自己的内部状态选择要显示哪个图像，在本例中是一个名为displayYesImage的自定义属性。

直接设置图层内容：

```
- (void)displayLayer:(CALayer *)theLayer {
    // Check the value of some state property
    if (self.displayYesImage) {
        // Display the Yes image
        theLayer.contents = [someHelperObject loadStateYesImage];
    }
    else {
        // Display the No image
        theLayer.contents = [someHelperObject loadStateNoImage];
    }
}
```

如果您没有预渲染的图像或助手对象为您创建位图，则您的委托可以使用drawLayer：inContext：方法动态地绘制内容。下面显示了drawLayer：inContext：方法的示例实现。在此示例中，委托使用固定宽度和当前渲染颜色绘制简单的曲线路径。

绘制图层的内容：

```
- (void)drawLayer:(CALayer *)theLayer inContext:(CGContextRef)theContext {
    CGMutablePathRef thePath = CGPathCreateMutable();
 
    CGPathMoveToPoint(thePath,NULL,15.0f,15.f);
    CGPathAddCurveToPoint(thePath,
                          NULL,
                          15.f,250.0f,
                          295.0f,250.0f,
                          295.0f,15.0f);
 
    CGContextBeginPath(theContext);
    CGContextAddPath(theContext, thePath);
 
    CGContextSetLineWidth(theContext, 5);
    CGContextStrokePath(theContext);
 
    // Release the path
    CFRelease(thePath);
}
```

对于具有自定义内容的图层支持的视图，您应该继续覆盖视图的方法来执行绘制。图层支持的视图自动将其自己作为其图层的委托并实现所需的委托方法，并且不应更改该配置。相反，你应该实现你的视图的drawRect：方法来绘制你的内容。

##### 通过子类提供图层内容

如果您正在实现自定义图层类，则可以覆盖图层类的绘图方法以执行任何绘图。图层对象本身生成自定义内容的情况并不常见，但图层当然可以管理内容的显示。例如，CATiledLayer类通过将较大的图像拆分为可以单独管理和渲染的较小图块来管理大图像。例如，CATiledLayer类通过将较大的图像拆分为可以单独管理和渲染的较小图块来管理大图像。因为只有图层具有关于在任何给定时间需要渲染哪些图块的信息，所以它直接管理图形行为。

在继承子类时，可以使用以下任一技术来绘制图层的内容：

* 覆盖图层的`display`方法，并使用它直接设置图层的`content`属性。

* 覆盖图层的`drawInContext：`方法并使用它绘制到提供的图形上下文中。

您重写的方法取决于您在绘图过程中需要多少控制。`display`方法是更新图层内容的主要入口点，因此覆盖该方法可使您完全控制过程。覆盖`display`方法也意味着你负责创建要分配给`content`属性的`CGImageRef`。如果您只想绘制内容（或者让图层管理绘图操作），则可以改为重写`drawInContext：`方法，并让图层为您创建后备存储。

##### 调整您提供的内容

将图像分配给图层的`content`属性时，图层的`contentsGravity`属性决定如何处理该图像以适应当前边界。默认情况下，如果图像大于或小于当前边界，图层对象将缩放图像以适合可用空间。如果图层边界的高宽比与图像的高宽比不同，则会导致图像失真。您可以使用`contentsGravity`属性来确保以最佳方式呈现您的内容。

您可以分配给`contentGravity`属性的值分为两类：

* 基于位置的重力常量允许您将图像固定到图层边界矩形的特定边或角，而不缩放图像。
* 基于比例的重力常量可让您使用多种选项中的一种来拉伸图像，其中一些选项可保留高宽比，其中一些选项不会。

下面显示基于位置的重力设置如何影响您的图像。除`kCAGravityCenter`常量外，每个常量都会将图像固定到图层边界矩形的特定边或角点。`kCAGravityCenter`常数将图层置于图层的中心。这些常量都不会导致图像以任何方式缩放，因此图像总是以原始大小呈现。如果图像大于图层边界，则可能会导致图像部分被裁剪，如果图像较小，图层未覆盖的图层部分会显示图层的背景颜色（如果已设置）。

基于位置的重力常量用于图层：![](/assets/layer_contentsgravity1_2x.png)下面显示了基于比例的重力常量如何影响图像。所有这些常量都会缩放图像，如果它不完全适合图层的边界矩形。模式之间的区别在于它们如何处理图像的原始高宽比。一些模式保留它，而其他模式不保留。默认情况下，图层的`contentsGravity`属性设置为`kCAGravityResize`常量，这是唯一不保留图像宽高比的模式。

针对图层的基于比例的重力常数:![](/assets/positioningmask_2x.png)

##### 处理高分辨率图像

图层对底层设备的屏幕分辨率没有任何固有的知识。一个图层只是存储一个指向你的位图的指针，并以给定可用像素的最佳方式显示它。如果将图像分配给图层的内容属性，则必须通过将图层的`contentsScale`属性设置为适当的值来告诉Core Animation关于图像的分辨率。该属性的默认值为1.0，这适用于打算在标准分辨率屏幕上显示的图像。如果您的图像用于Retina显示，请将此属性的值设置为2.0。只有在您直接为您的图层分配位图时，才需要更改`contentsScale`属性的值。UIKit和AppKit中的图层支持视图根据屏幕分辨率和视图管理的内容自动将图层的比例因子设置为适当的值。

### 调整图层的视觉风格和外观

图层对象内置了可以用来补充图层主要内容的可视化装饰，例如边框和背景颜色。由于这些视觉装饰并不需要您的任何渲染，所以它们可以在某些情况下将图层作为独立实体使用。你所要做的就是在图层上设置一个属性，图层处理必要的图形，包括任何动画。

##### 图层有他们自己的背景和边界（border）

除了基于图像的内容之外，图层还可以显示填充背景和描边边框。他将背景颜色渲染到图层的内容图像后面，并将边框呈现在该图像的顶部。如果图层包含子图层，则图层也会出现在边框下方。由于背景颜色位于图像后面，因此该颜色会透过图像的任何透明部分。

为图层添加边框和背景：![](/assets/layer_border_background_2x.png)

下面显示设置图层背景颜色和边框所需的代码。所有这些属性都是可以动画的。

设置图层的背景颜色和边框:

```
myLayer.backgroundColor = [NSColor greenColor].CGColor;
myLayer.borderColor = [NSColor blackColor].CGColor;
myLayer.borderWidth = 3.0;
```

如果您将图层的背景色设置为不透明颜色，请考虑将图层的不透明属性设置为YES。这样可以在合成屏幕图层时提高性能，并且不需要图层的后台存储来管理Alpha通道。不过，如果图层也具有非零的圆角半径，则不得将图层标记为不透明。

##### 图层支持角半径

您可以通过为其添加角半径来为您的图层创建一个圆角矩形效果。圆角半径是一种视觉装饰，用于遮盖图层边界矩形的一部分角落，以允许底层内容显示。因为它涉及应用透明度蒙版，所以除非`masksToBounds`属性设置为YES，否则拐角半径不会影响图层的`content`属性中的图像。但是，角半径总是会影响图层背景颜色和边框的绘制方式。

图层上的角半径：![](/assets/layer_corner_radius_2x.png)要将角半径应用于图层，请指定图层的`cornerRadius`属性的值。您指定的半径值以点为单位并在显示之前应用于图层的所有四个角。

##### 图层支持内置阴影

CALayer类包含几个用于配置阴影效果的属性。阴影通过使其看起来好像在其底层内容上方浮动而增加了图层的深度。这是另一种视觉装饰，您可能会发现它在特定情况下对您的应用有用。通过图层，您可以控制阴影的颜色，相对于图层内容的位置，不透明度和形状。

默认情况下，图层阴影的不透明度值设置为0，这有效隐藏了阴影。将不透明度更改为非零值会导致Core Animation绘制阴影。因为默认情况下，阴影直接位于图层下方，所以您可能还需要更改阴影的偏移量，然后才能看到阴影。不过请记住，您为阴影指定的偏移量是使用图层的本地坐标系来应用的，这在iOS和OS X上是不同的。

对图层应用阴影：![](/assets/layer_shadows_2x.png)

向图层添加阴影时，阴影是图层内容的一部分，但实际上是延伸到图层的边界矩形之外。因此，如果为该图层启用了masksToBounds属性，则阴影效果会在边缘被剪切。如果图层包含任何透明内容，则可能会导致一种奇怪的效果，即直接位于图层下方的阴影部分仍然可见，但延伸到图层之外的部分不会。如果你想要一个阴影，但也想使用边界遮罩，你可以使用两层而不是一层。将蒙版应用于包含内容的图层，然后将该图层嵌入到启用了阴影效果的大小完全相同的第二个图层中。

##### 过滤器将视觉效果添加到OS X视图

### OS X视图的图层重绘策略影响性能

### 将自定义属性添加到图层

CAAnimation和CALayer类扩展了键值编码惯例以支持自定义属性。

### 打印图层支持视图的内容



