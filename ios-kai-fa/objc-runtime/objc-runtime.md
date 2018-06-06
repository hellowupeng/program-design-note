# Objc Runtime

### 概述

Objc Runtime使得C具有了面向对象能力，在程序运行时创建，检查，修改类、对象和它们的方法。Runtime是C和汇编编写的，这里

[http://www.opensource.apple.com/source/objc4/](http://www.opensource.apple.com/source/objc4/)

可以下到苹果维护的开源代码，GNU也有一个开源的runtime版本，他们都努力的保持一致。

[苹果官方的Runtime编程指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008048)

#### Runtime 函数

Runtime系统是由一系列的函数和数据结构组成的公共接口动态共享库，在/usr/include/objc目录下可以看到头文件，可以用其中一些函数通过C语言实现Objective-C中一样的功能。苹果官方文档

[https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html)

里有详细的Runtime函数文档。

### Class 和 Object 基础数据结构



### 类和对象操作函数

### 成员变量与属性

### Method 和消息

### Protocol 和 Category

### Block



###### 参考

1. [Objc Runtime](https://github.com/ming1016/study/wiki/Objc-Runtime)



