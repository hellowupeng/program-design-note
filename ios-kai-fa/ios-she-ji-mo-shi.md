# iOS 设计模式

### MVC

* Model: 模型
* View：视图
* Controller：控制器

### 单例

单例使用懒加载方式在第一次实例时创建，如`[NSUserDefaults standardUserDefaults]`，`[UIApplication sharedApplication]`, `[UIScreen mainScreen]`,`[NSFileManager defaultManager]`

系统的单例类：

* UIApplication
* NSNotificationCenter
* NSFileManager
* NSUserDefaults
* NSURLCache
* NSHTTPCookieStorage

```
@interface LibraryAPI : NSObject

+ (LibraryAPI *)sharedInstance;

@end

+ (LibraryAPI *)sharedInstance
{
    // 声明一个静态变量去保存类的实例，确保它在类中的全局可用
    static LibraryAPI *_sharedInstance = nil;

    // dispatch_once_t 确保初始化器只执行一次
    static dispatch_once_t oncePredicate;

    // 单例的关键，一旦类被初始化，初始化器不会再被调用
    dispatch_once(&oncePredicate, ^{
        _sharedInstance = [[LibraryAPI alloc] init];
    });
    return _sharedInstance;
}
```

```
@implementation XNShareTool

/**
 步骤：
 1.一个静态变量_instance
 2.重写allocWithZone, 在里面用dispatch_once，并调用super allocWithZone
 3.自定义一个sharedXX，用来获取单例。在里面也调用dispatch_once，实例化_instance
 --------可选--------
 4.如果要支持copy。则（先遵守NSCopying协议）重写 copyWithZone，直接返回 _instance 即可。
 */
 
/** 第1步：存储唯一实例 */
static XNShareTool *_instance;

/** 第2步：分配内存空间时会调用这个方法。保证分配分配内存 alloc 时都相同 */
+(id)allocWithZone:(struct _NSZone *)zone
{
   // 调用 dispatch_once 保证在多线程中也只被实例化一次
   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      _instance = [super allocWithZone:zone];
   });
   return _instance;
}

/** 第3步：保证 init 初始化时都相同 */
+(instanceType)sharedTool
{
   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      _instance = [[XNShareTool alloc] init];
   });
   return _instance;
}

/** 第4步：保证 copy 时都相同 */
-(id)copyWithZone:(NSZone *)zone
{
   return _instance;
}

@end
```

### 门面模式 Facade

暴露接口

### 装饰器模式 Decorator

常见的实现是 Category 和 Delegation。

### 适配器模式 Adapter

包装一个对象暴露一个标准的接口。可以使用协议的方式实现，比如 UITableViewDelegate，UIScrollViewDelegate, NSCoding 和 NSCopying 协议。

### 观察者模式 Observer

Notifications 和 Key-Value Observing\(KVO\)。

### 备忘录模式 Memento

比如 NSUserDefaults，或者类似的方式进行状态保存使得再次进入时能够和离开时一样。可以使用 UIApplicationDidEnterBackgroundNotification 这个通知去保存状态。

### 命令模式

通过 Target\_Action 机制和 Invocation 实现命令模式。



