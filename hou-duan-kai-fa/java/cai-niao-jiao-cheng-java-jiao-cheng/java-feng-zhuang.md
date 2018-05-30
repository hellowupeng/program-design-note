# Java 封装

### 封装的优点

1. 良好的封装能够减少耦合。

2. 类内部的结构可以自由修改。

3. 可以对成员变量进行更精确的控制。

4. 隐藏信息，实现细节。

### 实现Java封装的步骤

1. 修改属性的可见性来限制对属性的访问（一般限制为private）。

   ```java
   public class Person {
       private String name;
       private int age;
   }
   ```

2. 对每个值属性提供对外的公共方法访问，也就是创建一对赋取值方法，用于对私有属性的访问。

   ```java
   public class Person{
       private String name;
       private int age;
   ​
       public int getAge(){
         return age;
       }
   ​
       public String getName(){
         return name;
       }
   ​
       public void setAge(int age){
         this.age = age;
       }
   ​
       public void setName(String name){
         this.name = name;
       }
   }
   ```



