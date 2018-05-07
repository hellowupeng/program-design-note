# 应用程序的生命周期

### 概览

![](/assets/The execution states of a UIKit app.png)

### 管理生命周期事件

系统通知应用代理以下过渡：

* 启动（Launch）。
* 激活（Activation）。
* 停用（Deactivation）。
* 后台执行（Background execution）。
* 终止（Termination）。

### 管理行为事件

应用程序代理还会响应其他一些重要事件：

* 内存警告。
* 时间变化。
* 受保护的数据变为可用/不可用。
* 状态恢复。
* 切换任务。
* 打开 URLs。
* 应用程序间通信。
* 文件下载。



