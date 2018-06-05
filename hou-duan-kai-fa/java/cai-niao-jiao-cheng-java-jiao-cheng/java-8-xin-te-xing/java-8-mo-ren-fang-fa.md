# Java 8 默认方法

默认方法就是接口可以有实现方法，而且不需要实现类去实现其方法。

只需在方法名前面加个default关键字即可实现默认方法。

### 语法

```java
public interface vehicle {
   default void print(){
      System.out.println("我是一辆车!");
   }
}
```

### 多个默认方法

一个接口有默认方法，考虑这样的情况，一个类实现了多个接口，且这些接口有相同的默认方法。

第一个解决方案是创建自己的默认方法，来覆盖重写接口的默认方法：

```java
public class car implements vehicle, fourWheeler {
   default void print(){
      System.out.println("我是一辆四轮汽车!");
   }
}
```

第二种解决方案可以使用 super 来调用指定接口的默认方法：

```java
public class car implements vehicle, fourWheeler {
   public void print(){
      vehicle.super.print();
   }
}
```

### 静态默认方法

ava 8 的另一个特性是接口可以声明（并且可以提供实现）静态方法。例如：



