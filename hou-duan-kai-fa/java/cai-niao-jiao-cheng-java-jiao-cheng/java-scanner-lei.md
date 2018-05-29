# Java Scanner 类

java.util.Scanner 是 Java5 的新特征，我们可以通过 Scanner 类来获取用户的输入。

```java
Scanner s = new Scanner(System.in);
```

通过 Scanner 类的 next\(\) 与 nextLine\(\) 方法获取输入的字符串，在读取前我们一般需要 使用 hasNext 与 hasNextLine 判断是否还有输入的数据：

##### 使用 next 方法：

```java
import java.util.Scanner; 
 
public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        // 从键盘接收数据
 
        // next方式接收字符串
        System.out.println("next方式接收：");
        // 判断是否还有输入
        if (scan.hasNext()) {
            String str1 = scan.next();
            System.out.println("输入的数据为：" + str1);
        }
        scan.close();
    }
}
```

##### 使用 nextLine 方法：

```java
import java.util.Scanner;
 
public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        // 从键盘接收数据
 
        // nextLine方式接收字符串
        System.out.println("nextLine方式接收：");
        // 判断是否还有输入
        if (scan.hasNextLine()) {
            String str2 = scan.nextLine();
            System.out.println("输入的数据为：" + str2);
        }
        scan.close();
    }
```

##### next\(\) 与 nextLine\(\) 区别

next\(\):

* 一定要读取到有效字符后才可以结束输入。

* 对输入有效字符之前遇到的空白，next\(\) 方法会自动将其去掉。

* 只有输入有效字符后才将其后面输入的空白作为分隔符或者结束符。

* next\(\) 不能得到带有空格的字符串。

nextLine\(\):

* 以Enter为结束符,也就是说 nextLine\(\)方法返回的是输入回车之前的所有字符。

* 可以获得空白。



