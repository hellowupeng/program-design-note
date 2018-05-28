# Java 方法

System.out.println\(\)

* println\(\) 是一个方法。
* System 是系统类。
* out 是标准输出对象。

### finalize\(\) 方法

在对象被垃圾收集器析构（回收）之前调用，用来清除回收对象。

例如，你可以使用 finalize\(\) 来确保一个对象打开的文件被关闭了。

```java
protected void finalize()
{
   // 在这里终结代码
}
```



