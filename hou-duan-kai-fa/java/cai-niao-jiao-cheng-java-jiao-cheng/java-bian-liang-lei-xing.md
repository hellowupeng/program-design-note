# Java 变量类型

Java 支持的变量类型：

* 类变量：独立于方法之外的变量，用 static 修饰。
* 实例变量：独立于方法之外的变量，没有 static 修饰。
* 局部变量：类方法中的变量。

```java
public class Variable {
    static int allClicks = 0; // 类变量
    
    String str = "hello world"; // 实例变量
    
    public void method() {
        int i = 0; // 局部变量
    }
}
```



