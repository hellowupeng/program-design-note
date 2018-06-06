# Objc Runtime

### 概述

Objc Runtime使得C具有了面向对象能力，在程序运行时创建，检查，修改类、对象和它们的方法。Runtime是C和汇编编写的，这里

[http://www.opensource.apple.com/source/objc4/](http://www.opensource.apple.com/source/objc4/)

可以下到苹果维护的开源代码，GNU也有一个开源的runtime版本，他们都努力的保持一致。

[苹果官方的Runtime编程指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008048)

#### Runtime 函数

Runtime系统是由一系列的函数和数据结构组成的公共接口动态共享库，在/usr/include/objc目录下可以看到头文件，可以用其中一些函数通过C语言实现Objective-C中一样的功能。苹果官方文档

[https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html)

里有详细的Runtime函数文档。

### Class 和 Object 基础数据结构

### 类和对象操作函数

### 成员变量与属性

### Method 和消息

### Protocol 和 Category

### Block

### Runtime 的应用

#### 获取系统提供的库相关信息

注意函数

```c
// 获取所有加载的Objective-C框架和动态库的名称
const char ** objc_copyImageNames ( unsigned int *outCount );

// 获取指定类所在动态库
const char * class_getImageName ( Class cls );

// 获取指定库或框架中所有类的类名
const char ** objc_copyClassNamesForImage ( const char *image, unsigned int *outCount );
```

通过这些函数就能够获取某个类所有的库，以及某个库中包含哪些类

```c
NSLog(@"获取指定类所在动态库");

NSLog(@"UIView's Framework: %s", class_getImageName(NSClassFromString(@"UIView")));

NSLog(@"获取指定库或框架中所有类的类名");
const char ** classes = objc_copyClassNamesForImage(class_getImageName(NSClassFromString(@"UIView")), &outCount);
for (int i = 0; i < outCount; i++) {
     NSLog(@"class name: %s", classes[i]);
}

//结果
2014-11-08 12:57:32.689 [747:184013] 获取指定类所在动态库
2014-11-08 12:57:32.690 [747:184013] UIView's Framework: /System/Library/Frameworks/UIKit.framework/UIKit
2014-11-08 12:57:32.690 [747:184013] 获取指定库或框架中所有类的类名
2014-11-08 12:57:32.691 [747:184013] class name: UIKeyboardPredictiveSettings
2014-11-08 12:57:32.691 [747:184013] class name: _UIPickerViewTopFrame
2014-11-08 12:57:32.691 [747:184013] class name: _UIOnePartImageView
2014-11-08 12:57:32.692 [747:184013] class name: _UIPickerViewSelectionBar
2014-11-08 12:57:32.692 [747:184013] class name: _UIPickerWheelView
2014-11-08 12:57:32.692 [747:184013] class name: _UIPickerViewTestParameters
......
```

#### 对 App 的用户行为进行追踪

就是用户点击时把事件记录下来。一般比较做法就是在viewDidAppear里记录事件，这样会让这样记录事件的代码遍布整个项目中。继承或类别也会有问题。这时利用Method Swizzling把一个方法的实现和另一个方法的实现进行替换。

```c
//先定义一个类别，添加要Swizzled的方法
@implementation UIViewController (Logging)- (void)swizzled_viewDidAppear:(BOOL)animated
{ // call original implementation
     [self swizzled_viewDidAppear:animated]; // Logging
     [Logging logWithEventName:NSStringFromClass([self class])];
}
//接下来实现swizzle方法
@implementation UIViewController (Logging)void swizzleMethod(Class class, SEL originalSelector, SEL swizzledSelector) { // the method might not exist in the class, but in its superclass
     Method originalMethod = class_getInstanceMethod(class, originalSelector);
     Method swizzledMethod = class_getInstanceMethod(class, swizzledSelector); // class_addMethod will fail if original method already exists
     BOOL didAddMethod = class_addMethod(class, originalSelector, method_getImplementation(swizzledMethod), method_getTypeEncoding(swizzledMethod)); // the method doesn’t exist and we just added one
     if (didAddMethod) {
          class_replaceMethod(class, swizzledSelector, method_getImplementation(originalMethod), method_getTypeEncoding(originalMethod));
     }
     else {
          method_exchangeImplementations(originalMethod, swizzledMethod);
     }
}

//最后要确保在程序启动的时候调用swizzleMethod方法在之前的UIViewController的Logging类别里添加+load:方法，然后在+load:里把viewDidAppear替换掉
@implementation UIViewController (Logging)+ (void)load
{
     swizzleMethod([self class], @selector(viewDidAppear:), @selector(swizzled_viewDidAppear:));
}

//更简化直接用新的IMP取代原IMP，不是替换，只需要有全局的函数指针指向原IMP即可。
void (gOriginalViewDidAppear)(id, SEL, BOOL);void newViewDidAppear(UIViewController *self, SEL _cmd, BOOL animated)
{ // call original implementation
     gOriginalViewDidAppear(self, _cmd, animated); // Logging
     [Logging logWithEventName:NSStringFromClass([self class])];
}
+ (void)load
{
     Method originalMethod = class_getInstanceMethod(self, @selector(viewDidAppear:));
     gOriginalViewDidAppear = (void *)method_getImplementation(originalMethod); if(!class_addMethod(self, @selector(viewDidAppear:), (IMP) newViewDidAppear, method_getTypeEncoding(originalMethod))) {
          method_setImplementation(originalMethod, (IMP) newViewDidAppear);
     }
}
```

通过Method Swizzling可以把事件代码或Logging，Authentication，Caching等跟主要业务逻辑代码解耦。这种处理方式叫做Cross Cutting Concerns

[http://en.wikipedia.org/wiki/Cross-cutting\_concern](http://en.wikipedia.org/wiki/Cross-cutting_concern)

用Method Swizzling动态给指定的方法添加代码解决Cross Cutting Concerns的编程方式叫Aspect Oriented Programming

[http://en.wikipedia.org/wiki/Aspect-oriented\_programming](http://en.wikipedia.org/wiki/Aspect-oriented_programming)

目前有些第三方库可以很方便的使用AOP，比如Aspects

[https://github.com/steipete/Aspects](https://github.com/steipete/Aspects)

这里是使用Aspects的范例

[https://github.com/okcomp/AspectsDemo](https://github.com/okcomp/AspectsDemo)

###### 参考

1. [Objc Runtime](https://github.com/ming1016/study/wiki/Objc-Runtime)



