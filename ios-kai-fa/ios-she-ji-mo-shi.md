# iOS 设计模式

### MVC

* Model: 模型
* View：视图
* Controller：控制器

### 单例

单例使用懒加载方式在第一次实例时创建，如`[NSUserDefaults standardUserDefaults]`，`[UIApplication sharedApplication]`, `[UIScreen mainScreen]`,` [NSFileManager defaultManager]`

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

### 门面模式 Facade

### 装饰器模式 Decorator

### 适配器模式 Adapter

### 观察者模式 Observer

### 备忘录模式 Memento

### 命令模式



