# Java StringBuffer 和 StringBuilder 类

使用 StringBuffer 和 StringBuilder 类对字符串进行修改。

和 String 类不同，StringBuffer 和 StringBuilder 的类对象能够被多次修改且不产生新的未使用对象。

StringBuilder 的方法不是线程安全的（不能同步访问）。

由于StringBuilder 相较于 StringBuffer 有速度优势，多数情况下建议使用 StringBuilder 类。在应用程序要求线程安全情况下必须使用 StringBuffer 类。

```java
public class Test {
    public static void main(String args[]) {
        StringBuffer sBuffer = new StringBuffer("菜鸟教程官网：");
        sBuffer.append("www");
        sBuffer.append(".runoob");
        sBuffer.append(".com");
        System.out.println(sBuffer);
    }
}
```



