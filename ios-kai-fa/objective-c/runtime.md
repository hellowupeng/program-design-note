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

新对象创建时，它的内存被分配，它的实例变量被初始化。对象的第一个实例变量是指向它的类结构的指针。这个指针叫做 isa，让对象能访问它的类，通过这个类到它继承的所有类。

![](/assets/Messaging Framework.png)

方法动态绑定到消息：

发送消息给对象时，消息传递函数跟随对象的 isa 指针，指向查找调度表中的方法选择器的类结构。如果它在那里找不到选择器，objc\_msgSend 跟随指向父类的指针，并尝试在其调度表中查找选择器。连续失败导致 objc\_msgSend 沿着类层次结构，直到它到达 NSObject 类。一旦找到选择器，该函数调用在表中输入的方法并将其传递给接收对象的数据结构。

为了加速消息处理过程，运行时系统会缓存方法的选择器和地址。

##### 使用隐藏参数

当 objc\_msgSend 找到实现方法的程序时，它调用该程序并传入消息中的所有参数。它还传入程序两个隐藏参数：

* 接收对象
* 方法选择器

方法引用接收对象为 self，引用它自己的选择器为 _cmd。_下面的例子_中，\_cmd 引用 strange 方法的选择器，self 引用接收 strange 消息的对象。_

```
- strange
{
    id target = getTheReceiver();
    SEL method = getTheMethod();

    if (target == self || method == _cmd)
        return nil;
    return [target performSelector:method];
}
```

##### 获取方法地址

绕过动态绑定的唯一方式是获取方法的地址并像函数一样直接调用它。

下面的例子展示了如何调用实现 setFilled：方法的过程：

```
void (*setter)(id, SEL, BOOL);
int i;

setter = (void (*)(id, SEL, BOOL))[target methodForSelector:@selector(setFilled:)];
for (i = 0; i < 100; i++)
    setter(targetList[i], @selector(setFilled:), YES);
```

传递给过程（procedure）的前两个参数是接收对象（self）和方法选择器（\_cmd）。这些参数在方法语法中是隐藏的，但是当方法作为函数调用时必须明确给出。

### 动态方法解析

##### 动态方法解析

##### 动态加载

### 消息转发

##### 转发

##### 转发和多重继承

##### 代理对象

##### 转发和继承



