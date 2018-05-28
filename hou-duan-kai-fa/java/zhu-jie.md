# 注解

### 注解的定义

注解通过`@interface`关键字进行定义。

```java
// 创建一个名字为 TestAnnotation 的注解
public @interface TestAnnotation {
}
```

（可以理解为创建一张名字为 TestAnnotation 的标签。）

### 注解的应用

```java
@TestAnnotation
public class Test {
}
```

（可以理解为将 TestAnnotation 这张标签贴到 Test 这个类上面。）

### 元注解

 元注解是可以注解到注解上的注解，或者说元注解是一种基本注解，能够应用到其他的注解上面。

（元注解也是一张标签，但是它是一张特殊的标签，它的作用和目的是给其他普通的标签进行解释说明的。）

元注解有 @Retention、@Documented、@Target、@Inherited、@Repeatable 5种

##### @Retention

Retention 以为保留期的意思。当 @Retention 应用到一个注解上时，它解释说明了这个注解的存活时间。

取值：

* RetentionPolicy.SOURCE 注解只在源码阶段保留，在编译器进行编译时他将被丢弃忽视。
* RetentionPolicy.CLASS 注解只被保留到编译进行的时候，它并不会被加载到 JVM 中。
* RetentionPolicy.RUNTIME 注解可以保留到程序运行的时候，他会被加载进入到 JVM 中，所以在程序运行时可以获取到它们。

@Retention 去给一张标签解释的时候，它指定了这张标签张贴的时间。@Retention 相当于给一张标签上面盖了一张时间戳，时间戳指明了标签张贴的时间周期。

```
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation {
}
```

上面的代码中，指定 TestAnnotation 可以在程序运行周期被获取到，因此它的生命周期非常长。

##### @Documented

这个元注解和文档有关。它的作用是能够将注解中的元素包含到 Javadoc 中去。

##### @Target

Target 是目标的意思，@Target 指定了注解运用的地方。

当一个注解被 @Target 注解时，这个注解就被限定了运用的场景。

类比到标签，原本标签是你想张贴到哪个地方就到哪个地方，但是因为 @Target 的存在，它张贴的地方就非常具体了，比如只能张贴到方法上、类上、方法参数上等等。

@Target 取值：

* ElementType.ANNOTATION\_TYPE 可以给一个注解进行注解
* ElementType.CONSTRUCTOR 可以给构造方法进行注解
* ElementType.FIELD 可以给属性进行注解
* ElementType.LOCAL\_VARIABLE 可以给局部变量进行注解
* ElementType.METHOD 可以给方法进行注解
* ElementType.PACKAGE 可以给一个包进行注解
* ElementType.PARAMTER 可以给一个方法内的参数进行注解
* ElementType.TYPE 可以给一个类型进行注解，比如类、接口、枚举

##### @Inherited

如果一个超类被 @Inherited 注解注解过的注解进行注解的话，那么如果他的子类没有被任何注解应用的话，那么这个子类就继承了超类的注解。

```java
@Inherited
@Retention(RetentionPolicy.RUNTIME)
@interface Test {}

@Test
public class A {}

public class B extends A {}
```

注解 Test 被 @Inherited 修饰，之后类 A 被 Test 注解，类 B 继承 A 类，类 B 也拥有 Test 这个注解。

##### @Repeatable

可重复应用的注解

```java
@interface Persons {
    Person[] value();
}

@Repeatable(Persons.class)
@interface Person {
    String role default "";
}

@Person(role="artist")
@Person(role="coder")
@Person(role="PM")
public class SuperMan {}
```

@Repeatable 注解了 Person。@Repeatable 后面括号中的类相当于一个容器注解。

容器注解就是用来存放其他注解的地方。它本身也是一个注解。

容器注解：

```java
@interface Persons {
    Person[] value();
}
```

按照规定，它里面必须要有一个 value 属性，属性类型是一个被 @Repeatable 注解过的注解数组。

### 注解的属性

注解的属性也叫做成员变量。注解只有成员变量，没有方法。注解的成员变量在注解定义中以”无形参的方法“形式来声明，其方法名定义了该成员变量的名字，其返回值定义了该成员变量的类型。

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation {
    int id();
    
    String msg();
}
```

上面代码定义了 TestAnnotation 这个注解中拥有 id 和 msg 两个属性。在使用的时候，我们应该给它们进行赋值。

赋值的方式是在注解括号内以 value="" 形式，多个属性之前用逗号（，）隔开。

```java
@TestAnnotation(id=3, msg="hello annotation")
public class Test {}
```

注意在注解中定义属性时它的类型必须是八种基本数据类型外加类、接口、注解及它们的数组。

注解中属性可以有默认值，默认值需要用 default 关键值指定。比如：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation {
    
    public int id() default -1;
    
    public String msg() default "Hi";
}
```

TestAnnotation 中 id 属性默认值为 -1，msg 属性默认值为 Hi。

它可以这样使用。

```
@TestAnnotation()
public class Test {}
```

因为有默认值了，所以无需再 @TestAnnotation 后面的括号里面进行赋值了，这一步可以省略。

如果一个注解内仅仅只有一个名字为 value 的属性时，应用这个注解时可以直接将属性值填写到括号内。

```
public @interface Check {
    String value();
}
```

Check 注解只有 value 这个属性。可以这样应用：

```
@Check("hi")
int a;
```

等价于：

```
@Check(value="hi")
int a;
```

注解没有任何属性时，应用这个注解时，可以省略括号：

```
public @interface Perform {}
```

```
@Perform
public void testMethod(){}
```

### Java 预置的注解

##### @Deprecated

用来标记过时的元素。编译器在编译阶段遇到这个注解时会发出提醒警告，告诉开发者正在调用一个过时的元素比如过时的方法、过时的类、过时的成员变量。

```java
public class Hero {

    @Deprecated
    public void say() {
        System.out.println("Nothing has to say!");
    }
    
    public void speak() {
        System.out.println("I have a dream!");
    }
}
```

![](/assets/@Deprecated 注解.png)

##### @Override

提示子类要复写父类中被 @Override 修饰的方法。

##### @SuppressWarnings

阻止警告。可用于忽略编译器警告。

```java
@SuppressWarnings("deprecation")
public void test1() {
    Hero hero = new Hero();
    hero.say();
    hero.speak();
}
```

##### @SafeVarargs

参数安全类型注解。它的目的是提醒开发者不要用参数做一些不安全的操作，它的存在会阻止编译器产生 unchecked 警告。

##### @FunctionalInterface

函数式接口注解。

###### 函数式接口（Functional Interface）就是一个具有一个方法的普通接口。

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

### 注解的提取

##### 注解与反射

注解通过反射获取。首先可以通过 Class 对象的 isAnnotationPresent\(\) 方法判断它是否应用了某个注解

```java
public boolean isAnnotationPresent(Class<? extends Annotation> annotationClass) {}
```

然后通过 getAnnotation\(\) 方法来获取 Annotation 对象。

```java
public <A extends Annotation> A getAnnotation(Class<A> annotationClass) {}
```

或者是 getAnnotations\(\) 方法：

```java
public Annotation[] getAnnotations() {}
```

前一种方法返回指定类型的注解，后一种方法返回注解到这个元素上的所有注解。

如果获取到的 Annotation 如果不为 null，则就可以调用它们的属性方法了。比如

```java
@TestAnnotation()
public class Test {
    public static void main(String[] args) {
        boolean hasAnnotation = Test.class.isAnnotationPresent(TestAnnotation.class);
        if (hasAnnotation) {
            TestAnnotation testAnnotation = Test.class.getAnnotation(TestAnnotation.class);
            
            System.out.println("id:"+testAnnotation.id());
            System.out.println("msg:"+testAnnotation.msg());
        }
    }
}
```

程序的运行结果是：

```
id: -1
msg:
```

### 注解的使用场景

注解的主要用处：

* 提供信息给编译器： 编译器可以利用注解来探测错误和警告信息

* 编译阶段时的处理： 软件工具可以用来利用注解信息来生成代码、Html文档或者做其它相应处理。

* 运行时的处理： 某些注解可以在程序运行的时候接受代码的提取

（注解不是代码本身的一部分。当开发者使用了Annotation 修饰了类、方法、Field 等成员之后，这些 Annotation 不会自己生效，必须由开发者提供相应的代码来提取并处理 Annotation 信息。这些处理提取和处理 Annotation 的代码统称为 APT（Annotation Processing Tool\)。）

### 总结

1. 如果注解难于理解，你就把它类同于标签，标签为了解释事物，注解为了解释代码。
2. 注解的基本语法，创建如同接口，但是多了个 @ 符号。
3. 注解的元注解。
4. 注解的属性。
5. 注解主要给编译器及工具类型的软件用的。
6. 注解的提取需要借助于 Java 的反射技术，反射比较慢，所以注解使用时也需要谨慎计较时间成本。

###### 参考资料

1. [秒懂，Java 注解 （Annotation）你可以这样学](https://blog.csdn.net/briblue/article/details/73824058)



