# 1、算法简介

算法是一组完成任务的指令。

二分查找是一种算法，其输入是一个有序的元素列表。

一般而言，对于包含n个元素的列表，用二分查找最多需要log2n步，而简单查找最多需要n步。

> 对数：你可能不记得什么是对数了，但很可能记得什么是幂。log10100相当于问“将多少个10相乘 的结果为100”。答案是两个:10×10 = 100。因此，log10100 = 2。**对数运算是幂运算的逆运算。**

* 函数binary\_search接受一个有序数组和一个元素。如果指定的元素包含在数组中，这个函数将返回其位置。
  ```py
  def binary_search(list, item):
      low = 0
      high = len(list) - 1
    
      while low <= high:
          mid = (low + high)/2
          guess = list(mid)
          if guess == item:
              return mid
          if guess > item:
              high = mid - 1
          else:
              low = mid + 1
      return None
    
  my_list = [1, 3, 5, 7, 9]

  print binary_search(my_list, 3) # => 1
  print binary_search(my_list, -1) # => None
  ```



