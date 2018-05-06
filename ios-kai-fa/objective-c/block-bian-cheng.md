# Block 编程

Blocks 是一个添加到 C，Objective-C 和 C++ 的语言级特性，它允许你创建能传入到方法或函数、似乎是值的不同代码片段。Blocks 是 Objective-C 对象，能被添加到集合（NSArray，NSDictionary）里。它们也能从封闭范围里捕获值，类似于其他编程语言里的闭包或 lambda 表达式。

###### Block 语法

使用脱字符（^）定义 block 字面量：

```Objective-C
^{
    NSLog(@"This is a block");
}
```

声明一个变量来跟踪 block：

```
void (^simpleBlock)(void);
```

这个例子声明一个叫做 simpleBlock 的变量引用不携带参数、没有返回值的 block。这个变量能分配给上面展示的 block 字面量：

```
void (^simpleBlock)(void) = ^{
    NSLog(@"This is a block");
};
```

调用声明并赋值的 block：

```
simpleBlock();
```

> 注意：如果调用未分配的变量（nil block 变量），应用会崩溃。

**Block 携带参数和返回值**

**Block 能从封闭范围捕获值**

**Block 能作为方法或函数的参数**

**使用类型定义简化 Block 语法**

**对象使用属性追踪 Block**

**在捕获 self 时避免强引用循环**

###### Block 简化枚举

###### Block 简化并发任务

**和操作队列（Operation Queue）一起使用 Block 操作（operation）**

**和 GCD 一起在调度队列（Dispatch Queue）上安排 Blocks**

###### 参考资料

1. [Working with Blocks](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithBlocks/WorkingwithBlocks.html)



