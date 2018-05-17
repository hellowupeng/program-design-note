# 消息转发

##### 转发

如果您将消息发送给不处理该消息的对象，在宣布错误之前，运行时发送该对象一个带有 NSInvocation 对象作为其唯一参数的forwardInvocation：消息，NSInvocation 对象封装了原始消息和随之传递的参数。

你可以实现一个 forwardInvocation：方法来给消息一个默认的响应，或者以其他方式避免错误。顾名思义，forwardInvocation：通常用于将消息转发给另一个对象。

即使你的类不能继承 `negotiate` 方法，你仍然可以通过实现一个只将消息传递给另一个类的实例的方法来“借用”它：

```
- (id)negotiate
{
    if ( [someOtherObject respondsTo:@selector(negotiate)] )
        return [someOtherObject negotiate];
    return self;
}
```

要转发消息，所有forwardInvocation：方法需要做的是：

* 确定消息的去向

* 用它的原始参数往那里发送。

该消息可以通过 invokeWithTarget：方法发送：

```
- (void)forwardInvocation:(NSInvocation *)anInvocation
{
    if ([someOtherObject respondsToSelector:
            [anInvocation selector]])
        [anInvocation invokeWithTarget:someOtherObject];
    else
        [super forwardInvocation:anInvocation];
}
```

被转发的消息的返回值将返回给原始发送者。可以将所有类型的返回值传递给发件人，包括 ids，结构体和双精度浮点数。

forwardInvocation：方法可以充当无法识别的消息的分发中心，将它们分发给不同的接收者。或者它可以是一个中转站，将所有消息发送到相同的目的地。它可以将一条消息翻译成另一条消息，或者简单地“吞下”一些消息，因此没有响应，也没有错误。forwardInvocation：方法也可以将多个消息合并为一个响应。

> 注意：forwardInvocation：方法只有在它们不调用标称接收方中的现有方法时才会处理消息。例如，如果您希望您的对象将 negotiate 消息转发给另一个对象，则它不能拥有自己的协商方法。如果是这样，该消息将永远不会达到 forwardInvocation :.

##### 转发和多重继承

转发模仿继承，并可用于将多重继承的某些影响提供给Objective-C程序。通过转发消息来响应消息的对象似乎借用或“继承”了另一个类中定义的方法实现。

![](/assets/forwarding.png)

在此图中，Warrior 类的实例将 `negotiate` 消息转发给 Diplomat 类的实例。它似乎会对 `negotiate` 信息作出回应，并且出于所有实际目的，它的确做出了回应。（尽管实际上是 Diplomat 正在做这项工作）



##### 代理对象

##### 转发和继承



