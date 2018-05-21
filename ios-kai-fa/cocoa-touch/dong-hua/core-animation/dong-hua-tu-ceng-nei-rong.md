# 动画图层内容（Animating Layer Content）

Core Animation提供的基础架构可以轻松创建应用图层的复杂动画，并扩展到拥有这些图层的任何视图。示例包括更改图层 frame 矩形的大小，更改其在屏幕上的位置，应用旋转变换或更改其不透明度。使用Core Animation，启动动画通常就像更改属性一样简单，但您也可以创建动画并明确设置动画参数。

### 动画对图层属性进行简单更改

您可以根据需要隐式或显式执行简单的动画。隐式动画使用默认的时间和动画属性来执行动画，而明确的动画要求您使用动画对象自己配置这些属性。因此，隐式动画对于想要在没有大量代码的情况下进行更改的情况非常适用，并且默认计时适用于您。

简单动画涉及更改图层的属性，并让Core Animation随着时间的推移对这些更改进行动画处理。图层定义了许多影响图层可见外观的属性。改变这些属性之一是动画改变外观的一种方法。例如，将图层的不透明度从1.0更改为0.0会导致图层淡出并变为透明。

要触发隐式动画，您只需更新图层对象的属性即可。在修改图层树中的图层对象时，这些对象会立即反映您的更改。但是，图层对象的视觉外观不会立即改变。相反，核心动画会使用您的更改作为触发器来创建和调度一个或多个隐式动画以供执行。因此，下面那样进行更改会导致Core Animation为您创建一个动画对象，并安排该动画从下一个更新周期开始运行。

隐式动画化变更：

```
theLayer.opacity = 0.0;
```

要使用动画对象显式进行相同的更改，请创建一个`CABasicAnimation`对象并使用该对象来配置动画参数。在将动画添加到图层之前，您可以设置动画的开始和结束值，更改持续时间或更改任何其他动画参数。清单3-2显示了如何使用动画对象淡出图层。创建对象时，需要为要设置动画的属性指定关键路径，然后设置动画参数。要执行动画，可以使用addAnimation：forKey：方法将其添加到要进行动画处理的图层中。

清单3-2显式地为变化设置动画:

```
CABasicAnimation* fadeAnim = [CABasicAnimation animationWithKeyPath:@"opacity"];
fadeAnim.fromValue = [NSNumber numberWithFloat:1.0];
fadeAnim.toValue = [NSNumber numberWithFloat:0.0];
fadeAnim.duration = 1.0;
[theLayer addAnimation:fadeAnim forKey:@"opacity"];

// Change the actual data value in the layer to the final value.
theLayer.opacity = 0.0;
```

> 提示：在创建显式动画时，建议您始终为动画对象的fromValue属性指定一个值。如果您未为此属性指定值，则Core动画将该图层的当前值用作起始值。如果您已经将该属性更新为其最终值，那可能不会产生您想要的结果。

与更新图层对象的数据值的隐式动画不同，显式动画不会修改图层树中的数据。显式动画只会产生动画。在动画结束时，Core Animation从该图层中移除该动画对象，并使用其当前数据值重绘该图层。如果您希望显式动画的更改为永久性的，则还必须更新图层的属性，如上例所示。

隐式和显式动画通常在当前运行循环结束后开始执行，并且当前线程必须具有运行循环才能执行动画。如果更改多个属性，或者将多个动画对象添加到图层，则所有这些属性更改都会同时进行动画。例如，您可以通过同时配置两个动画来淡入淡出图层。不过，您也可以将动画对象配置为在特定时间开始。

### 使用关键帧动画更改图层属性

尽管基于属性的动画将属性从开始值更改为结束值，但CAKeyframeAnimation对象允许您通过一组可能或可能不是线性的方式来设置一组目标值的动画。关键帧动画由一组目标数据值和应达到每个值的时间组成。在最简单的配置中，您可以使用数组指定值和时间。对于图层位置的更改，也可以按照路径进行更改。动画对象采用您指定的关键帧，并通过在给定时间段内从一个值插入到下一个值来构建动画。

图3-1显示了图层`position`属性的5秒动画。位置动画为遵循一条路径，该路径使用`CGPathRef`数据类型指定。清单3-3显示了这个动画的代码。

清单3-3显示了图3-1中用于实现动画的代码。本示例中的路径对象用于为动画的每个帧定义图层的位置。

图3-1图层位置属性的5秒关键帧动画：![](/assets/keyframing_2x.png)清单3-3 创建一个反弹关键帧动画：

```
// create a CGPath that implements two arcs (a bounce)
CGMutablePathRef thePath = CGPathCreateMutable();
CGPathMoveToPoint(thePath,NULL,74.0,74.0);
CGPathAddCurveToPoint(thePath,NULL,74.0,500.0,
                                   320.0,500.0,
                                   320.0,74.0);
CGPathAddCurveToPoint(thePath,NULL,320.0,500.0,
                                   566.0,500.0,
                                   566.0,74.0);

CAKeyframeAnimation * theAnimation;

// Create the animation object, specifying the position property as the key path.
theAnimation=[CAKeyframeAnimation animationWithKeyPath:@"position"];
theAnimation.path=thePath;
theAnimation.duration=5.0;

// Add the animation to the layer.
[theLayer addAnimation:theAnimation forKey:@"position"];
```

##### 指定关键帧值

关键帧值是关键帧动画中最重要的部分。这些值定义了动画在其执行过程中的行为。指定关键帧值的主要方式是作为一个对象数组，但对于包含`CGPoint`数据类型的值（例如图层的`anchorPoint`和`position`属性），您可以指定`CGPathRef`数据类型。

在指定数组值时，放入数组的内容取决于属性所需的数据类型。你放入数组取决于属性所需的数据类型。您可以直接将一些对象添加到数组中;然而，在添加之前，必须将某些对象转换为id，并且所有标量类型或结构都必须由对象包装。例如：

* 对于采用`CGRect`的属性（例如边界和框架属性），将每个矩形包装在一个`NSValue`对象中。

* 对于图层的`transform`属性，将每个`CATransform3D`矩阵包装在一个`NSValue`对象中。为此属性设置动画会导致关键帧动画将每个变换矩阵依次应用于图层。

* 对于`borderColor`属性，将每个`CGColorRef`数据类型转换为类型标识，然后将其添加到数组中。

* 对于具有`CGFloat`值的属性，请将每个值包含在`NSNumber`对象中，然后再将其添加到数组中。

* 动画化图层的`content`属性时，请指定`CGImageRef`数据类型的数组。

对于采用`CGPoint`数据类型的属性，可以创建一个点数组（包装在`NSValue`对象中），或者可以使用`CGPathRef`对象指定要遵循的路径。指定点数组时，关键帧动画对象在每个连续点之间绘制一条直线并沿着该路径。当您指定`CGPathRef`对象时，动画将从路径的起点开始沿着其轮廓，包括沿着任何曲面。您可以使用打开或关闭的路径。

##### 指定关键帧动画的时间

关键帧动画的时间安排和步调比基本动画更复杂，您可以使用几个属性来控制它：

* `calculateMode`属性定义用于计算动画定时的算法。此属性的值会影响其他与时间相关的属性的使用方式。

  * 线性和立方体动画 - 也就是其中`calculateMode`属性设置为kCAAnimationLinear或`kCAAnimationCubic` 的动画。

  * Paced动画 - 也就是说，`calculateMode`属性设置为`kCAAnimationPaced`或`kCAAnimationCubicPaced`的动画，不依赖于keyTimes或timingFunctions属性提供的外部时间值。相反，定时值是隐式计算的，以便为动画提供恒定的速度。

  * 离散动画 - 也就是其中`calculateMode`属性设置为`kCAAnimationDiscrete`的动画。导致动画属性从一个关键帧值跳到下一个没有任何插值。计算模式使用`keyTimes`属性中的值，但忽略了`timingFunctions`属性。

* `keyTimes`属性指定应用每个关键帧值的时间标记。仅当计算模式设置为`kCAAnimationLinear`，`kCAAnimationDiscrete`或`kCAAnimationCubic`时才使用此属性。它不适用于节奏（Paced）动画。

* `timingFunctions`属性指定用于每个关键帧段的时间曲线。（该属性替换继承的`timingFunction`属性。）

如果您想自己处理动画计时，请使用`kCAAnimationLinear`或`kCAAnimationCubic`模式以及`keyTimes`和`timingFunctions`属性。`keyTimes`定义应用每个关键帧值的时间点。所有中间值的时间由定时功能控制，这允许您将缓入或缓出曲线应用到每个段。如果您没有指定任何定时功能，则定时是线性的。

### 停止正在运行的显式动画

### 使多个改变一起动画

### 检测动画的结束

### 如何动画图层支持的视图



