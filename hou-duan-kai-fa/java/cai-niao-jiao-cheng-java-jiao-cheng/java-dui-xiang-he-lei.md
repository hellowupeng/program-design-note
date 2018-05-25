# Java 对象和类

### 构造方法

每个类都有构造方法。如果没有显式地为类定义构造方法，Java编译器将会为该类提供一个默认构造方法。在创建一个对象的时候，至少要调用一个构造方法。构造方法的名称必须与类同名，一个类可以有多个构造方法。

构造方法示例：

```java
public class Puppy{
    public Puppy(){
    }
 
    public Puppy(String name){
        // 这个构造器仅有一个参数：name
    }
}
```

### 创建对象

创建对象需要三步：

1. **声明**：声明一个对象，包括对象名称和对象类型。
2. **实例化**：使用关键字new来创建一个对象。
3. **初始化**：使用new创建对象时，会调用构造方法初始化对象。

创建对象例子：

```java
public class Puppy{
   public Puppy(String name){
      //这个构造器仅有一个参数：name
      System.out.println("小狗的名字是 : " + name ); 
   }
   public static void main(String []args){
      // 下面的语句将创建一个Puppy对象
      Puppy myPuppy = new Puppy( "tommy" );
   }
}
```

### 访问实例变量和方法

```java
/* 实例化对象 */
ObjectReference = new Constructor();
/* 访问类中的变量 */
ObjectReference.variableName;
/* 访问类中的方法 */
ObjectReference.MethodName();
```

### 源文件声明规则

* 一个源文件只能有一个 public 类
* 一个源文件可以有多个非 public 类
* 源文件的名称应该和 public 类的类名保持一致。
* 如果一个类定义在某个包中，那么 package 语句应该在源文件首行。
* import 语句放在 package 语句和类定义之间。

### Java 包

包主要用来对类和接口进行分类。

### Import 语句

Import 语句用来提供一个合理的路径，使得编译器可以找到某个类。





