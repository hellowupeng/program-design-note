# 面试题

1. 不用中间变量,用两种方法交换A和B的值

   1. 中间变量

      ```c
      void swap(int a, int b) {

         int temp = a;

         a = b;

         b = temp;

      }
      ```

   2. 加法

      ```c
      void swap(int a, int b) {

         a = a + b;

         b = a - b;

         a = a - b;

      }
      ```

   3. 异或（相同为0，不同为1. 可以理解为不进位加法）

      ```c
      void swap(int a, int b) {

         a = a ^ b;

         b = a ^ b;

         a = a ^ b;

      }
      ```

2. 求最大公约数

   1. 直接遍历法

      ```c
      int maxCommonDivisor(int a, int b) {

          int max = 0;

          for (int i = 1; i <=b; i++) {

              if (a % i == 0 && b % i == 0) {

                  max = i;

              }

          }

          return max;

      }
      ```

   2. 辗转相除法

      ```c
      int maxCommonDivisor(int a, int b) {

          int r;

          while(a % b > 0) {

              r = a % b;

              a = b;

              b = r;

          }

          return b;

      }
      ```

   3. 扩展：最小公倍数 = \(a \* b\)/最大公约数

3. 模拟栈操作

   栈是一种数据结构，特点：先进后出

   练习：使用全局变量模拟栈的操作

   ```c
   #include <stdio.h>
   #include <stdbool.h>
   #include <assert.h>

   //保护全局变量：在全局变量前加static后，这个全局变量就只能在本文件中使用
   static int data[1024];//栈最多能保存1024个数据

   static int count = 0;//目前已经放了多少个数(相当于栈顶位置)

   //数据入栈 push
   void push(int x){
     	assert(!full());//防止数组越界
   	data[count++] = x;
   }

   //数据出栈 pop
   int pop(){
   	assert(!empty());
   	return data[--count];
   }

   //查看栈顶元素 top
   int top(){
   	assert(!empty());
   	return data[count-1];
   }

   //查询栈满 full
   bool full() {
   	if(count >= 1024) {
        	return 1;
   	}
        return 0; 
   }

   //查询栈空 empty
   bool empty() {
   	if(count <= 0) {
   		return 1;
   	}
       return 0;
   }

   int main(){
       //入栈
       for (int i = 1; i <= 10; i++) {
           push(i);
       }

       //出栈
       while(!empty()){
           printf("%d ", top()); //栈顶元素
           pop(); //出栈
       }

       printf("\n");
       return 0;
   }
   ```

   1. 排序算法

      选择排序、冒泡排序、插入排序三种排序算法可以总结为如下：

      都将数组分为已排序部分和未排序部分。

      1\) 选择排序将已排序部分定义在左端，然后选择未排序部分的最小元素和未排序部分的第一个元素交换。

      2\) 冒泡排序将已排序部分定义在右端，在遍历未排序部分的过程执行交换，将最大元素交换到最右端。

      3\) 插入排序将已排序部分定义在左端，将未排序部分元的第一个元素插入到已排序部分合适的位置。

      1. 选择排序

         【选择排序】：最值出现在起始端

         第1趟：在n个数中找到最小\(大\)数与第一个数交换位置

         第2趟：在剩下n-1个数中找到最小\(大\)数与第二个数交换位置  
         重复这样的操作...依次与第三个、第四个...数交换位置

         第n-1趟，最终可实现数据的升序（降序）排列。

         ```c
         void selectSort(int *arr, int length) {

             for (int i = 0; i < length - 1; i++) { //趟数

                 for (int j = i + 1; j < length; j++) { //比较次数

                     if (arr[i] > arr[j]) {

                         int temp = arr[i];

                         arr[i] = arr[j];

                         arr[j] = temp;

                     }

                 }

             }

         }
         ```

      2. 冒泡排序

         【冒泡排序】：相邻元素两两比较，比较完一趟，最值出现在末尾

         第1趟：依次比较相邻的两个数，不断交换（小数放前，大数放后）逐个推进，最值最后出现在第n个元素位置

         第2趟：依次比较相邻的两个数，不断交换（小数放前，大数放后）逐个推进，最值最后出现在第n-1个元素位置

         第n-1趟：依次比较相邻的两个数，不断交换（小数放前，大数放后）逐个推进，最值最后出现在第2个元素位置

         ```c
         void bublleSort(int *arr, int length) {

             for(int i = 0; i < length - 1; i++) { //趟数

                 for(int j = 0; j < length - i - 1; j++) { //比较次数

                     if(arr[j] > arr[j+1]) {

                         int temp = arr[j];

                         arr[j] = arr[j+1];

                         arr[j+1] = temp;

                     }

                 } 

             }

         }
         ```

      3. 插入排序

4. 折半查找（二分查找）

   折半查找：优化查找时间（不用遍历全部数据）

   折半查找的原理：

   1. 数组必须是有序的

   2. 必须已知min和max（知道范围）

   3. 动态计算mid的值，取出mid对应的值进行比较

   4. 如果mid对应的值大于要查找的值，那么max要变小为mid-1

   5. 如果mid对应的值小于要查找的值，那么min要变大为mid+1

   已知一个有序数组, 和一个key, 要求从数组中找到key对应的索引位置

   ```c
   int findKey(int *arr, int length, int key) {

       int min = 0, max = length - 1, mid;

       while (min <= max) {

           mid = (min + max) / 2; //计算中间值

           if (key > arr[mid]) {

               min = mid + 1;

           } else if (key < arr[mid]) {

               max = mid - 1;

           } else {

               return mid;

           }

       }

       return -1;

   }
   ```



