# Java 流（Stream）、文件（File）和 IO

一个流可以理解为一个数据的序列。输入流表示从一个源读取数据，输出流表示向一个目标写数据。

### 读取控制台输入

Java 的控制台输入由 System.in 完成。

为了获得一个绑定到控制台的字符流，你可以把 System.in 包装在一个 BufferedReader 对象中来创建一个字符流。

```java
BufferedReader br = new BufferedReader(new 
                      InputStreamReader(System.in));
```

用 read\(\) 方法从控制台不断读取字符直到用户输入 "q"。

```java
// 使用 BufferedReader 在控制台读取字符
 
import java.io.*;
 
public class BRRead {
  public static void main(String args[]) throws IOException
  {
    char c;
    // 使用 System.in 创建 BufferedReader 
    BufferedReader br = new BufferedReader(new 
                       InputStreamReader(System.in));
    System.out.println("输入字符, 按下 'q' 键退出。");
    // 读取字符
    do {
       c = (char) br.read();
       System.out.println(c);
    } while(c != 'q');
  }
}
```

### 从控制台读取字符串

从标准输入读取一个字符串需要使用 BufferedReader 的 readLine\(\) 方法。

### 控制台输出

控制台的输出由 print\( \) 和 println\(\) 完成。这些方法都由类 PrintStream 定义，System.out 是该类对象的一个引用。

### 读写文件

下图是一个描述输入流和输出流的类层次图。

![](/assets/下图是一个描述输入流和输出流的类层次图.png)

### FileInputStream

使用字符串类型的文件名来创建一个输入流对象来读取文件：

```java
InputStream f = new FileInputStream("C:/java/hello");
```

使用一个文件对象来创建一个输入流对象来读取文件。我们首先得使用 File\(\) 方法来创建一个文件对象：

```java
File f = new File("C:/java/hello");
InputStream out = new FileInputStream(f);
```

### FileOutputStream

该类用来创建一个文件并向文件中写数据。

如果该流在打开文件进行输出前，目标文件不存在，那么该流会创建该文件。

有两个构造方法可以用来创建 FileOutputStream 对象。

使用字符串类型的文件名来创建一个输出流对象：

```java
OutputStream f = new FileOutputStream("C:/java/hello")
```

使用一个文件对象来创建一个输出流来写文件。我们首先得使用File\(\)方法来创建一个文件对象：

```java
File f = new File("C:/java/hello");
OutputStream f = new FileOutputStream(f);
```

### Java中的目录

##### 创建目录：

* mkdir\(\) 方法创建一个文件夹，成功则返回true，失败则返回false。失败表明File对象指定的路径已经存在，或者由于整个路径还不存在，该文件夹不能被创建。
* mkdirs\(\) 方法创建一个文件夹和它的所有父文件夹。

```java
import java.io.File;
 
public class CreateDir {
  public static void main(String args[]) {
    String dirname = "/tmp/user/java/bin";
    File d = new File(dirname);
    // 现在创建目录
    d.mkdirs();
  }
}
```

##### 读取目录

##### 删除目录或文件

删除文件可以使用 **java.io.File.delete\(\) **方法。

