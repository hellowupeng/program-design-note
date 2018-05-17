# 消息转发

##### 转发

如果您将消息发送给不处理该消息的对象，在宣布错误之前，运行时发送该对象一个带有 NSInvocation 对象作为其唯一参数的forwardInvocation：消息，NSInvocation 对象封装了原始消息和随之传递的参数。

你可以实现一个 forwardInvocation：方法来给消息一个默认的响应，或者以其他方式避免错误。顾名思义，forwardInvocation：通常用于将消息转发给另一个对象。

##### 转发和多重继承

##### 代理对象

##### 转发和继承



