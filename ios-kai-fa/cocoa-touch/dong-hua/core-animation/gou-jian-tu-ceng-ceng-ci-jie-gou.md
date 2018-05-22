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

##### 定位和调整子图层的大小

##### 图层层次结构如何影响动画

### 调整图层层次结构的布局

### 子图层和剪切

### 转换图层之间的坐标值

  


  




