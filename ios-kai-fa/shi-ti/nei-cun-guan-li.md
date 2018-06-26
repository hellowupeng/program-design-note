# 内存管理

##### 1、为什么代理要用 weak？代理的 delegate 和dataSource 有什么区别？block 和 代理的区别？

一、为什么代理要用weak？  
   防止循环引用。例如View有一个协议，需要一个代理实现回调。一个Controller添加这个View，并且遵守协议，成为View的代理。如果不用week，用strong，Controller -&gt;View -&gt; delegate -&gt; Controller，就循环引用了。

二、代理的delegate和dataSource有什么区别？  
   delegate偏重于与用户交互的回调，有那些方法可以供我使用，例如UITableviewDelegate；dataSource偏重于数据的回调，view里面有什么东西，属性都是什么，例如UITableviewDatasource；

三、block和代理的区别?

1）block简介

在 iOS中， block一共分三种。

（1）全局静态 block，不会访问任何外部变量，执行完就销毁。

```
^{
    NSLog(@"Hello World!");
}();
```

（2）保存在栈中的 block，当函数返回时会被销毁，和第一种的区别就是调用了外部变量。

```
[UIView animateWithDuration:3 animations:^{

    self.view.backgroundColor = [UIColor redColor];
}];
```

（3）保存在堆中的 block，当引用计数为 0 时会被销毁。例如按钮的点击事件，一直存在，即使执行过，也不销毁，因为按钮还可能被点击，持有按钮的View被销毁，它才会被销毁。

2）block优点

block的代码可读性更好。因为应用block和实现block的地方在一起。代理的声明和实现就分开来了，在两个类中。代理使用起来也更麻烦，因为要声明协议、声明代理、遵守协议、实现协议里的方法。block不需要声明，也不需要遵守，只需要声明和实现就可以了。

block是一种轻量级的回调，可以直接访问上下文，由于block的代码是内联的，运行效率更高。block就是一个对象，实现了匿名函数的功能。所以我们可以把block当做一个成员变量、属性、参数使用，使用起来非常灵活。像用AFNetworking请求数据和GCD实现多线程，都使用了block回调。

3）block缺点

blcok的运行成本高。block出栈需要将使用的数据从栈内存拷贝到堆内存，当然对象的话就是引用计数加1，使用完或者block置nil后才销毁。delegate只是保存了一个对象指针\(一定要用week修饰delegate，不然也会循环引用\)，直接回调，没有额外消耗。就像C的函数指针，只多做了一个查表动作。

block容易造成循环引用，而且不易察觉。因为为了blcok不被系统回收，所以我们都用copy关键字修饰，实行强引用。block对捕获的变量也都是强引用，所以就会造成循环引用。

4）如何使用

优先使用block。

  如果回调函数很多，多余三个使用代理。

  如果回调的很频繁，次数很多，像UITableview，每次初始化、滑动、点击都会回调，使用代理。

5）代理和 block 的区别

相同点：代理和Block大多是我们都可以用来做倒序传值的。我们都得注意避免循环引用。不然我们去使用代理还是Block的时候，都需要判断它们是否实现

不同点：代理使用weak修饰，代理必须先声明方法。当我们调用代理的时候要判断是否已经实现。

block：使用的是copy来修饰，block保存的是一段代码，其实也就是一个函数。并且可以自动捕捉自动变量，如果想修改此自动变量，还必须使用\_\_block修饰。

