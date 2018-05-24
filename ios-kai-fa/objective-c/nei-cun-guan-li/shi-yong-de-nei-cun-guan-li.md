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

假设你想实现一个方法来重置计数器。你有多种选择。第一种实现使用 alloc 创建 NSNumber 实例，可以通过一个 release 来平衡它。

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

Cocoa 的所有权策略规定，收到的对象通常在调用方法的整个范围内保持有效。也应该可以从当前范围返回一个接收的对象，而不用担心它被释放。

这条规则偶尔有例外，主要分为两类。

1. 从一个基本集合类中移除一个对象时。

```
heisenObject = [array objectAtIndex:n];
[array removeObjectAtIndex:n];
// heisenObject could now be invalid.
```

当一个对象从其中一个基本集合类中被移除时，它会发送一个 release（而不是 autorelease）消息。如果集合是被删除对象的唯一所有者，那么将立即释放被删除的对象（示例中的 heisenObject）。

1. 当“父对象”被释放时。

```
id parent = <#create a parent object#>;
// ...
heisenObject = [parent child] ;
[parent release]; // Or, for example: self.parent = nil;
// heisenObject could now be invalid.
```

在某些情况下，您从另一个对象中获取对象，然后直接或间接释放父对象。如果释放父对象，并且父项是该子项的唯一所有者，则该子对象（示例中的 heisenObject）将同时释放。

为了防止出现这些情况，您在收到 heisenObject 后保留 heisenObject，并在完成后释放它。例如：

```
heisenObject = [[array objectAtIndex:n] retain];
[array removeObjectAtIndex:n];
// Use heisenObject...
[heisenObject release];
```

### 不要使用 dealloc 来管理稀缺资源

您通常不应该在 dealloc 方法中管理稀缺资源，例如文件描述符，网络连接以及缓冲区或缓存。尤其是，您不应该设计类，以便在您认为它会被调用时调用 dealloc。dealloc 的调用可能由于错误而延迟或回避。

相反，如果你有一个实例管理稀缺资源的类，你应该设计你的应用程序，以便知道你什么时候不再需要这些资源，然后告诉实例在那个时候“清理”。

### 集合拥有它们包含的对象

将对象添加到集合（例如数组，字典或集合）时，集合将拥有它。当对象从集合中删除或集合本身被释放时，集合将放弃所有权。如果您想创建一个数字数组，您可以执行以下任一操作：

```
NSMutableArray *array = <#Get a mutable array#>;
NSUInteger i;
// ...
for (i = 0; i < 10; i++) {
    NSNumber *convenienceNumber = [NSNumber numberWithInteger:i];
    [array addObject:convenienceNumber];
}
```

在这种情况下，你没有调用 alloc，所以不需要调用 release。没有必要保留新的数字（convenienceNumber），因为数组会这样做。

```
NSMutableArray *array = <#Get a mutable array#>;
NSUInteger i;
// ...
for (i = 0; i < 10; i++) {
    NSNumber *allocedNumber = [[NSNumber alloc] initWithInteger:i];
    [array addObject:allocedNumber];
    [allocedNumber release];
}
```

在这种情况下，您需要在 for 循环的范围内发送 allocedNumber release 消息来平衡 alloc。由于数组通过addObject添加时保留了数字，因此在数组中它不会被释放。

### 使用引用计数实现所有权策略

所有权策略通过引用计数来实现--通常称为“保留计数”。每个对象都有一个保留计数。

* 当你创建一个对象时，它的保留计数为1。

* 当你向对象发送 `retain` 消息时，其保留计数会加1。

* 当你向对象发送 release 消息时，其保留计数减1。

当您向对象发送自动释放消息时，其保留计数在当前自动释放池块的末尾减1。

* 如果一个对象的保留计数减少到零，它将被释放。



