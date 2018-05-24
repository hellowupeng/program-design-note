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

> 注意：`forwardInvocation：`方法只有在它们不调用接收方中的现有方法时才会处理消息。例如，如果您希望您的对象将 `negotiate` 消息转发给另一个对象，则它不能拥有自己的 `negotiate` 方法。如果是这样，该消息将永远不会达到`forwardInvocation :`。

##### 转发和多重继承

转发模仿继承，并可用于将多重继承的某些影响提供给Objective-C程序。通过转发消息来响应消息的对象似乎借用或“继承”了另一个类中定义的方法实现。

![](/assets/forwarding.png)

在此图中，Warrior 类的实例将 `negotiate` 消息转发给 Diplomat 类的实例。它似乎会对 `negotiate` 信息作出回应，并且出于所有实际目的，它的确做出了回应。（尽管实际上是 Diplomat 在做这项工作）

因此转发消息的对象从继承层次结构的两个分支“继承”方法。它自己的分支和响应该消息的对象的分支。在上面的例子中，看起来好像 Warrior 类继承自 Diplomat 以及它自己的超类。

转发提供了你通常希望从多重继承中获得的大部分功能。但是，两者之间有一个重要的区别：多重继承在一个对象中结合了不同的功能。它倾向于大型，多面的对象。它将问题分解成更小的对象，但以对消息发送者透明的方式关联这些对象。

##### 代理对象

转发不仅可以模拟多重继承，还可以开发代表或覆盖更多实体对象的轻量级对象。代理代表另一个对象并向其发送消息。

Objective-C 编程语言中“远程消息传递”中讨论的代理就是这样的代理。代理负责将消息转发到远程接收方的管理细节，确保参数值在连接中被复制和检索，等等。

##### 转发和继承

虽然转发模仿继承，NSObject 类永远不会混淆这两者。像 respondsToSelector：和 isKindOfClass 这样的方法：仅查看继承层次结构，从不查看转发链。例如，将询问 Warrior 对象是否响应 `negotiate` 消息：

```
if ( [aWarrior respondsToSelector:@selector(negotiate)] )
    ...
```

答案是否定的，即使它可以毫无错误地接收谈判信息，并在某种意义上将它们转发给外交官。

如果你希望你的对象表现得好像他们真的继承了它们转发消息的对象的行为一样，你需要重新实现 respondsToSelector：和isKindOfClass：方法来包含你的转发算法：

```
- (BOOL)respondsToSelector:(SEL)aSelector
{
    if ( [super respondsToSelector:aSelector] )
        return YES;
    else {
        /* Here, test whether the aSelector message can     *
         * be forwarded to another object and whether that  *
         * object can respond to it. Return YES if it can.  */
    }
    return NO;
}
```

除了respondsToSelector：和 isKindOfClass：之外，instancesRespondToSelector：方法还应该镜像转发算法。如果使用协议，同样应该将 conformsToProtocol：方法添加到列表中。

如果一个对象能够将消息转发给它的代理项，你可以实现 methodSignatureForSelector：如下:

```
- (NSMethodSignature*)methodSignatureForSelector:(SEL)selector
{
    NSMethodSignature* signature = [super methodSignatureForSelector:selector];
    if (!signature) {
       signature = [surrogate methodSignatureForSelector:selector];
    }
    return signature;
}
```



