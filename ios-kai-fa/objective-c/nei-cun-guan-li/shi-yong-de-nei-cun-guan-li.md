# 实用的内存管理

### 使用访问器方法使内存管理更容易

如果你的类有一个属性是一个对象，你必须确保在你使用它的时候，任何设置为该值的对象都不会被释放。因此，您必须在设置对象时声明对象的所有权。你也必须确保稍后放弃当前持有值的所有权。

有时它可能看起来很乏味或迂腐，但如果你始终如一地使用访问器方法，那么发生存储管理问题的机会就会大大减少。

考虑一个Counter对象，你想要设置它的 count：

```
@interface Counter : NSObject
@property (nonatomic, retain) NSNumber *count;
@end;
```

该属性声明两个访问器方法。通常，你应该要求编译器合成这些方法。

在 “get” 访问器中，您只需返回合成的实例变量，因此不需要 retain 或 release：

```
- (NSNumber *)count {
    return _count;
}
```

在“设置”方法中，如果其他人都按照相同的规则进行游戏，则必须假定新的计数可以在任何时候被处理，因此您必须获得对象的所有权，通过发送一个 retain 消息来确保它不会被释放。您还必须通过发送 release 消息来放弃旧计数对象的所有权。您必须在 \[newCount retain\] 之后发送此内容，以防两者是相同的对象 - 您不希望无意中导致它被释放。

```
- (void)setCount:(NSNumber *)newCount {
    [newCount retain];
    [_count release];
    // Make the new assignment.
    _count = newCount;
}
```

##### 使用访问器方法来设置属性值

假设你想实现一个方法来重置计数器。你又多种选择。第一种实现使用 alloc 创建 NSNumber 实例，可以通过一个 release 来平衡它。

```
- (void)reset {
    NSNumber *zero = [[NSNumber alloc] initWithInteger:0];
    [self setCount:zero];
    [zero release];
}
```

第二个使用便利的构造函数来创建一个新的 NSNumber 对象。因此不需要 retain 或 release 消息。

```
- (void)reset {
    NSNumber *zero = [NSNumber numberWithInteger:0];
    [self setCount:zero];
}
```

##### 不要在初始化方法和 dealloc 中使用访问器方法

唯一不应使用访问器方法来设置实例变量的地方在初始化方法和 dealloc 中。

```
- init {
    self = [super init];
    if (self) {
        _count = [[NSNumber alloc] initWithInteger:0];
    }
    return self;
}
```

要允许计数器初始化为非零计数，可以按如下方式实现 initWithCount：方法：

```
- initWithCount:(NSNumber *)startingCount {
    self = [super init];
    if (self) {
        _count = [startingCount copy];
    }
    return self;
}
```

由于 Counter 类有一个对象实例变量，所以你还必须实现一个 dealloc 方法。

它应该通过发送释放消息来放弃任何实例变量的所有权，最终它应该调用 super 的实现：

```
- (void)dealloc {
    [_count release];
    [super dealloc];
}
```

### 使用弱引用来避免 Retain Cycle（循环引用）

保留一个对象会为该对象创建一个强引用。一个对象不能被释放，直到所有的强引用被释放。如果两个对象可能有循环引用，则可能会出现一个称为保留循环的问题，也就是说，他们彼此有强引用。

保留循环（retain cycle）问题的解决方案是使用弱引用。弱引用是非拥有关系，其中源对象不保留其具有引用的对象。

Cocoa 制定了一个约定，一个“父”对象应该对其“子女”保持强引用，这些孩子应该对他们的父母使用弱引用。Cocoa 中弱引用的例子包括 table 数据源，outline view items，通知观察者以及其他 target 和 delegate。

同样，当一个委托对象被释放时，你需要通过向另一个对象发送一个带有 nil 参数的 setDelegate：消息来移除委托链接。这些消息通常从对象的 dealloc 方法发送。

### 避免导致你正在使用的对象被释放

### 不要使用 dealloc 来管理稀缺资源

### 集合拥有它们包含的对象

### 使用引用计数实现所有权策略



