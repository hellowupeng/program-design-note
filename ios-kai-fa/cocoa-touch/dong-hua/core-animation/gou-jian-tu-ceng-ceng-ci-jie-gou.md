# 构建图层层次结构

大多数情况下，在您的应用中使用图层的最佳方式是将它们与视图对象结合使用。但是，有时您可能需要通过向其添加附加图层对象来增强视图层次结构。这样做时可以使用图层提供更好的性能，或者让您实现仅凭视图难以处理的功能。在这些情况下，您需要知道如何管理您创建的图层层次结构。

### 安排图层到图层层次结构

图层层次结构在许多方面与视图层次结构相似。将一个图层嵌入另一个图层中，以在要嵌入的图层（称为子图层）和父图层（称为超级图层）之间创建父子关系。这种父子关系会影响子图层的各个方面。例如，其内容位于父级内容的上方，其位置是相对于其父级的坐标系指定的，并且受到应用于父级的任何变换的影响。

##### 添加，插入和删除子图层

每个图层对象都有添加，插入和删除子图层的方法。表4-1总结了这些方法及其行为。

表4-1修改图层层次结构的方法：

* 添加图层  
  [addSublayer:](https://developer.apple.com/documentation/quartzcore/calayer/1410833-addsublayer)

  将新的子图层对象添加到当前图层。子图层被添加到图层的子图层列表的末尾。这会导致子图层出现在其zPosition属性中具有相同值的任何兄弟的顶部。

* 插入图层

  [insertSublayer:above:](https://developer.apple.com/documentation/quartzcore/calayer/1410798-insertsublayer)、[insertSublayer:atIndex:](https://developer.apple.com/documentation/quartzcore/calayer/1410944-insertsublayer)、[insertSublayer:below:](https://developer.apple.com/documentation/quartzcore/calayer/1410840-insertsublayer)

  将子图层插入指定索引处的子图层次结构中或相对于另一子图层的位置处。当插入到另一个子图层的上方或下方时，只需指定子图层数组中的子图层的位置。图层的实际可见性主要由它们的`zPosition`属性中的值决定，其次由它们在子层数组中的位置决定。

* 移除图层

  [removeFromSuperlayer](https://developer.apple.com/documentation/quartzcore/calayer/1410767-removefromsuperlayer)

  从其父层删除子图层。

* 交换图层

  [replaceSublayer:with:](https://developer.apple.com/documentation/quartzcore/calayer/1410820-replacesublayer)

  为另一个交换一个子图层。如果您要插入的子图层已经在另一个图层层次结构中，则首先从该层次结构中删除它。

在使用自己创建的图层对象时，可以使用上述方法。不能使用这些方法来排列属于图层支持视图的图层。但是，图层支持的视图可以作为您自己创建的独立层的父层。

##### 定位和调整子图层的大小

添加和插入子图层时，必须在子图层出现在屏幕上之前设置子图层的大小和位置。您可以在将子图层添加到图层层次结构后修改其大小和位置，但应该在创建图层时设置这些值。使用`bounds`属性设置子图层的大小，并使用`position`属性在其父图层中设置其位置。bounds 矩形的原点几乎总是（0，0），并且大小与点中指定的图层的大小无关。`position` 属性中的值相对于图层的锚点（anchor point）进行解释，该锚点默认位于图层的中心。如果不为这些属性赋值，Core Animation会将图层的初始宽度和高度设置为0，并将位置设置为（0，0）。

```
myLayer.bounds = CGRectMake(0, 0, 100, 100);
myLayer.position = CGPointMake(200, 200);
```

##### 图层层次结构如何影响动画

某些父图层属性可能会影响应用于其子图层的任何动画的行为。其中一个属性是`speed`属性，它是动画速度的乘数。该属性的值默认设置为1.0，但将其更改为2.0会使动画以原始速度的两倍运行，从而在一半时间完成。该属性不仅影响其设置的图层，还影响该图层的子图层。这种变化也是可乘的。如果子图层和其父图层的速度均为2.0，则子图层上的动画将以其原始速度的四倍运行。

大多数其他图层更改以可预测的方式影响任何包含的子层。例如，将旋转变换应用到图层会旋转该图层及其所有子图层。同样，更改图层的不透明度会更改其子图层的不透明度。对图层大小的更改遵循调整图层层次结构的布局中所述的布局规则。

### 调整图层层次结构的布局

Core Animation 支持多种选项来调整子层的大小和位置以响应其父图层的变化。在iOS中，普遍使用图层支持的视图使得图层层次结构的创建不那么重要;只支持手动布局更新。对于OS X，可以使用其他几个选项来更容易地管理层次结构。

##### 使用约束来管理OS X中的图层层次结构

##### 为您的OS X图层分层结构设置自动调整规则

##### 手动布局您的图层层次结构

### 子图层和裁剪

与视图不同，父图层不会自动剪裁位于边框矩形外的子图层的内容。相反，父图层允许其子图层默认全部显示。但是，可以通过将图层的`masksToBounds`属性设置为`YES`来重新启用裁剪。

如果指定了图层的剪切蒙版，则其形状包含图层的角半径。图4-3显示了一个图层，演示了`masksToBounds`属性如何影响具有圆角的图层。当属性设置为`NO`时，即使子层延伸到其父层的边界之外，子层也会全部显示。将该属性更改为`YES`会导致其内容被裁剪。

图4-3 将子图层裁剪到父级边界![](/assets/Clipping sublayers to the parent’s bounds.png)

### 转换图层之间的坐标值

有时，您可能需要将一个图层中的坐标值转换为不同图层中同一屏幕位置处的坐标值。CALayer类提供了一组可以用于此目的的简单转换例程：

* [`convertPoint:fromLayer:`](https://developer.apple.com/documentation/quartzcore/calayer/1410825-convert)

* [`convertPoint:toLayer:`](https://developer.apple.com/documentation/quartzcore/calayer/1410881-convertpoint)

* [`convertRect:fromLayer:`](https://developer.apple.com/documentation/quartzcore/calayer/1410948-convert)

* [`convertRect:toLayer:`](https://developer.apple.com/documentation/quartzcore/calayer/1410742-convertrect)

除了转换点和矩形值之外，还可以使用`convertTime：fromLayer：`和`convertTime：toLayer：`方法在图层之间转换时间值。每个图层都定义了自己的本地时间空间，并使用该时间空间将动画的开始和结束与系统的其余部分同步。

