# 高级动画技巧

### 过渡动画支持对图层可见性的更改

过渡动画对象为图层创建动画视觉过渡。过渡对象最常见的用途是协调一个图层的显示和另一个图层的消失。与基于属性的动画不同，在属性动画中，动画会更改图层的一个属性，而过渡动画会操纵图层的缓存图像来创建视觉效果，而单独更改属性将很难或无法完成。标准类型的转换可让您执行显示，推送，移动或交叉淡入淡出动画。在OS X上，您还可以使用核心图像过滤器来创建使用其他类型效果的过渡，例如您设计的wipes，页面卷曲，涟漪或自定义效果。

要执行过渡动画，您需要创建一个`CATransition`对象并将其添加到转换中涉及的图层中。您可以使用过渡对象来指定要执行的过渡类型以及过渡动画的开始点和结束点。您也不需要使用整个过渡动画。过渡对象可让您指定动画时使用的开始和结束进度值。这些值可让您在中点处开始或结束动画。清单5-1显示了用于在两个视图之间创建动画推送转换的代码。在该示例中，`myView1`和`myView2`位于同一父视图中的相同位置，但只有`myView1`当前可见。push过渡会导致`myView1`向左滑出并淡入，直到`myView2`从右侧滑入并变为可见时隐藏。更新两个视图的隐藏属性可确保在动画结束时两个视图的可见性都是正确的。

清单5-1 动画iOS中两个视图之间的转换

```
CATransition* transition = [CATransition animation];
transition.startProgress = 0;
transition.endProgress = 1.0;
transition.type = kCATransitionPush;
transition.subtype = kCATransitionFromRight;
transition.duration = 1.0;
 
// Add the transition animation to both layers
[myView1.layer addAnimation:transition forKey:@"transition"];
[myView2.layer addAnimation:transition forKey:@"transition"];
 
// Finally, change the visibility of the layers.
myView1.hidden = YES;
myView2.hidden = NO;
```

当两个图层涉及相同的转换时，可以为两者使用相同的转换对象。使用相同的转换对象还可以简化必须编写的代码。但是，您可以使用不同的转换对象，并且如果每个图层的转换参数不同，则肯定会这样做。

清单5-2 显示了如何使用Core Image过滤器在OS X上实现过渡效果。使用所需参数配置过滤器后，将其分配给过渡对象的`filter`属性。之后，应用动画的过程与其他类型的动画对象相同。

清单5-2 使用核心图像过滤器为OS X上的转换设置动画效果

```
// Create the Core Image filter, setting several key parameters.
CIFilter* aFilter = [CIFilter filterWithName:@"CIBarsSwipeTransition"];
[aFilter setValue:[NSNumber numberWithFloat:3.14] forKey:@"inputAngle"];
[aFilter setValue:[NSNumber numberWithFloat:30.0] forKey:@"inputWidth"];
[aFilter setValue:[NSNumber numberWithFloat:10.0] forKey:@"inputBarOffset"];
 
// Create the transition object
CATransition* transition = [CATransition animation];
transition.startProgress = 0;
transition.endProgress = 1.0;
transition.filter = aFilter;
transition.duration = 1.0;
 
[self.imageView2 setHidden:NO];
[self.imageView.layer addAnimation:transition forKey:@"transition"];
[self.imageView2.layer addAnimation:transition forKey:@"transition"];
[self.imageView setHidden:YES];
```

### 自定义动画的时间

### 暂停和恢复动画

### 显式事务让您更改动画参数

### 为您的动画添加透视图 

  


