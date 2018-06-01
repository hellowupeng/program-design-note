# 创建自定义手势识别器

要实现自定义手势识别器，首先在Xcode中创建UIGestureRecognizer的子类。然后，在你的子类的头文件中添加下面的import指令：

```
#import <UIKit/UIGestureRecognizerSubclass.h>
```

接下来，将以下来自UIGestureRecognizerSubclass.h的方法声明复制到您的头文件中; 这些是您在子类中重写的方法：

```
- (void)reset;
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event;
- (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event;
- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event;
- (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event;
```

### 实现自定义手势的触摸事件处理方法

### 重置手势识别器状态

如果您的手势识别器转换为Recognized / Ended，Canceled或Failed，则UIGestureRecognizer类会在手势识别器转换回Possible之前调用重置方法。

重置手势识别器：

```
- (void)reset {
    [super reset];
    self.midPoint = CGPointZero;
    self.strokeUp = NO;
}
```





