# Block 编程

Blocks 是一个添加到 C，Objective-C 和 C++ 的语言级特性，它允许你创建能传入到方法或函数、似乎是值的不同代码片段。Blocks 是 Objective-C 对象，能被添加到集合（NSArray，NSDictionary）里。它们也能从封闭范围里捕获值，类似于其他编程语言里的闭包或 lambda 表达式。

### Block 语法

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

##### **Block 携带参数和返回值**

Block 也能像方法和参数一样携带参数和返回值。

一个变量引用一个返回两个值相乘的结果的 block：

```
double (^multiplyTwoValues)(double, double);
```

相应的 block 字面量：

```
^(double firstValue, double secondValue){
    return firstValue * secondValue;
}
```

和函数定义一样，firstValue 和 secondValue 被用于在 block 被调用时引用提供的值。返回类型从 block 里的返回语句推断出。

也可以明确的指定返回类型：

```
^double(double firstValue, double secondValue){
    return firstValue * secondValue;
}
```

声明并定义了 block 后，像函数一样调用它：

```
double (^multiplyTwoValues)(double, double) = ^(double firstValue, double secondValue) {
    return firstValue * secondValue;
};

double result = multiplyTwoValues(2, 4);
NSLog(@"The result is %f", result);
```

##### **Block 能从封闭范围捕获值**

block 能捕获方法范围里的值：

```
- (void)testMethod {
    int anInteger = 42;

    void (^testBlock)(void) = ^{
        NSLog(@"Integer is: %i", anInteger);
    }

    testBlock();
}
```

anInteger 在 block 外面声明，在 block 定义时被捕获。

除非另外指定，否则 block 只能捕获定义时的值。

被调用期间改变外部变量的值：

```
int anInteger = 42;

void (^testBlock)(void) = ^{
    NSLog(@"Integer is: %i", anInteger);
}

anInteger = 84;

testBlock();
```

block 捕获的值不受影响：

```
Integer is: 42
```

block 不能改变原始变量的值，也不能改变捕获的值（它作为 const 变量被捕获）。

###### 使用 \_\_block 变量共享存储

想要能够改变 block 里捕获的值，在原始变量声明上使用 \_\_block 存储类型修饰符。

```
__block int anInteger = 42;

void (^testBlock)(void) = ^{
    NSLog(@"Integer is: %i", anInteger);
};

anInteger = 84;

testBlock();
```

anInteger 被声明为 \_\_block 变量，它和 block 声明共享存储。

##### **Block 能作为方法或函数的参数**

##### **使用类型定义简化 Block 语法**

##### **对象使用属性追踪 Block**

##### **在捕获 self 时避免强引用循环**

### Block 简化枚举

### Block 简化并发任务

**和操作队列（Operation Queue）一起使用 Block 操作（operation）**

**和 GCD 一起在调度队列（Dispatch Queue）上安排 Blocks**

###### 参考资料

1. [Working with Blocks](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithBlocks/WorkingwithBlocks.html)



