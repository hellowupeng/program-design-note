# 阴影

阴影是在图形对象下面绘制并偏移的图像，以便阴影模仿光源在图形对象上的效果，如图所示。文字也可以被遮蔽。阴影可以使图像呈现三维效果，或者像浮动一样。

阴影效果：

![](/assets/A shadow.png)

阴影有三个特点：

* 一个x偏移量，它指定阴影偏离图像水平方向多远。

* y偏移量，用于指定阴影距图像偏移量在垂直方向上的距离。

* 一个模糊值，用于指定图像是否具有硬边缘（如图7-2左侧所示）或漫反射边缘（如图的右侧所示）。

图7-2一个没有模糊的阴影，另一个模糊的边缘：

![](/assets/A shadow with no blur and another with a soft edge.png)

### 阴影如何工作

Quartz中的阴影是图形状态\(graphics state\)的一部分。您可以调用函数`CGContextSetShadow`，传递图形上下文，偏移值和模糊值。置阴影后，您绘制的任何对象都会绘制阴影，并在设备RGB颜色空间中使用具有1/3 alpha值的黑色。

通过调用函数`CGContextSetShadowWithColor`，传递图形上下文，偏移值，模糊值和CGColor对象，可以绘制彩色阴影。为颜色提供的值取决于要绘制的颜色空间。

如果在调用`CGContextSetShadow`或`CGContextSetShadowWithColor`之前保存图形状态，则可以通过恢复图形状态来关闭阴影。您还可以通过将阴影颜色设置为`NULL`来禁用阴影。

### 基于上下文的阴影绘制惯例

前面所述的偏移指定阴影所在位置与投射阴影的图像相关。这些偏移由上下文解释并用于计算阴影的位置：

* 正x偏移表示阴影位于图形对象的右侧。

* 在Mac OS X中，正y偏移表示向上位移。这与Quartz 2D的默认坐标系相匹配。

* 在iOS中，如果您的应用程序使用Quartz 2D API创建PDF或位图上下文，则正y偏移表示向上位移。

* 在iOS中，如果图形上下文是由UIKit创建的，例如由UIView对象创建的图形上下文或通过调用`UIGraphicsBeginImageContextWithOptions`函数创建的上下文，那么正y偏移表示向下位移。这符合UIKit坐标系的绘图惯例。

* 阴影绘制约定不受当前变换矩阵的影响。

### 绘制阴影

按照以下步骤绘制阴影：

1. 保存图形状态。

2. 调用函数`CGContextSetShadow`，传递适当的值。

3. 执行所有您想要应用阴影的绘图。

4. 恢复图形状态。

按照以下步骤绘制彩色阴影：

1. 保存图形状态。

2. 创建一个`CGColorSpace`对象以确保Quartz正确解释阴影颜色值。

3. 创建一个指定要使用的阴影颜色的CGColor对象。

4. 调用函数`CGContextSetShadowWithColor`，传递适当的值。

5. 执行所有您想要应用阴影的绘图。

6. 恢复图形状态。

图7-3中的两个矩形用阴影绘制 - 一个带有彩色阴影。

![](/assets/A colored shadow and a gray shadow.png)

清单7-1中的函数显示了如何设置阴影以绘制图7-3中显示的矩形

清单7-1一个设置阴影的函数

```
void MyDrawWithShadows (CGContextRef myContext, // 1
                         CGFloat wd, CGFloat ht);
{
    CGSize          myShadowOffset = CGSizeMake (-15,  20);// 2
    CGFloat           myColorValues[] = {1, 0, 0, .6};// 3
    CGColorRef      myColor;// 4
    CGColorSpaceRef myColorSpace;// 5

    CGContextSaveGState(myContext);// 6

    CGContextSetShadow (myContext, myShadowOffset, 5); // 7
    // Your drawing code here// 8
    CGContextSetRGBFillColor (myContext, 0, 1, 0, 1);
    CGContextFillRect (myContext, CGRectMake (wd/3 + 75, ht/2 , wd/4, ht/4));

    myColorSpace = CGColorSpaceCreateDeviceRGB ();// 9
    myColor = CGColorCreate (myColorSpace, myColorValues);// 10
    CGContextSetShadowWithColor (myContext, myShadowOffset, 5, myColor);// 11
    // Your drawing code here// 12
    CGContextSetRGBFillColor (myContext, 0, 0, 1, 1);
    CGContextFillRect (myContext, CGRectMake (wd/3-75,ht/2-100,wd/4,ht/4));

    CGColorRelease (myColor);// 13
    CGColorSpaceRelease (myColorSpace); // 14

    CGContextRestoreGState(myContext);// 15
}
```

代码作用：

1. 需要三个参数 - 构建矩形时使用的图形上下文以及宽度和高度。

2. 声明并创建一个包含阴影偏移值的CGSize对象。这些值指定对象左侧15个单位的阴影偏移量和对象上方20个单位的阴影偏移量。

3. 声明一个颜色值的数组。这个例子使用了RGBA，但是这些值只有在它们被传递到Quartz以及Quartz正确解释值所必需的色彩空间之前，才会有任何意义。

4. 声明存储以获取颜色参考。

5. 声明存储的颜色空间参考。

6. 保存当前的图形状态，以便稍后恢复。

7. 将阴影设置为先前声明的偏移值，并将模糊值设置为5，这表示柔和的阴影边缘。

8. 接下来的两行代码绘制了图7-3右侧的矩形。你用自己的绘图代码替换这些行。

9. 创建设备RGB颜色空间。创建CGColor对象时，您需要提供一个颜色空间。

10. 创建一个CGColor对象，提供设备的RGB色彩空间和之前声明的RGBA值。该对象指定阴影颜色，在这种情况下，该颜色为红色，并且Alpha值为0.6。

11. 设置一个颜色阴影，提供刚刚创建的红色。阴影使用先前创建的偏移量和模糊值5，这表示柔和的阴影边缘。

12. 接下来的两行代码绘制图7-3左侧的矩形。你用自己的绘图代码替换这些行。

13. 释放颜色对象，因为它不再需要。

14. 释放颜色空间对象，因为它不再需要。

15. 将图形状态恢复到设置阴影之前的状态。



