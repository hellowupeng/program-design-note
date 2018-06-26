# 架构模式

##### 1、讲一下 MVC 和 MVVM，MVP？

• Models（模型） — 数据层，或者负责处理数据的 数据接口层。比如 Person 和 PersonDataProvider 类

• Views（视图） - 展示层\(GUI\)。对于 iOS 来说所有以 UI 开头的类基本都属于这层。

• Controller/Presenter/ViewModel（控制器/展示器/视图模型） - 它是 Model 和 View 之间的胶水或者说是中间人。一般来说，当用户对 View 有操作时它负责去修改相应 Model；当 Model 的值发生变化时它负责去更新对应 View。

###### MVC![](/assets/mvc.png)

Controller： 控制器层，它是 Model 和 View 之间的胶水或者说是中间人。一般来说，当用户对 View 有操作时它负责去修改相应 Model；当 Model 的值发生变化时它负责去更新对应 View。

###### MVVM![](/assets/MVVM.png)

在MVVM 中，view 和 view controller正式联系在一起，我们把它们视为一个组件

view 和 view controller 都不能直接引用model，而是引用视图模型（viewModel）

viewModel 是一个放置用户输入验证逻辑，视图显示逻辑，发起网络请求和其他代码的地方

###### MVP![](/assets/mvp.png)

MVP 是从经典的模式MVC演变而来，它们的基本思想有相通的地方：Controller/Presenter负责逻辑的处理，Model提供数据，View负责显示。作为一种新的模式，MVP与MVC有着一个重大的区别：在MVP中View并不直接使用Model，它们之间的通信是通过Presenter \(MVC中的Controller\)来进行的，所有的交互都发生在Presenter内部，而在MVC中View会从直接Model中读取数据而不是通过 Controller。



参考

1、[2018年1月iOS招人心得（附面试题）- 答案整理](https://blog.csdn.net/qxuewei/article/details/79418952#commentBox)

