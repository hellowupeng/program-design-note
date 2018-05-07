# Block 编程

Blocks 是一个添加到 C，Objective-C 和 C++ 的语言级特性，它允许创建能传入到方法或函数、似乎是值的不同代码片段。Blocks 是 Objective-C 对象，能被添加到集合（NSArray，NSDictionary）里。它们也能从封闭范围里捕获值，类似于其他编程语言里的闭包或 lambda 表达式。

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

Block 也能像方法和函数一样携带参数和返回值。

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

输出显示：

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

\(void \(^\)\(void\)\)指定参数是一个不携带任何参数和返回值的 block。

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

许多 Cocoa 和Cocoa Touch API 使用 blocks 简化常用任务，例如集合枚举。

NSArray 类提供三个基于 block 的方法，包括：

```
- (void)enumerateObjectsUsingBlock:(void (^)(id obj, NSUInteger idx, BOOL *stop))block;
```

这个方法携带一个参数，是一个数组里每个条目都会调用一个的 block：

```
NSArray *array = ...
    [array enumerateObjectUsingBlock:^ (id obj, NSUInteger idx, BOOL *stop) {
        NSLog(@"Object at index %lu is %@", idx, obj);
    }];
```

这个 block 携带三个参数，前两个引用数组里的当前对象和它的索引。第三个参数是一个指向你可以用于停止枚举的布尔变量的指针：

```
[array enumerateObjectsUsingBlock:^ (id obj, NSUInteger idx, BOOL *stop) {
    if (...) {
        *stop = YES;
    }
}];
```

使用 enumerateObjectsWithOptions:usingBlock: 方法自定义枚举。例如指定 NSEnumerationReverse 选项，会从相反的顺序遍历数组。

如果枚举 block 里的代码是处理器密集型并且对于并发执行是安全的，你可以使用 NSEnumerationConcurrent 选项：

```
[array enumerateObjectsWithOptions:NSEnumerationConcurrent usingBlock:^ (id obj, NSUInteger idx, BOOL *stop) {
    ...
}];
```

这个标记指示枚举 block 调用可以分发到多个线程，如果 block 代码尤其是处理器密集型的话，会提供一个潜在的性能提升。注意在使用这个选项时，枚举顺序是未定义的。

NSDictionary 类也提供基于 block 的方法，包括：

```
NSDictionary *dictionary = ...
    [dictionary enumerateKeysAndObjectsUsingBlock:^ (id key, id obj, BOOL *stop) {
        NSLog(@"key: %@, value: %@", key, obj);
    }];
```

这使枚举每个键值对比在使用传统的循环时更加方便。

### Block 简化并发任务

OS X 和 iOS 提供了各种并发技术，包括两种任务调度机制：操作队列和 GCD。这些机制围绕等待被调用的任务队列的想法。你按你需要它们被调用的顺序添加 blocks 到一个队列，系统在处理器时间和资源变得可用时把它们移出队列用于调用。

串行队列一次只允许执行一个任务，队列里的下一个任务不会被移出队列并调用直到之前的任务已经完成。并发队列调用尽可能多的任务，不需要等待之前的任务完成。

**和操作队列（Operation Queue）一起使用 Block 操作（operation）**

操作队列是 Cocoa 和 Cocoa Touch 进行任务调度的途径。你创建一个 NSOperation 实例来封装一个工作单位以及任何必要的数据，然后添加那个操作到一个 NSOperationQueue 用于执行。

你可以创建自定义 NSOperation 子类来实现复杂任务，也可以使用 NSBlockOperation 来使用 block 创建一个操作：

```
NSBlockOperation *operation = [NSBlockOperation blockOperationWithBlock:^{
    ...
}];
```

也可以手动执行一个 operation，但是 operations 通常被添加到一个存在的操作队里或你自己创建的队列，准备执行：

```
// schedule task on main queue:
NSOperationQueue *mainQueue = [NSOperationQueue mainQueue];
[mainQueue addOperation:operation];

// schedule task on background queue
NSOperationQueue *queue = [[NSOperationQueue alloc] init];
[queue addOperation:operation];
```

使用一个操作队列，可以在队列之间配置优先级或依赖，例如指定一个操作不应该被执行直到一组其他操作已经完成。你也可以通过键值观察监听你的操作的状态变化。

**和 GCD 一起在调度队列（Dispatch Queue）上安排 Blocks**

如果你需要安排一个随意的代码块用于执行，你可以直接和由 GCD 控制的调度队列（dispatch queue）一起工作。调度队列使用期望的调用者同步或者异步地执行任务变得容易，并且采用先进先出的顺序执行它们的任务。

你可以创建你自己的调度队列或使用由 GCD 自动提供的调度队列之一。如果需要安排一个用于同时执行的任务，例如，可以通过使用 dispatch\_get\_global\_queue\(\) 函数获取一个存在队列的引用并指定队列优先级：

```
 dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
```

要调度 block 到队列里，使用 dispatch\_async\(\) 或 dispatch\_sync\(\) 函数。dispatch\_async\(\) 函数立即返回，无需等待 block 被调用。

```
dispatch_async(queue, ^{
    NSLog(@"Block for asynchronous execution");
});
```

dispatch\_sync\(\) 函数不返回直到 block 已经完成执行；可以在并发 block 在继续之前需要在主线程上等待另一个任务完成的情况下使用它。

###### 参考资料

1. [Working with Blocks](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithBlocks/WorkingwithBlocks.html)



