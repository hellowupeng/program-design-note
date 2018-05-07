# runtime

Objective-C 语言推迟尽可能多的决定从编译时和链接时到运行时（runtime）。只要有可能，他就动态地处理任务。runtime 系统扮演了 Objective-C 语言的操作系统，它是使 Objective-C 语言工作的原因。

### 与 runtime 交互

Objective-C 程序在三个不同级别上与 runtime 系统交互：通过 Objective-C 源码；通过 Foundation 框架的 NSObject 类定义的方法；t通过直接调用 runtime 函数。

##### Objective-C 源码

大多数时候，runtime 系统是在幕后自动工作。只使用它来编写和编译 Objective-C 源码。

在编译包含 Objective-C 类和方法的代码时，编译器创建实现了语言动态特性的数据结构和函数调用。数据结构捕获从类、分类定义和协议声明里找到的信息。他们包括类和协议对象，以及方法选择器，实例变量模板和其他从源码提炼的信息。runtime 的主要功能是发送消息。由源码消息表达式调用。

##### NSObject 方法

NSObject 提供了一些方法用于从 runtime 系统查询信息。class 方法请求对象确定它的类；isKindOfClass: 和 isMemberOfClass: 方法测试对象在继承层次结构中的位置；

##### Runtime 函数

### 消息

### 动态方法解析

### 消息转发



