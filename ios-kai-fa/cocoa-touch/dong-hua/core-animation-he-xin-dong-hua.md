# Core Animation

### 基本动画

* 使用 CABasicAnimation，实现一个动画

```
CABasicAnimation *animation = [CABasicAnimation animation];
animation.keyPath = @"position.x";
animation.fromValue = @77;
animation.toValue = @455;
animation.duration = 1;

使用 rocket 留在最终状态，设置 removeOnCompletion 为 No 以防止它被自动移除
animation.fillMode = kCAFillModeForword;
animation.removedOnCompletion = NO;

[rocket.layer addAnimation:animation forKey:@"basic"];
```

* 其中的animation.keyPath支持的键路径的完整列表：[https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation\_guide/Key-ValueCodingExtensions/Key-ValueCodingExtensions.html](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Key-ValueCodingExtensions/Key-ValueCodingExtensions.html)

* fromValue,byValue和toValue的不同组合实现不同效果可以查看：[https://developer.apple.com/library/ios/documentation/GraphicsImaging/Reference/CABasicAnimation\_class/Introduction/Introduction.html\#//apple\_ref/doc/uid/TP40004496-CH1-SW4](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Reference/CABasicAnimation_class/Introduction/Introduction.html#//apple_ref/doc/uid/TP40004496-CH1-SW4)

### 多步动画

使用CAKeyframeAnimation，实现一个动画：

```c
CAKeyframeAnimation *animation = [CAKeyframeAnimation animation];
animation.keyPath = @"position.x";;
animation.values = @[@0, @10, @-10, @10, @0];
animation.keyTimes = @[@0, @(1 / 6.0), @(3 / 6.0), @(5 / 6.0), @1];
animation.duration = 0.4;

animation.additive = YES;
[form.layer addAnimation:animation forKey:@"shake"];
```

### 沿路径动画

```
CGRect boundingRect = CGRectMake(-150, -150, 300, 300);

CAKeyframeAnimation *orbit = [CAKeyframeAnimation animation];
orbit.keyPath = @"position";
orbit.path = CFAutorelease(CGPathCreateWithEllipseInRect(boundingRect, NULL));
orbit.duration = 4;
orbit.additive = YES;
orbit.repeatCount = HUGE_VALF;
orbit.calculationMode = kCAAnimationPaced;
orbit.rotationMode = kCAAnimationRotateAuto;

[satellite.layer addAnimation:orbit forKey:@"orbit"];
```

### 时间函数

##### CAMediaTimingFunction

time function也可以称作easing函数，最简单的easing函数是linear。在Core Animation中这个功能由CAMediaTimingFunction 类表示

```
CABasicAnimation *animation = [CABasicAnimation animation];
animation.keyPath = @"position.x";
animation.fromValue = @50;
animation.toValue = @150;
animation.duration = 1;

animation.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionLinear];

[rectangle.layer addAnimation:animation forKey:@"basic"];

rectangle.layer.position = CGPointMake(150, 0);
```

Core Animation附带了一些linear之外的内置easing函数

* Ease in \(kCAMediaTimingFunctionEaseIn\)

* Ease out \(kCAMediaTimingFunctionEaseOut\)

* Ease in ease out \(kCAMediaTimingFunctionEaseInEaseOut\)

* 默认 \(kCAMediaTimingFunctionDefault\)

##### +functionWithControlPoints:::: 创建自己的 easing 函数

```
CABasicAnimation *animation = [CABasicAnimation animation];
animation.keyPath = @"position.x";
animation.fromValue = @77;
animation.toValue = @455;
animation.duration = 1;

animation.timingFunction = [CAMediaTimingFunction functionWithControlPoints:0.5:0:0.9:0.7];

[rocket.layer addAnimation:animation forKey:@"basic"];

rocket.layer.position = CGPointMake(150, 0);
```

### 动画组

复杂动画，同时为多个属性进行动画。同时对position,rotation和z-position进行动画。使用CAAnimationGroup

```
CABasicAnimation *zPosition = [CABasicAnimation animation];
zPosition.keyPath = @"zPosition";
zPosition.fromValue = @-1;
zPosition.toValue = @1;
zPosition.duration = 1.2;

CAKeyframeAnimation *rotation = [CAKeyframeAnimation animation];
rotation.keyPath = @"transform.rotation";
rotation.values = @[ @0, @0.14, @0 ];
rotation.duration = 1.2;
rotation.timingFunctions = @[
[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut],
[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut]
];

CAKeyframeAnimation *position = [CAKeyframeAnimation animation];
position.keyPath = @"position";
position.values = @[
[NSValue valueWithCGPoint:CGPointZero],
[NSValue valueWithCGPoint:CGPointMake(110, -20)],
[NSValue valueWithCGPoint:CGPointZero]
];
position.timingFunctions = @[
[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut],
[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut]
];
position.additive = YES;
position.duration = 1.2;

CAAnimationGroup *group = [[CAAnimationGroup alloc] init];
group.animations = @[ zPosition, rotation, position ];
group.duration = 1.2;
group.beginTime = 0.5;

[card.layer addAnimation:group forKey:@"shuffle"];

card.layer.zPosition = 1;
```

###### 参考

1. [Core Animation](https://github.com/ming1016/study/wiki/Core-Animation)



