# 核心动画基础（Core Animation Basics）

Core Animation 为动画应用的视图和其他视觉元素提供了一个通用系统。核心动画不能取代你的应用程序的视图。相反，它是一种与视图集成的技术，可以为动画制作内容提供更好的性能和支持。它通过将视图内容缓存到可由图形硬件直接操作的位图来实现此行为。在某些情况下，这种缓存行为可能需要您重新考虑如何呈现和管理您的应用内容，但大多数情况下，您使用 Core Animation 时并不知道它存在。除了缓存视图内容外，Core Animation 还定义了一种指定任意可视内容的方法，将该内容与视图集成，并与其他所有内容一起进行动画制作。

您可以使用核心动画为您的应用的视图和可视对象设置动画效果。大多数更改与修改视觉对象的属性有关。例如，您可以使用核心动画来为视图的位置，大小或不透明度设置动画效果。当您进行这样的更改时，Core Animation 会在属性的当前值和您指定的新值之间进行动画处理。您通常不会使用 Core Animation 以每秒60次的速度替换视图的内容，例如卡通内容。相反，您使用 Core Animation 将视图的内容移动到屏幕上，淡入淡出内容，将任意图形转换应用于视图或更改视图的其他视觉属性。

### 图层（Layer）为绘画和动画提供基础

图层对象是组织在3D空间中的2D表面，是 Core Animation 所做的一切的核心。与视图一样，图层管理有关其表面的几何图形，内容和视觉属性的信息。与视图不同，图层不定义自己的外观。一个图层只管理位图周围的状态信息。位图本身可以是视图绘制本身或您指定的固定图像的结果。出于这个原因，您在应用程序中使用的主要图层被视为模型对象，因为它们主要管理数据。这个概念很重要，因为它会影响动画的行为

##### 基于图层的绘图模型

大多数图层不会在您的应用中执行任何实际的绘图。相反，图层会捕获应用程序提供的内容并将其缓存在位图中，该位图有时称为后备存储。当您随后更改图层的属性时，您所做的只是更改与图层对象关联的状态信息。当更改触发动画时，Core Animation将图层的位图和状态信息传递给图形硬件，图形硬件执行使用新信息渲染位图的工作。

Core Animation 如何绘制内容：![](/assets/how core animation draws content.png)由于它操纵静态位图，因此基于图层的绘图与传统的基于视图的绘图技术有很大不同。使用基于视图的绘图时，对视图本身的更改通常会导致调用视图的 drawRect：方法以使用新参数重绘内容。但以这种方式绘制是昂贵的，因为它使用主线程上的 CPU 来完成。只要可能，通过在硬件中操作缓存的位图来实现相同或类似的效果，Core Animation 就可以避免这种开销。

尽管 Core Animation 尽可能使用缓存内容，但您的应用程序仍必须提供初始内容并不时进行更新。

##### 基于图层的动画

图层对象的数据和状态信息与屏幕上该图层内容的可视化表示分离。这种解耦为 Core Animation 提供了一种插入自身的方法，并使从旧状态值到新状态值的变化动画化。例如，更改图层的位置属性会使 Core Animation 将图层从其当前位置移动到新指定的位置。对其他属性的类似更改会导致适当的动画。

可以在图层上执行的动画示例:![](/assets/Examples of animations you can perform on layers.png)  
在动画过程中，Core Animation会以硬件完成所有逐帧绘制。您只需指定动画的开始点和结束点，然后让 Core Animation 完成剩下的动作。您还可以根据需要指定自定义时间信息和动画参数;但是，如果您不这样做，Core Animation 会提供合适的默认值。

### 图层对象定义他们自己的几何图形

一个图层的工作之一是管理其内容的视觉几何图形。视觉几何包含有关内容边界，屏幕位置以及图层是否以任何方式旋转，缩放或变形的信息。与视图类似，图层具有可用于定位图层及其内容的框架和边界矩形。图层还具有视图不具有的其他属性，例如定义操作发生点的定位点（anchor point）。您指定图层几何体的某些方面的方式也与您为视图指定该信息的方式不同。

##### 图层使用两种类型的坐标系统

图层利用基于点的坐标系和单位坐标系来指定内容的位置。使用哪个坐标系取决于传送的信息的类型。指定直接映射到屏幕坐标的值时必须使用基于点的坐标，或者必须指定相对于其他图层的值（例如图层的位置属性）。当值不应与屏幕坐标相关时使用单位坐标，因为它与其他值相关。例如，图层的anchorPoint属性指定了一个相对于图层本身边界的点，它可以改变。

基于点的坐标最常见的用途是指定图层的大小和位置，您可以使用图层的边界（`bounds`）和位置（`position`）属性进行确定。边界\(bounds\)定义了图层本身的坐标系，并包含了图层在屏幕上的大小。position 属性定义了图层相对于其父坐标系的位置。尽管图层具有 `frame` 属性，但该属性实际上是从 `bounds` 和 `position` 属性中的值导出的，并且使用频率较低。

图层 bounds 和 frame 矩形的方向始终与底层平台的默认方向匹配。在 iOS 中，默认情况下，边界矩形的原点位于图层的左上角，在 OS X 中，它位于左下角。

iOS 和 OS X 的默认图层几何图形:![](/assets/The default layer geometries for iOS and OS X.png)要注意的一点是 position 属性位于图层的中间。该属性是其中几个定义基于该图层的 anchorPoint 属性中的值而更改的属性之一。

定位点（anchor point）是您使用单位坐标系指定的几个属性之一。您可以将单位坐标视为指定总可能值的百分比。单位坐标空间中的每个坐标的范围为0.0到1.0。

iOS 和 OS X 的默认单位坐标系:![](/assets/The default unit coordinate systems for iOS and OS X.png)所有坐标值，无论是点还是单位坐标都被指定为浮点数。浮点数的使用允许您指定可能落在正常坐标值之间的精确位置。使用浮点值很方便，特别是在打印过程中或在绘制视网膜显示时，其中一个点可能由多个像素表示。浮点值允许您忽略基础设备分辨率，并以您需要的精度指定值。

##### 锚点（Anchor Point）影响几何操作

与图层的几何相关的操作相对于该图层的锚点发生，您可以使用图层的 anchorPoint 属性访问该锚点。处理图层的 position 或 transform 属性时，锚点的影响最为明显。位置属性始终是相对于图层的锚点指定的，并且您应用于该图层的任何转换也会相对于锚点发生。

锚点如何影响图层的位置属性:![](/assets/layer_coords_anchorpoint_position_2x.png)锚点如何影响层转换:

##### 层可以在三个维度操纵

每个图层都有两个转换矩阵，您可以使用它们来操纵图层及其内容。CALayer 的 transform 属性指定要应用于图层及其嵌入子图层的变换。通常当你想修改图层本身时使用这个属性。例如，您可以使用该属性缩放或旋转图层或临时更改其位置。sublayerTransform 属性定义了仅应用于子图层的其他转换，并且最常用于将透视视觉效果添加到场景内容。

变换的工作方式是将坐标值乘以数字矩阵以获得代表原始点变换版本的新坐标。由于Core Animation值可以在三维中指定，每个坐标点有四个值，必须乘以四乘四矩阵。在 Core Animation 中，图中的变换由 CATransform3D 类型表示。幸运的是，您不必直接修改此结构的字段以执行标准转换。核心动画提供了一套全面的功能，用于创建比例，平移和旋转矩阵以及进行矩阵比较。除了使用函数来操作变换外，Core Animation还扩展了键值编码支持，允许您使用关键路径修改变换。

使用矩阵数学转换坐标：![](/assets/transform_basic_math_2x.png)通过标识变换乘以任何坐标返回完全相同的坐标。对于其他转换，坐标如何修改完全取决于您更改哪个矩阵组件。例如，要仅沿x轴平移，您将为平移矩阵的 tx 分量提供非零值，并将 ty 和 tz 值保留为0。对于旋转，您可以提供目标旋转角度的合适的正弦和余弦值。

常见转换的矩阵配置：![](/assets/transform_manipulations_2x.png)

### 图层树反映了动画状态的不同方面

使用Core Animation的应用程序有三组图层对象。每一组图层对象在使您的应用程序的内容出现在屏幕上时具有不同的作用：

* 模型图层树中的对象（或简称为“图层树”）是您的应用与最多的对象交互的对象。此树中的对象是存储任何动画的目标值的模型对象。无论何时更改图层的属性，都可以使用这些对象之一。

* 表示树中的对象包含任何正在运行的动画的空中值。尽管图层树对象包含动画的目标值，但表示树中的对象反映了屏幕上显示的当前值。您绝不应该修改此树中的对象。相反，您可以使用这些对象来读取当前的动画值，也许可以从这些值开始创建一个新的动画。

* 渲染树中的对象执行实际的动画，并且对于 Core Animation 是私有的。

每组图层对象都组织成一个层次结构，就像您的应用中的视图一样。事实上，对于为所有视图启用图层的应用程序，每个树的初始结构都与视图层次结构完全匹配。但是，应用程序可以根据需要将其他图层对象（即未与视图关联的图层）添加到图层分层结构中。您可以在某些情况下执行此操作，以优化应用程序对不需要视图所有开销的内容的性能。

与窗口关联的图层：![](/assets/sublayer_hierarchy_2x.png)窗口的图层树：![](/assets/sublayer_hierarchies_2x.png)

### 层与视图的关系

图层不能代替您的应用视图 - 也就是说，您无法仅基于图层对象创建可视界面。图层为您的视图提供基础设施。具体而言，图层可以更轻松，更高效地绘制视图内容并进行动画处理，并在此过程中保持较高的帧速率。但是，有很多层没有做的事情。图层不处理事件，绘制内容，参与响应者链或做许多其他事情。因此，每个应用程序都必须有一个或多个视图来处理这些交互。

在 iOS中，每个视图都由相应的图层对象支持，但在 OS X 中，您必须确定哪些视图应该具有图层。  
当为视图启用图层支持时，您将创建被称为图层支持的视图。所有iOS视图都是图层支持的。

> 注意：对于层次支持的视图，建议尽可能操作视图而不是图层。在iOS中，视图只是图层对象的薄包装，因此对图层进行的任何操作通常都可以正常工作。

###  

  


  


