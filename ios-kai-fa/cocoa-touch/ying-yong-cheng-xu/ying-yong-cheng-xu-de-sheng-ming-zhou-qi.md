# 应用程序的生命周期

### 概览

![](/assets/The execution states of a UIKit app.png)

### 管理生命周期事件

系统通知应用代理以下过渡（transitions）：

* 启动（Launch）。
* 激活（Activation）。
* 停用（Deactivation）。
* 后台执行（Background execution）。
* 终止（Termination）。

### 管理行为事件

应用程序代理（app delegate）还会响应其他一些重要事件：

* 内存警告。
* 时间变化。
* 受保护的数据变为可用/不可用。
* 状态恢复。
* 切换任务。
* 打开 URLs。
* 应用程序间通信。
* 文件下载。

###### 参考资料

1. [Managing Your App's Life Cycle](https://developer.apple.com/documentation/uikit/core_app/managing_your_app_s_life_cycle?language=objc)



