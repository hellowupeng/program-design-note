# 语言基础

### 表达式，变量和常量

##### 代码注释

###### **单行注释**

```Kotlin
// This is a comment. It is not executed.
```

```
// This is also a comment.
// Over multiple lines.
```

###### **多行注释**

```
/* This is also a comment.
   Over many..
   many...
   many lines. */
```

###### **嵌套注释**

```
/* This is a comment.

  /* And inside it
  is
  another comment.
  */

  Back to the first.
*/
```

##### 打印

```
println("Hello, Kotlin Apprentice reader!")
```

##### 算数运算

###### 移位操作

* 左移：`shl`
* 右移：`shr`

```
1 shl 3 // 8

32 shr 2 // 8
```

左移一位等同于乘以2，右移一位等同于除以2。

##### 数学函数

```
sin(45 * PI / 180)
// 0.7071067811865475

cos(135 * PI / 180)
// -0.7071067811865475
```

```
sqrt(2.0)
// 1.414213562373095
```

```
max(5, 10)
// 10

min(-5, -10)
// -10
```

##### 命名数据

###### 常量（Constants）

Int:

```
val number: Int = 10
```

Double:

```
val pi: Double = 3.14159
```

浮点型：Float

Double 的精度约为 Float 的两倍。

一旦声明常量，就不能修改它的数据。

```
number = 0
Val cannot be reassigned
```

###### 变量（Variables）

```
var variableNumber: Int = 42
```

```
variableNumber = 0
variableNumber = 1_000_000
```

###### 使用有意义的名字

好名字：

* personAge
* numberOfPeople
* gradePointAverage

坏名字：

* a
* temp
* average

### 类型和操作

##### 类型转换



