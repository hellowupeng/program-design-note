# 内存管理

### 关于内存管理

应用程序内存管理是在程序运行时分配内存，使用它并在完成时释放内存的过程。一个写得很好的程序尽可能少地使用内存。在 Objective-C 中，它也可以被看作是将有限内存资源的所有权分配给许多数据和代码段的一种方式。

Objective-C 提供了两种应用程序内存管理方法：

1. “手动保留释放”或 MRR 的方法中，你可以通过跟踪您拥有的对象来显式管理内存。这是通过使用称为引用计数的模型实现的，该模型是 Foundation 类 NSObject 与运行时环境一起提供的。

2. 在自动引用计数或 ARC 中，系统使用与 MRR 相同的引用计数系统，但它会在编译时插入适当的内存管理方法调用。

内存管理不正确会导致两种主要问题：

* 释放或覆盖仍在使用的数据

  这会导致内存损坏，并且通常会导致应用程序崩溃，甚至导致用户数据损坏。

* 未释放不再使用的数据会导致内存泄漏

  内存泄漏是分配的内存未被释放的地方，即使它不再被使用。泄漏导致您的应用程序使用不断增加的内存量，这反过来可能导致系统性能下降或应用程序终止。

###### 参考资料

1. [Advanced Memory Management Programming Guide](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html#//apple_ref/doc/uid/10000011-SW1)



