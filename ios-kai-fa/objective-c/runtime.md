# runtime

Objective-C 语言推迟尽可能多的决定从编译时和链接时到运行时（runtime）。只要有可能，他就动态地处理任务。runtime 系统扮演了 Objective-C 语言的操作系统，它是使 Objective-C 语言工作的原因。

### 与 runtime 交互

Objective-C 程序在三个不同级别上与 runtime 系统交互：通过 Objective-C 源码；通过 Foundation 框架的 NSObject 类定义的方法；t通过直接调用 runtime 函数。

##### Objective-C 源码

大多数时候，runtime 系统是在幕后自动工作。只使用它来编写和编译 Objective-C 源码。

在编译包含 Objective-C 类和方法的代码时，编译器创建实现了语言动态特性的数据结构和函数调用。数据结构捕获从类、分类定义和协议声明里找到的信息。他们包括类和协议对象，以及方法选择器，实例变量模板和其他从源码提炼的信息。runtime 的主要功能是发送消息。由源码消息表达式调用。

##### NSObject 方法

NSObject 提供了一些方法用于从 runtime 系统查询信息。class 方法请求对象确定它的类；isKindOfClass: 和 isMemberOfClass: 方法测试对象在继承层次结构中的位置；respondsToSelector: 表明对象是否能接受特定的消息；conformsToProtocol: 表明对象是否要求实现指定协议里定义的方法；methodForSelector: 提供方法实现的地址。

##### Runtime 函数

运行时系统是一个动态共享库，具有由一组函数和数据结构组成的公共接口。

### 消息传递

##### objc\_\_msgSen\_d 函数

在 Objective-C 里，消息在运行时才会绑定到方法实现。编译器转换消息表达式为一个在消息传递函数 objc\_msgSend 上的一个调用。

```
[receiver message]
```

这个函数把消息接收者和方法选择器（方法名）作为它的两个主要参数：

```
objc_msgSend(receiver, selector)
```

传入消息的参数也由 objc\_msgSend 处理：

```
objc_msgSend(receiver, selector, arg1, arg2, ...)
```

消息传递函数为动态绑定做了所有必须的事情：

* 首先，它查找选择器引用的方法实现。由于相同的方法能被不同的类实现，因此它找到的方法实现取决于接收者的 class。
* 然后，它调用找到的方法，传入接收对象（一个指向它的数据的指针）以及方法指定的所有参数。
* 最后，它传递方法的返回值作为它自己的返回值。

> 注意：编译器生成消息传递函数的调用。你不应该在你写的代码里直接调用它。

消息传递的关键在于编译器为每个类和对象构建的结构。每个类的结构包括两个基本要素：

* 一个指向父类的指针。
* 一个类调度表。该表具有将方法选择器与它们识别的方法的类特定地址相关联的条目。setOrigin: 方法选择器与 setOrigin: 的地址关联，display 方法选择器与 display 的地址关联。

##### 使用隐藏参数

##### 获取方法地址

### 动态方法解析

### 消息转发



