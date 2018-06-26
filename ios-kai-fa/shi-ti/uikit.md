# UIKit

##### 1、UICollectionView 自定义 layout 如何实现？

实现一个自定义layout的常规做法是继承UICollectionViewLayout类，然后重载下列方法：

```
-(CGSize)collectionViewContentSize
返回collectionView的内容的尺寸

-(NSArray *)layoutAttributesForElementsInRect:(CGRect)rect
返回rect中的所有的元素的布局属性
返回的是包含UICollectionViewLayoutAttributes的NSArray
UICollectionViewLayoutAttributes可以是cell，追加视图或装饰视    图的信息，通过不同的UICollectionViewLayoutAttributes初始化方法可以得到不同类型的UICollectionViewLayoutAttributes：
   layoutAttributesForCellWithIndexPath:
   layoutAttributesForSupplementaryViewOfKind:withIndexPath:
layoutAttributesForDecorationViewOfKind:withIndexPath:

  -(UICollectionViewLayoutAttributes )layoutAttributesForItemAtIndexPath:(NSIndexPath )indexPath
返回对应于indexPath的位置的cell的布局属性

-(UICollectionViewLayoutAttributes )layoutAttributesForSupplementaryViewOfKind:(NSString )kind atIndexPath:(NSIndexPath *)indexPath
返回对应于indexPath的位置的追加视图的布局属性，如果没有追加视图可不重载

-(UICollectionViewLayoutAttributes * )layoutAttributesForDecorationViewOfKind:(NSString)decorationViewKind atIndexPath:(NSIndexPath )indexPath
返回对应于indexPath的位置的装饰视图的布局属性，如果没有装饰视图可不重载

-(BOOL)shouldInvalidateLayoutForBoundsChange:(CGRect)newBounds
当边界发生改变时，是否应该刷新布局。如果YES则在边界变化（一般是scroll到其他地方）时，将重新计算需要的布局信息。
```

##### 2、用 Storyboard 开发界面有什么弊端？如何避免？

使用简单逻辑页面的跳转是可以使用sb的，开发比较块。

但是SB对于逻辑项目比较复杂的时候，开发起来比较慢。不适合多人合作开发；也不利于版本的梗系和后期的维护。使用sb在项目编译的时候，也都会直接加载到内存中，造成内存的浪费。

可以使用xib来代替，编辑复杂逻辑界面时候可以使用纯码编写。



