# Java 包

为了更好地组织类，Java 提供了包机制，用于区别类名的命名空间。

### 包的作用

Java 使用包（package）这种机制是为了防止命名冲突，访问控制，提供搜索和定位类（class）、接口、枚举（enumerations）和注释（annotation）等。

语法：

```java
package pkg1[．pkg2[．pkg3…]];
```

### package 的目录结构

类放在包中会有两种主要的结果：

1. 包名成为类名的一部分。
2. 包名必须与相应的字节码所在的目录结构相吻合。



