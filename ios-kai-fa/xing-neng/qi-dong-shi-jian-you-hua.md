# 启动时间优化

时间分为两个部分 T1\(main前\) + T2\(main后\), {T1:系统环境布局时间:创建进程，加载解析可执行文件\(库加载，堆栈环境配置等等\)， T2：从main函数到第一个界面显示时间}

1. 库加载越多，启动越慢

2. Objc类越多，越慢

3. 静态对象全局对象越多，启动越慢

4. Objc的 +load越多，启动越慢

#### **我们比较能把控的点：load 和 T2时间上进行优化**

每个objc类都有一个 +load函数，而它的调用优先级在调用main函数之前

通过以下操作可以论证上述的观点

![](/assets/启动优化3.png)

![](/assets/启动优化2.png)

编译，控制台输出如下：![](/assets/启动优化1.png)结论1:尽量避免过多使用+load方法，可以使用+initlalize替代

在系统第一次使用到这个类的使用，才会使用到他的+\(void\)initlalize方法，比如：![](/assets/qidongyouhua2.png)结论2:减少- \(BOOL\)application:\(UIApplication \*\)application didFinishLaunchingWithOptions:\(NSDictionary \*\)launchOptions里面的操作，在启动页出现后再进行相关操作.（例如放到子线程中去执行）

#### **关于每个objc文件的+load函数的执行循序**

每个objc文件都有+load方法，那么他们的执行顺序是怎么样的？

![](/assets/启动优化5.png)

此处的文件顺序，决定了+load的执行顺序

