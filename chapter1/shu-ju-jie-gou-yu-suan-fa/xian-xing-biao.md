# 线性表

线性表是最常用且最简单的一种数据结构，它是n个数据元素的有限序列。实现线性表的方式一般有两种，一种是使用数组存储线性表的元素，即用一组连续的存储单元依次存储线性表的数据元素。另一种是使用链表存储线性表的元素，即用一组任意的存储单元存储线性表的数据元素（存储单元可以是连续的，也可以是不连续的）。

### 数组实现

数组是一种大小固定的数据结构，对线性表的所有操作都可以通过数组来实现。虽然数组一旦创建之后，它的大小就无法改变了，但是当数组不能再存储线性表中的新元素时，我们可以创建一个新的大的数组来替换当前数组。这样就可以使用数组实现动态的数据结构。

* 代码1 创建一个更大的数组来替换当前数组

  ```
  int[] oldArray = new int[10];

  int[] newArray = new int[20];

  for (int i = 0; i < oldArray.length; i++) {
      newArray[i] = oldArray[i];
  }

  // 也可以使用System.arraycopy方法来实现数组间的复制     
  // System.arraycopy(oldArray, 0, newArray, 0, oldArray.length);

  oldArray = newArray;
  ```

* 代码2 在数组位置index上添加元素e

  ```
  //oldArray 表示当前存储元素的数组
  //size 表示当前元素个数
  public void add(int index, int e) {

      if (index > size || index < 0) {
          System.out.println("位置不合法...");
      }

      //如果数组已经满了 就扩容
      if (size >= oldArray.length) {
          // 扩容函数可参考代码1
      }

      for (int i = size - 1; i >= index; i--) {
          oldArray[i + 1] = oldArray[i];
      }

      //将数组elementData从位置index的所有元素往后移一位
      // System.arraycopy(oldArray, index, oldArray, index + 1,size - index);

      oldArray[index] = e;

      size++;
  }
  ```

  上面简单写出了数组实现线性表的两个典型函数，具体我们可以参考Java里面的ArrayList集合类的源码。数组实现的线性表优点在于可以通过下标来访问或者修改元素，比较高效，主要缺点在于插入和删除的花费开销较大，比如当在第一个位置前插入一个元素，那么首先要把所有的元素往后移动一个位置。为了提高在任意位置添加或者删除元素的效率，可以采用链式结构来实现线性表。

### 链表

链表是一种物理存储单元上非连续、非顺序的存储结构，数据元素的逻辑顺序是通过链表中的指针链接次序实现的。链表由一系列节点组成，这些节点不必在内存中相连。每个节点由数据部分Data和链部分Next，Next指向下一个节点，这样当添加或者删除时，只需要改变相关节点的Next的指向，效率很高。

但链表的结构：![](/assets/单链表的结构.png)下面主要用代码来展示链表的一些基本操作，需要注意的是，这里主要是以单链表为例，暂时不考虑双链表和循环链表。

* 链表的节点

  ```
  class Node<E> {

      E item;
      Node<E> next;

      //构造函数
      Node(E element) {
         this.item = element;
         this.next = null;
     }
  }
  ```

* 定义好节点后，使用前一般是对头节点和尾节点进行初始化

  ```
  //头节点和尾节点都为空 链表为空
  Node<E> head = null;
  Node<E> tail = null;
  ```

* 空链表创建一个新节点

  ```
  //创建一个新的节点 并让head指向此节点
  head = new Node("nodedata1");

  //让尾节点也指向此节点
  tail = head;
  ```

###### 参考资料

1. [常见数据结构与算法整理总结](https://www.jianshu.com/p/230e6fde9c75)



