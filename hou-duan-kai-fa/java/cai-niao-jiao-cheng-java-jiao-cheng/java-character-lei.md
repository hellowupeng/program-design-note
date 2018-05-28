# Java Character 类

Character 类用于对单个字符进行操作。

Character 类在对象中包装一个基本类型 char 的值。

```java
char ch = 'a';

char uniChar = '\u039A';

char[] charArray = { 'a', 'b', 'c', 'd', 'e' };
```

Java 语言为内置数据类型 char 提供了包装类 Character 类。

```java
Character ch = new Character('a');
```

将一个 char 类型的参数传递给需要一个 Character 类型参数的方法时，编译器会自动地将 char 类型参数转换为 Character。这种特征称为装箱，反过来称为拆箱。

```java
// 原始字符 'a' 装箱到 Character 对象 ch 中
Character ch = 'a';

// 原始字符 'x' 用 test 方法装箱
// 返回拆箱的值到 'c'
char c = test('x');
```



