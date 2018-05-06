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

日志输出：

```
Integer is: 84
```

block 能修改原始的值：

```
__block int anInteger = 42;

void (^testBlock)(void) = ^{
    NSLog(@"Integer is: %i", anInteger);
    anInteger = 100;
};

testBlock();
NSLog(@"Value of original variable is now: %i", anInteger);
```

输入显示：

```
Integer is: 42
Value of original variable is now: 100
```

##### **Block 能作为方法或函数的参数**

blocks 常被用于回调，在任务完成时执行。

blocks 能在初始化任务时定义回调行为：

```
- (IBAction)fetchRemoteInformation:(id)sender {
    [self showProgressIndicator];

    XYZWebTask *task = ...

    [task beginTaskWithCallbackBlock:^{
        [self hideProgressIndicator];
    }];
}
```

这个例子调用方法显示进度指示器，然后创建任务并开始。回调 block 指定任务完成时被执行的代码：调用方法隐藏进度指示器。回调 block 捕获了 self 以便能在被调用时调用 hideProgressIndicator 方法。捕获 self 时要小心，很容易造成强引用循环。

```
- (void)beginTaskWithCallbackBlock:(void (^)(void))callback;
```

\(void \(^\)\(void\)\)指定参数是一个不携带任何参数或返回值的 block。

```
- (void)beginTaskWithCallbackBlock:(void (^)(void))callback {
    ...
    callback();
}
```

拥有一个 block 参数且 block 拥有两个参数的方法：

```
- (void)doSomethingWithBlock:(void (^)(double, double))block
{
    ...
    block(21.0, 2.0);
}
```

###### Block 总应该是方法的最后一个参数

一个方法最后只使用一个 block 参数。如果方法需要其他非 block 参数，block 应该放在末尾：

```
- (void)beginTaskWithName:(NSString *)name completion:(void (^)(void))callback;
```

这样方法调用时更加易读：

```
[self beginTaskWithName:@"MyTask" completion:^{
    NSLog(@"The task is complete");
}];
```

##### **使用类型定义简化 Block 语法**

为一个不携带参数或返回值的 block 定义一种类型：

```
typedef void (^XYZSimpleBlock)(void);
```

然后能为方法参数或创建 block 变量时使用自定义类型：

```
XYZSimpleBlock anotherBlock = ^{
    ...
};
```

```
- (void)beginFetchWithCallbackBlock:(XYZSimpleBlock)callback {
    ...
    callbackBlock();
};
```

自定义类型定义在处理返回 blocks 或携带其他 blocks 作为参数时特别有用。

```
void (^(^complexBlock)(void (^)(void))(void)) = ^(void (^aBlock)(void)) {
    ...
    return ^{
        ...
    };
};
```

complexBlock 变量引用一个携带另一个 block 作为一个参数（aBlock）和返回另一个 block 的 block。

使用类型定义重写代码：

```
XYZSimpleBlock (^betterBlock)(XYZSimpleBlock) = ^(XYZSimpleBlock aBlock) {
    ...
    return ^{
        ...    
    };
};
```

##### **对象使用属性追踪 Block**

定义属性跟踪 block 的语法类型 block 变量：

```
@interface XYZObject : NSObject
@property (copy) void (^blockProperty)(void);
@end
```

> 注意：指定 copy 作为属性特性，因为 block 需要被复制来在原始范围外跟踪它捕获的状态。

block 属性像其他 block 变量一样设置、调用：

```
self.blockProperty = ^{
    ...
};
self.blockProperty();
```

也可以使用类型定义作为 block 属性声明：

```
typedef void (^XYZSimpleBlock)(void);

@interface XYZObject : NSObject
@propery (copy) XYZSimpleBlock blockProperty;
@end
```

##### **在捕获 self 时避免强引用循环**

blocks 保留所有捕获对象的强引用，包括 self，很容易造成强引用循环。

一个保留一个 copy 属性的对象，block 捕获了 self。

```
@interface XYZBlockKeeper : NSObject
@property (copy) void (^block)(void);
@end
```

```
@implementation XYZBlockKeeper
- (void)configureBlock {
    self.block = ^{
        [self doSomething];    // capturing a strong reference to self
                               // creates a strong reference cycle
    };
}
...
@end
```

避免这个问题的最佳实践是捕获 self 的弱引用：

```
- (void)configureBlock {
    XYZBlockKeeper * __weak weakSelf = self;
    self.block = ^{
        [weakSelf doSomething];   // capture the weak reference
                                  // to avoid the reference cycle
    }
}
```

捕获指向 self 的弱指针，block 不会保留 XYZBlockKeeper 对象的强引用关系。如果对象在 block 被调用之前被释放，weakSelf 指针会被置为 nil。

### Block 简化枚举

### Block 简化并发任务

**和操作队列（Operation Queue）一起使用 Block 操作（operation）**

**和 GCD 一起在调度队列（Dispatch Queue）上安排 Blocks**

###### 参考资料

1. [Working with Blocks](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithBlocks/WorkingwithBlocks.html)



