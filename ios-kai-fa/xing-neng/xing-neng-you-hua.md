# 性能优化总结

### 卡顿产生的原因

![](/assets/卡顿产生的原因.png)

在VSync信号到来后，系统图形服务会通过CADisplayLink等机制通知App，App主线程开始在CPU中计算显示内容，比如视图的创建、布局计算、图片解码、文本绘制等。随后CPU会将计算好的内容提交到GPU去，由GPU进行变换、合成、渲染。随后GPU会把渲染结果提交到帧缓冲区去，等待下一次VSync信号到来时显示到屏幕上。由于垂直同步的机制，如果在一个VSync时间内，CPU或者GPU没有完成内容提交，则那一帧就会被丢弃，等待下一次机会再显示，而这时显示屏会保留之前的内容不变。这就是界面卡顿的原因。

在开发中，CPU和GPU中任何一个压力过大，都会导致掉帧现象，所以在开发时，也需要分别对CPU和GPU压力进行评估和优化。

### iOS 设备中的 CPU 和 GPU

##### CPU

加载资源，对象创建，对象调整，对象销毁，布局计算，Autolayout，文本计算，文本渲染，图片的解码， 图像的绘制（Core Graphics）都是在CPU上面进行的。

##### GPU

GPU是一个专门为图形高并发计算而量身定做的处理单元，比CPU使用更少的电来完成工作并且GPU的浮点计算能力要超出CPU很多。

GPU的渲染性能要比CPU高效很多，同时对系统的负载和消耗也更低一些，所以在开发中，

**我们应该尽量让CPU负责主线程的UI调动，把图形显示相关的工作交给GPU来处理。**

相对于CPU来说，GPU能干的事情比较单一：接收提交的纹理（Texture）和顶点描述（三角形），应用变换（transform）、混合（合成）并渲染，然后输出到屏幕上。通常你所能看到的内容，主要也就是纹理（图片）和形状（三角模拟的矢量图形）两类。

##### CPU 和 GPU 的协作

![](/assets/cpu和gpu的协作.png)

由上图可知，要在屏幕上显示视图，需要CPU和GPU一起协作，CPU计算好显示的内容提交到GPU，GPU渲染完成后将结果放到帧缓存区，随后视频控制器会按照VSync信号逐行读取帧缓冲区的数据，经过可能的数模转换传递给显示器显示。

##### 缓冲机制

![](/assets/iOS缓冲机制.png)

iOS使用的是双缓冲机制。即GPU会预先渲染好一帧放入一个缓冲区内（前帧缓存），让视频控制器读取，当下一帧渲染好后，GPU会直接把视频控制器的指针指向第二个缓冲器（后帧缓存）。当你视频控制器已经读完一帧，准备读下一帧的时候，GPU会等待显示器的VSync信号发出后，前帧缓存和后帧缓存会瞬间切换，后帧缓存会变成新的前帧缓存，同时旧的前帧缓存会变成新的后帧缓存。

### 优化方案

##### TableViewCell 复用

在cellForRowAtIndexPath:回调的时候只创建实例，快速返回cell，不绑定数据。在willDisplayCell: forRowAtIndexPath:的时候绑定数据（赋值）。

##### 高度缓存

在tableView滑动时，会不断调用heightForRowAtIndexPath:，当cell高度需要自适应时，每次回调都要计算高度，会导致 UI 卡顿。为了避免重复无意义的计算，需要缓存高度。

###### 怎么缓存？

* 字典，NSCache
* [UITableView-FDTemplateLayoutCell](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fforkingdog%2FUITableView-FDTemplateLayoutCell)

##### 视图层级优化

###### 不要动态创建视图

* 在内存可控的前提下，缓存subview。

* 善用hidden。

###### 减少视图层级

* 减少subviews个数，用layer绘制元素。
* 少用clearColor，maskToBounds，阴影效果等。

##### 减少多余的绘制操作

###### 图片

* 不要用JPEG的图片，应当使用PNG图片。

* 子线程预解码（Decode），主线程直接渲染。因为当image没有Decode，直接赋值给imageView会进行一个Decode操作。

* 优化图片大小，尽量不要动态缩放\(contentMode\)。

* 尽可能将多张图片合成为一张进行显示。

##### 减少透明 view

使用透明view会引起blending，在iOS的图形处理中，blending主要指的是混合像素颜色的计算。最直观的例子就是，我们把两个图层叠加在一起，如果第一个图层的透明的，则最终像素的颜色计算需要将第二个图层也考虑进来。这一过程即为Blending。

会导致blending的原因：

* UIView的alpha &lt; 1

* UIImageView的image含有alpha channel（即使UIImageView的alpha是1，但只要image含有透明通道，则仍会导致blending）。

> **为什么blending会导致性能的损失？**
>
> 原因是很直观的，如果一个图层是不透明的，则系统直接显示该图层的颜色即可。而如果图层是透明的，则会引起更多的计算，因为需要把另一个的图层也包括进来，进行混合后的颜色计算。

* opaque设置为YES，减少性能消耗，因为GPU将不会做任何合成，而是简单从这个层拷贝。

##### 减少离屏渲染

离屏渲染指的是在图像在绘制到当前屏幕前，需要先进行一次渲染，之后才绘制到当前屏幕。

OpenGL中，GPU屏幕渲染有以下两种方式：

* On-Screen Rendering即当前屏幕渲染，指的是GPU的渲染操作是在当前用于显示的屏幕缓冲区中进行。

* Off-Screen Rendering即离屏渲染，指的是GPU在当前屏幕缓冲区以外新开辟一个缓冲区进行渲染操作。

为什么离屏渲染会发生卡顿？主要包括两方面内容：

* 创建新的缓冲区。

* 上下文切换，离屏渲染的整个过程，需要多次切换上下文环境（CPU渲染和GPU切换），先是从当前屏幕（On-Screen）切换到离屏（Off-Screen）；等到离屏渲染结束以后，将离屏缓冲区的渲染结果显示到屏幕上又需要将上下文环境从离屏切换到当前屏幕。而上下文环境的切换是要付出很大代价的。

设置了以下属性时，都会触发离屏渲染：

* layer.shouldRasterize，光栅化

* layer.mask，遮罩

* layer.allowsGroupOpacity为YES，layer.opacity的值小于1.0

* layer.cornerRadius，并且设置layer.masksToBounds为YES。可以使用剪切过的图片，或者使用layer画来解决。

* layer.shadows，\(表示相关的shadow开头的属性\)，使用shadowPath代替。

  两种不同方式来绘制阴影：

  不使用shadowPath![](/assets/不使用shadowPath.png)使用shadowPath：![](/assets/使用shadowPath.png)性能差别，如下图：![](/assets/阴影性能差别.png)

###### 离屏渲染的优化建议

* 使用ShadowPath指定layer阴影效果路径。

* 使用异步进行layer渲染（Facebook开源的异步绘制框架AsyncDisplayKit）。

* 设置layer的opaque值为YES，减少复杂图层合成。

* 尽量使用不包含透明（alpha）通道的图片资源。

* 尽量设置layer的大小值为整形值。

* 直接让美工把图片切成圆角进行显示，这是效率最高的一种方案。

* 很多情况下用户上传图片进行显示，可以在客户端处理圆角。

* 使用代码手动生成圆角image设置到要显示的View上，利用UIBezierPath（Core Graphics框架）画出来圆角图片。

###### 合理使用光栅化 shouldRasterize

光栅化是把GPU的操作转到CPU上，生成位图缓存，直接读取复用。

优点：

* CALayer会被光栅化为bitmap，shadows、cornerRadius等效果会被缓存。

缺点：

* 更新已经光栅化的layer，会造成离屏渲染。

* bitmap超过100ms没有使用就会移除。

* 受系统限制，缓存的大小为 2.5X Screen Size。

**shouldRasterize适合静态页面显示，动态页面会增加开销。如果设置了shouldRasterize为YES，那也要记住设置rasterizationScale为contentsScale。**

###### 异步渲染

在子线程绘制，主线程渲染。例如 [VVeboTableViewDemo](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fjohnil%2FVVeboTableViewDemo)![](/assets/异步渲染.png)

##### 理性使用-drawRect:

大家或许感到奇怪，有不少开发者在发有关性能优化的博客当中指出使用-drawRect:来优化性能。但是我这里不太建议大家未经思考的使用-drawRect:方法。原因如下：

当你使用UIImageView在加载一个视图的时候，这个视图虽然依然有CALayer，但是却没有申请到一个后备的存储，取而代之的是使用一个使用屏幕外渲染，将CGImageRef作为内容，并用渲染服务将图片数据绘制到帧的缓冲区，就是显示到屏幕上，当我们滚动视图的时候，这个视图将会重新加载，浪费性能。所以对于使用-drawRect:方法，更倾向于使用CALayer来绘制图层。因为使用CALayer的-drawInContext:，Core Animation将会为这个图层申请一个后备存储，用来保存那些方法绘制进来的位图。那些方法内的代码将会运行在CPU上，结果将会被上传到GPU。这样做的性能更为好些。

静态界面建议使用-drawRect:的方式，动态页面不建议。

###### 按需加载

* 局部刷新，刷新一个cell就能解决的，坚决不刷新整个section或者整个tableView，刷新最小单元元素。

* 利用[runloop](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fdiwu%2FRunLoopWorkDistribution)提高滑动流畅性，在滑动停止的时候再加载内容，像那种一闪而过的（快速滑动），就没有必要加载，可以使用默认的占位符填充内容。

### [关于性能测试](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2F100mango%2Fzen%2Fblob%2Fmaster%2FWWDC心得：Advanced Graphics and Animations for iOS Apps%2FAdvanced Graphics and Animations for iOS Apps.md%23测试工具)

在出现图像性能问题，滑动，动画不够流畅之后，我们首先要做的就是定位出问题的所在。而这个过程并不是只靠经验和穷举法探索，我们应该用有脉络，有顺序的科学的手段进行探索。

首先，我们要有一个定位问题的模式。我们可以按照这样的顺序来逐步定位，发现问题。

1. 定位帧率，为了给用户流畅的感受，我们需要保持帧率在60帧左右。当遇到问题后，我们首先检查一下帧率是否保持在60帧。

2. 定位瓶颈，究竟是CPU还是GPU。我们希望占用率越少越好，一是为了流畅性，二也节省了电力。

3. 检查有没有做无必要的CPU渲染，例如有些地方我们重写了drawRect:，而其实是我们不需要也不应该的。我们希望GPU负责更多的工作。

4. 检查有没有过多的离屏渲染，这会耗费GPU的资源，像前面已经分析的到的。离屏渲染会导致GPU需要不断地onScreen和offscreen进行上下文切换。我们希望有更少的离屏渲染。

5. 检查我们有无过多的Blending，GPU渲染一个不透明的图层更省资源。

6. 检查图片的格式是否为常用格式，大小是否正常。如果一个图片格式不被GPU所支持，则只能通过CPU来渲染。一般我们在iOS开发中都应该用PNG格式，之前阅读过的一些资料也有指出苹果特意为PNG格式做了渲染和压缩算法上的优化。

7. 检查是否有耗费资源多的View或效果，我们需要合理有节制的使用。

8. 最后，我们需要检查在我们View层级中是否有不正确的地方。例如有时我们不断的添加或移除View，有时就会在不经意间导致bug的发生。

##### 测试工具：

* Core Animation，Instruments里的图形性能问题的测试工具。

* view debugging，Xcode 自带的，视图层级。

* reveal，视图层级。

### 参考文章

* [绘制像素到屏幕上](https://link.juejin.im/?target=https%3A%2F%2Fobjccn.io%2Fissue-3-1%2F)

* [iOS图形原理与离屏渲染](https://link.juejin.im/?target=http%3A%2F%2Fsonnewilling.com%2Fblog%2F2016%2F10%2F19%2Fiostu-xing-yuan-li-yu-chi-ping-xuan-ran%2F%23anchor1.1) 在1.4.1中，这也是为什么 CALayer 有一个叫做 opaque 的属性了。如果这个属性为 NO，GPU 将不会做任何合成，而是简单从这个层拷贝，不需要考虑它下方的任何东西\(因为都被它遮挡住了\)。中的opaque属性为NO，GPU将不会做任何合成，这句话时错误的，应该是为YES，GPU才不会做任何合成。

* [iOS 保持界面流畅的技巧](https://link.juejin.im/?target=https%3A%2F%2Fblog.ibireme.com%2F2015%2F11%2F12%2Fsmooth_user_interfaces_for_ios%2F)

* [Advanced Graphics and Animations for iOS Apps\(session 419\)](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2F100mango%2Fzen%2Fblob%2Fmaster%2FWWDC心得：Advanced Graphics and Animations for iOS Apps%2FAdvanced Graphics and Animations for iOS Apps.md)

* [使用 ASDK 性能调优 - 提升 iOS 界面的渲染性能](https://link.juejin.im/?target=https%3A%2F%2Fdraveness.me%2Fasdk-rendering)

* [Designing for iOS: Graphics & Performance](https://link.juejin.im/?target=https%3A%2F%2Frobots.thoughtbot.com%2Fdesigning-for-ios-graphics-performance)

* [iOS离屏渲染之优化分析](https://link.juejin.im/?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2F52c72f18e142)

* [iOS视图渲染以及性能优化总结](https://link.juejin.im/?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2Fb29c682679c4%3Futm_campaign%3Dmaleskine%26utm_content%3Dnote%26utm_medium%3Dseo_notes%26utm_source%3Drecommendation)

* [iOS 离屏渲染](https://link.juejin.im/?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2F1c80ccc01919%3Futm_campaign%3Dmaleskine%26utm_content%3Dnote%26utm_medium%3Dseo_notes%26utm_source%3Drecommendation)

* [深刻理解移动端优化之离屏渲染](https://link.juejin.im/?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2Fd74398c50fe1%3Futm_campaign%3Dmaleskine%26utm_content%3Dnote%26utm_medium%3Dseo_notes%26utm_source%3Drecommendation)

* [iOS 流畅度性能优化、CPU、GPU、离屏渲染](https://link.juejin.im/?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2Fd27323c18790%3Futm_campaign%3Dmaleskine%26utm_content%3Dnote%26utm_medium%3Dseo_notes%26utm_source%3Drecommendation)

* [iOS 图形性能优化锦集](https://link.juejin.im/?target=http%3A%2F%2Fwww.bijishequ.com%2Fdetail%2F268411)

* [离屏渲染优化详解：实例示范+性能测试](https://link.juejin.im/?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2Fca51c9d3575b)

###### 参考资料

1. [iOS 性能优化总结](http://www.cocoachina.com/ios/20180412/22990.html)



