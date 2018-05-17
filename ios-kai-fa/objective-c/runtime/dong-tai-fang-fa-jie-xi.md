# 动态方法解析

##### 动态方法解析

在某些情况下，您可能想动态地提供方法的实现。例如，Objective-C 声明的属性功能包括 @dynamic 指令：

```
@dynamic propertyName;
```

它告诉编译器与该属性关联的方法将被动态地提供。你可以实现 resolveInstanceMethod：和 resolveClassMethod: 方法分别为实例和类方法的给定选择器动态提供实现。

Objective-C 方法只是一个 C 函数，它至少需要两个参数 self 和 \_cmd。你可以使用函数 class\_addMethod 将函数作为方法添加到类中。

```
void dynamicMethodIMP(id self, SEL _cmd) {
    // implementation ....
}
```

你可以使用 resolveInstanceMethod：将其作为方法动态添加到类中（称为 resolveThisMethodDynamically）。

```
@implementation MyClass
+ (BOOL)resolveInstanceMethod:(SEL)aSEL
{
    if (aSEL == @selector(resolveThisMethodDynamically)) {
          class_addMethod([self class], aSEL, (IMP) dynamicMethodIMP, "v@:");
          return YES;
    }
    return [super resolveInstanceMethod:aSEL];
}
@end
```

在转发机制启动之前，一个类有机会动态地解析一个方法。如果调用 respondsToSelector：或 instancesRespondToSelector：，则动态方法解析器有机会首先为选择器提供 IMP。如果你实现 resolveInstanceMethod：但希望特定的选择器通过转发机制实际转发，则您将为这些选择器返回 NO。

##### 动态加载

Objective-C 程序可以在运行时加载和链接新的类和分类。新代码被合并到程序中，并且和在开始时加载的类和分类的处理方式相同。

动态加载可以用来做很多不同的事情。例如，系统偏好设置应用程序中的各个模块都是动态加载的。

在 Cocoa 环境中，动态加载通常用于允许应用程序进行自定义。

### 



