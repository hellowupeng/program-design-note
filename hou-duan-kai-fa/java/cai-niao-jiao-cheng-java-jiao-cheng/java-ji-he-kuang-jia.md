# Java 集合框架

集合框架是一个用来代表和操纵集合的统一架构。所有的集合框架都包含如下内容：

* **接口**。是代表集合的抽象数据类型。接口允许集合独立操纵其代表的细节。在面向对象的语言，接口通常形成一个层次。
* **实现类**。是集合接口的具体实现。从本质上讲，它们是可重复使用的数据结构。
* **算法**。是实现集合接口的对象里的方法执行的一些有用的计算，例如：搜索和排序。这些算法被称为多态，那是因为相同的方法可以在相似的接口上有着不同的实现。

除了集合，该框架也定义了几个Map接口和类。Map里存储的是键/值对。尽管Map不是collections，但是它们完全整合在集合中。

### 集合框架体系如图所示![](/assets/集合框架体系图.png)

Java 集合框架提供了一套性能优良，使用方便的接口和类，java集合框架位于java.util包中， 所以当使用集合框架的时候需要进行导包。

### 集合接口

1. **Collection 接口**：Collection 是最基本的集合接口，一个 Collection 代表一组 Object，即 Collection 的元素, Java不提供直接继承自Collection的类，只提供继承于的子接口\(如List和set\)。

2. **List 接口**：List接口是一个有序的 Collection，使用此接口能够精确的控制每个元素插入的位置，能够通过索引\(元素在List中位置，类似于数组的下标\)来访问List中的元素，第一个元素的索引为 0，而且允许有相同的元素。

3. **Set 接口**：Set 具有与 Collection 完全一样的接口，只是行为上不同，Set 不保存重复的元素。

4. **SortedSet 接口**：继承于Set保存有序的集合。

5. **Map 接口**：将唯一的键映射到值。

6. **Map.Entry 接口**：描述在一个Map中的一个元素（键/值对）。是一个Map的内部类。

7. **SortedMap 接口**：继承于Map，使Key保持在升序排列。

8. Enumeration：这是一个传统的接口和定义的方法，通过它可以枚举（一次获得一个）对象集合中的元素。这个传统接口已被迭代器取代。

##### Set和List的区别

* Set 接口实例存储的是无序的，不重复的数据。List 接口实例存储的是有序的，可以重复的元素。

* Set检索效率低下，删除和插入效率高，插入和删除不会引起元素位置改变，实现类有 HashSet，TreeSet

* List和数组类似，可以动态增长，根据实际存储的数据的长度自动增长List的长度。查找元素效率高，插入删除效率低，因为会引起其他元素位置改变，实现类有 ArrayList，LinkedList，Vector

### 集合实现类（集合类）

Java提供了一套实现了Collection接口的标准集合类。其中一些是具体类，这些类可以直接拿来使用，而另外一些是抽象类，提供了接口的部分实现。

1. **AbstractCollection**

2. **AbstractList**

3. **AbstractSequentialList**

4. **LinkedList**：该类实现了List接口，允许有null（空）元素。主要用于创建链表数据结构，该类没有同步方法，如果多个线程同时访问一个List，则必须自己实现访问同步，解决方法就是在创建List时候构造一个同步的List。例如：

   ```java
   List list=Collections.synchronizedList(new LinkedList(...));
   ```

   LinkedList 查找效率低。

5. **ArrayList**：该类也是实现了List的接口，实现了可变大小的数组，随机访问和遍历元素时，提供更好的性能。该类也是非同步的,在多线程的情况下不要使用。ArrayList 增长当前长度的50%，插入删除效率低。

6. **AbstractSet**

7. **HashSet**：该类实现了Set接口，不允许出现重复元素，不保证集合中元素的顺序，允许包含值为null的元素，但最多只能一个。

8. LinkedHashSet

9. TreeSet

10. **AbstractMap**

11. **HashMap**：HashMap 是一个散列表，它存储的内容是键值对\(key-value\)映射。该类实现了Map接口，根据键的HashCode值存储数据，具有很快的访问速度，最多允许一条记录的键为null，不支持线程同步。

12. TreeMap

13. WeakHashMap

14. LinkedHashMap

15. IdentityHashMap

java.util包定义的类：

1. Vector：该类和ArrayList非常相似，但是该类是同步的，可以用在多线程的情况，该类允许设置默认的增长长度，默认扩容方式为原来的2倍。

2. Stack：栈是Vector的一个子类，它实现了一个标准的后进先出的栈。

3. Dictionary

4. HashTable

5. Properties

6. BitSet



