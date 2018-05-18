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

### 使用弱引用来避免 Retain Cycle（循环引用）

### 避免导致你正在使用的对象被释放

### 不要使用 dealloc 来管理稀缺资源

### 集合拥有它们包含的对象

### 使用引用计数实现所有权策略



