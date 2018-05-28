# Java 修饰符

Java 修饰符主要分为两类：

* 访问修饰符
* 非访问修饰符

### 访问控制修饰符

使用访问控制符来保护对类、变量、方法和构造方法的访问。Java 支持 4 种不同的访问权限：

* default（即缺省，什么也不写）：在同一个包内可见，不使用任何修饰符。使用对象：类、接口、变量、方法。
* private：在同一个类可见。使用对象：变量、方法。不能修饰类（外部类）
* public：对所有类可见。使用对象：类、接口、变量、方法。
* protected：对同一包内的类和所有子类可见。使用对象：变量、方法。不能修饰类（外部类）

##### 默认访问修饰符-不使用任何关键字

接口里变量都隐式声明为 `public static final`，接口里的方法默认情况下访问权限为 `public`。

##### 私有访问修饰符-private

private 访问修饰符的使用注意用来隐藏类的实现细节和保护类的数据。

##### 访问控制和继承

* 父类中声明为 public 的方法在子类中也必须为 public。
* 父类中声明为 protected 的方法在子类中要么声明为 protected，要么声明为 public，不能为 private。
* 父类中声明为 private 的方法，不能够被继承。

### 非访问修饰符

static 修饰符，用来修饰类方法和类变量。

final 修饰符，用来修饰类、方法和变量，final 修饰的类不能被继承，修饰的方法不能被继承类重新定义，修饰的变量为常量，是不可修改的。

abstract 修饰符，用来创建抽象类和抽象方法。

synchronized 和 volatile 修饰符，主要用于线程编程。

##### final 修饰符

###### final 变量

final 对象的引用不能改变，但是里面的值可以改变。

###### final方法

final 方法可以被子类继承，但不能被子类修改。

```java
public class Test {
    public final void changeName() {
        // 方法体
    }
}
```

###### final 类

final 类不能被继承，没有类能够继承 final 类的任何特性。

```java
public final class Test {
    // 类体
}
```

##### abstract 修饰符

###### 抽象类

抽象类不能用来实例化对象。

一个类不能同时被 abstract 和 final 修饰。如果类包含抽象方法，那么该类一定要声明为抽象类，否则将出现编译错误。

抽象类可以包含抽象方法和非抽象方法。

```java
abstract class Caravan {
    private double price;
    private String model;
    private String year;
    public abstract void goFast(); // 抽象方法
    public abstract void changeColor();
}
```

###### 抽象方法

抽象方法是没有任何实现的方法，方法的具体实现由子类提供。

任何继承抽象类的子类必须实现父类的所有抽象方法，除非该子类也是抽象类。

```java
public abstract class SuperClass {
    abstract void m(); // 抽象方法
}

class SubClass extends SuperClass {
    // 实现抽象方法
    void m() {
        ...
    }
}
```

##### synchronized 修饰符

synchronized 关键字声明的方法同一时间只能被一个线程访问。

```java
public synchronized void showDetails() {
    ...
}
```

##### transient 修饰符

序列化的对象包含被 transient 修饰的实例变量时，Java 虚拟机（JVM）跳过该特定变量。

该修饰符包含在定义变量的语句中，用来预处理类和变量的数据类型。

```java
public transient int limit = 55; // 不会持久化
public int b; // 持久化
```

##### volatile 修饰符

volatile 修饰的成员变量在每次被线程访问时，都强制从共享内存中重新读取该成员变量的值。而且，当成员变量发生变化时，会强制线程将变化值回写到共享内存。这样在任何时刻，两个不同的线程总是看到某个成员变量的同一个值。

一个 volatile 对象引用可能是 null。



