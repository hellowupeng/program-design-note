# 应用程序启动过程

1. 应用由用户或系统启动。
2. Xcode提供的 main 函数调用 UIKit 的 UIApplicationMain 函数。
3. UIApplicationMain 函数创建 UIApplication 对象和应用代理。
4. UIKit 从 main storyboard 或 nib 文件加载应用的默认界面。
5. UIKit 调用应用代理的 application:willFinishLaunchingWithOptions: 方法。
6. UIKit 执行状态恢复，调用应用代理和视图控制器的其他方法。
7. UIKit 调用应用代理的 application:didFinishLaunchingWithOptions: 方法。



