# 消息传递

##### objc\_msgSend 函数

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
* 一个类调度表。该表具有将方法选择器与方法的地址相关联的条目。setOrigin: 方法选择器与 setOrigin: 的地址关联，display 方法选择器与 display 的地址关联。

新对象创建时，它的内存被分配，它的实例变量被初始化。对象的第一个实例变量是指向它的类结构的指针。这个指针叫做 isa，让对象能访问它的类，通过这个类到它继承的所有类。

![](/assets/Messaging Framework.png)

方法动态绑定到消息过程：

发送消息给对象时，消息传递函数跟随对象的 isa 指针，指向查找调度表中的方法选择器的类结构。如果它在那里找不到选择器，objc\_msgSend 跟随指向父类的指针，并尝试在父类调度表中查找选择器。继续失败会导致 objc\_msgSend 沿着类层次结构，直到它到达 NSObject 类。一旦找到选择器，消息传递函数调用在表中输入的方法并将其传递给接收对象的数据结构。

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

