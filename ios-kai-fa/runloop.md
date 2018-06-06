# RunLoop

### 简介

简单的说 run loop 是事件驱动的一个循环，如下代码所示

```c
int main(int argc, char * argv[]) {
    // 程序一直运行状态
    while (AppIsRunning) {
        // 睡眠状态，等等唤醒事件
        id whoWakesMe = SleepForWakingUp();
        // 得到唤醒事件
        id event = GetEvent(whoWakesMe);
        // 开始处理事件
        HandleEvent(event);
    }
    return 0;
}
```

### Cocoa 会涉及到 RunLoop 的

* 系统级：GCD，mach kernel, block, pthread
* 应用层：NSTimer, UIEvent, Autorelease, NSObject\(NSDelayedPerforming\), NSObject\(NSThreadPerformAddition\), CADisplayLink, CATransition, CAAnimation, dispatch\_get\_main\_queue\(\)（GCD中dispatch到main queue的block会被dispatch到main RunLoop执行），NSPort, NSURLConnection, AFNetworking\(这个第三方网络请求框架使用在开启新线程中添加自己的run loop监听事件\)

### 在 Main thread 堆栈中所处位置

堆栈最底层是start\(dyld\)，往上依次是main，UIApplication\(main.m\) -&gt; GSEventRunModal\(Graphic Services\) -&gt;

RunLoop\(包含CFRunLoopRunSpecific，\_\_CFRunLoopRun，\_\_CFRunLoopDoSouces0，**CFRUNLOOP\_IS\_CALLING\_OUT\_TO\_A\_SOURCE0\_PERFORM\_FUNCTION**\) -&gt; Handle Touch Event

### RunLoop 原理

CFRunLoop开源代码：

[http://opensource.apple.com/source/CF/CF-855.1/](http://opensource.apple.com/source/CF/CF-855.17/)

#### 执行顺序的伪代码

```
SetupThisRunLoopRunTimeoutTimer(); // by GCD timer
do {
     __CFRunLoopDoObservers(kCFRunLoopBeforeTimers);
     __CFRunLoopDoObservers(kCFRunLoopBeforeSources);

     __CFRunLoopDoBlocks();
     __CFRunLoopDoSource0();

     CheckIfExistMessagesInMainDispatchQueue(); // GCD

     __CFRunLoopDoObservers(kCFRunLoopBeforeWaiting);
     var wakeUpPort = SleepAndWaitForWakingUpPorts();
     // mach_msg_trap
     // Zzz...
     // Received mach_msg, wake up
     __CFRunLoopDoObservers(kCFRunLoopAfterWaiting);
     // Handle msgs
     if (wakeUpPort == timerPort) {
          __CFRunLoopDoTimers();
     } else if (wakeUpPort == mainDispatchQueuePort) {
          // GCD
          __CFRUNLOOP_IS_SERVICING_THE_MAIN_DISPATCH_QUEUE__()
     } else {
          __CFRunLoopDoSource1();
     }
     __CFRunLoopDoBlocks();
} while (!stop && !timeout);
```

#### 构成

Thread包含一个CFRunLoop，一个CFRunLoop包含一种CFRunLoopMode，mode包含CFRunLoopSource，CFRunLoopTimer和CFRunLoopObserver。

##### CFRunLoopMode

RunLoop只能运行在一种mode下，如果要换mode当前的loop也需要停下重启成新的。利用这个机制，ScrollView过程中NSDefaultRunLoopMode的mode会切换UITrackingRunLoopMode来保证ScrollView的流畅滑动不受只能在NSDefaultRunLoopMode时处理的事件影响滑动。同时mode还是可定制的。

* NSDefaultRunLoopMode：默认，空闲状态
* UITrackingRunLoopMode：ScrollView滑动时
* UIInitializationRunLoopMode：启动时
* NSRunLoopCommonModes：Mode集合 Timer计时会被scrollView的滑动影响的问题可以通过将timer添加到NSRunLoopCommonModes来解决

```c
//将timer添加到NSDefaultRunLoopMode中
[NSTimer scheduledTimerWithTimeInterval:1.0
     target:self
     selector:@selector(timerTick:)
     userInfo:nil
     repeats:YES];

//然后再添加到NSRunLoopCommonModes里
NSTimer *timer = [NSTimer timerWithTimeInterval:1.0
     target:self
     selector:@selector(timerTick:)
     userInfo:nil
     repeats:YES];
[[NSRunLoop currentRunLoop] addTimer:timer forMode:NSRunLoopCommonModes];
```

##### CFRunLoopTimer

NSTimer是对RunLoopTimer的封装

```c
+ (NSTimer *)timerWithTimeInterval:(NSTimeInterval)ti invocation:(NSInvocation *)invocation repeats:(BOOL)yesOrNo;
+ (NSTimer *)scheduledTimerWithTimeInterval:(NSTimeInterval)ti invocation:(NSInvocation *)invocation repeats:(BOOL)yesOrNo;

- (void)performSelector:(SEL)aSelector withObject:(id)anArgument afterDelay:(NSTimeInterval)delay inModes:(NSArray *)modes;

+ (CADisplayLink *)displayLinkWithTarget:(id)target selector:(SEL)sel;
- (void)addToRunLoop:(NSRunLoop *)runloop forMode:(NSString *)mode;
```

##### CFRunLoopSource

* source0：处理如UIEvent，CFSocket这样的事件
* source1：Mach port驱动，CFMachport，CFMessagePort

##### CFRunLoopObserver

Cocoa框架中很多机制比如CAAnimation等都是由RunLoopObserver触发的。observer到当前状态的变化进行通知。

```c
typedef CF_OPTIONS(CFOptionFlags, CFRunLoopActivity) {
     kCFRunLoopEntry = (1UL << 0),
     kCFRunLoopBeforeTimers = (1UL << 1),
     kCFRunLoopBeforeSources = (1UL << 2),
     kCFRunLoopBeforeWaiting = (1UL << 5),
     kCFRunLoopAfterWaiting = (1UL << 6),
     kCFRunLoopExit = (1UL << 7),
     kCFRunLoopAllActivities = 0x0FFFFFFFU
};
```

### 使用 RunLoop 的案例

#### AFNetworking

使用NSOperation+NSURLConnection并发模型都会面临NSURLConnection下载完成前线程退出导致NSOperation对象接收不到回调的问题。AFNetWorking解决这个问题的方法是按照官方的guid

[https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection\_Class/Reference/Reference.html\#//apple\_ref/occ/instm/NSURLConnection/initWithRequest:delegate:startImmediately:](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/Reference/Reference.html#//apple_ref/occ/instm/NSURLConnection/initWithRequest:delegate:startImmediately:)

上写的NSURLConnection的delegate方法需要在connection发起的线程runloop中调用，于是AFNetWorking直接借鉴了Apple自己的一个Demo

[https://developer.apple.com/LIBRARY/IOS/samplecode/MVCNetworking/Introduction/Intro.html](https://developer.apple.com/LIBRARY/IOS/samplecode/MVCNetworking/Introduction/Intro.html)

的实现方法单独起一个global thread，内置一个runloop，所有的connection都由这个runloop发起，回调也是它接收，不占用主线程，也不耗CPU资源。

```c
+ (void)networkRequestThreadEntryPoint:(id)__unused object {
     @autoreleasepool {
          [[NSThread currentThread] setName:@"AFNetworking"];

          NSRunLoop *runLoop = [NSRunLoop currentRunLoop];
          [runLoop addPort:[NSMachPort port] forMode:NSDefaultRunLoopMode];
          [runLoop run];
     }
}

+ (NSThread *)networkRequestThread {
     static NSThread *_networkRequestThread = nil;
     static dispatch_once_t oncePredicate;
     dispatch_once(&oncePredicate, ^{
          _networkRequestThread =
          [[NSThread alloc] initWithTarget:self
               selector:@selector(networkRequestThreadEntryPoint:)
               object:nil];
          [_networkRequestThread start];
     });

     return _networkRequestThread;
}
```

类似的可以用这个方法创建一个常驻服务的线程。

#### TableView中实现平滑滚动延迟加载图片

利用CFRunLoopMode的特性，可以将图片的加载放到NSDefaultRunLoopMode的mode里，这样在滚动UITrackingRunLoopMode这个mode时不会被加载而影响到。

```c
UIImage *downloadedImage = ...;
[self.avatarImageView performSelector:@selector(setImage:)
     withObject:downloadedImage
     afterDelay:0
     inModes:@[NSDefaultRunLoopMode]];
```

#### 接到程序崩溃时的信号进行自主处理例如弹出提示等

```c
CFRunLoopRef runLoop = CFRunLoopGetCurrent();
NSArray *allModes = CFBridgingRelease(CFRunLoopCopyAllModes(runLoop));
while (1) {
     for (NSString *mode in allModes) {
          CFRunLoopRunInMode((CFStringRef)mode, 0.001, false);
     }
}
```

#### 异步测试

```c
- (BOOL)runUntilBlock:(BOOL(^)())block timeout:(NSTimeInterval)timeout
{
     __block Boolean fulfilled = NO;
     void (^beforeWaiting) (CFRunLoopObserverRef observer, CFRunLoopActivity activity) =
     ^(CFRunLoopObserverRef observer, CFRunLoopActivity activity) {
          fulfilled = block();
          if (fulfilled) {
               CFRunLoopStop(CFRunLoopGetCurrent());
          }
     };

     CFRunLoopObserverRef observer = CFRunLoopObserverCreateWithHandler(NULL, kCFRunLoopBeforeWaiting, true, 0, beforeWaiting);
     CFRunLoopAddObserver(CFRunLoopGetCurrent(), observer, kCFRunLoopDefaultMode);

     // Run!
     CFRunLoopRunInMode(kCFRunLoopDefaultMode, timeout, false);

     CFRunLoopRemoveObserver(CFRunLoopGetCurrent(), observer, kCFRunLoopDefaultMode);
     CFRelease(observer);

     return fulfilled;
}
```



