# 线程管理

OS X 或 iOS 中的每个进程（应用程序）都由一个或多个线程组成，每个线程表示通过应用程序代码执行的单个路径。每个应用程序都以单个线程开始，该线程运行应用程序的`main`功能。应用程序可以产生额外的线程，每个线程执行特定功能的代码。

当一个应用程序产生一个新的线程时，该线程将成为应用程序进程空间内的一个独立的实体。每个线程都有自己的执行堆栈，并由内核独立调度运行时间。一个线程可以与其他线程和其他进程通信，执行I / O操作，并执行任何其他你可能需要的操作。但是，由于它们在同一个进程空间内，因此单个应用程序中的所有线程共享相同的虚拟内存空间，并具有与进程本身相同的访问权限。

### 线程开销

在内存使用和性能方面，线程对您的程序（和系统）有实际的成本。每个线程都需要在内核内存空间和程序的内存空间中分配内存。管理线程和协调其调度所需的核心结构使用有线存储器存储在内核中。您的线程的堆栈空间和每线程数据存储在程序的内存空间中。当您首次创建线程时，大多数这些结构都会创建并初始化 - 由于与内核进行必需的交互，该进程可能相对较为昂贵。

线程创建开销：

* 内核数据结构（Kernel data structures）

大约1 KB

该内存用于存储线程数据结构和属性，其中大部分分配为有线内存，因此无法分页到磁盘。

* 堆栈空间（Stack space）

512 KB（辅助线程） 8 MB（OS X主线程） 1 MB（iOS主线程）

辅助线程允许的最小堆栈大小为16 KB，堆栈大小必须是4 KB的倍数。这个内存的空间在创建线程的时候被放置在进程空间中，但是与该内存相关的实际页面只有在需要时才会被创建。

* 创建时间（Creation time）

大约90微秒

该值反映了创建线程的初始调用与线程入口点例程开始执行的时间之间的时间。

编写线程代码时需要考虑的另一个成本是生产成本。设计线程应用程序有时可能需要对组织应用程序数据结构的方式进行根本性更改。为避免使用同步，进行这些更改可能是必要的，这可能会对设计不当的应用程序造成巨大的性能损失。设计这些数据结构并调试线程代码中的问题可能会增加开发线程应用程序所需的时间。但是，避免这些成本会在运行时造成更大的问题，但是，如果您的线程花费太多时间等待锁定或什么都不做。

### 创建一个线程

创建低级线程相对简单。在任何情况下，你都必须有一个函数或方法来充当线程的主入口点，并且你必须使用其中一个可用的线程例程来启动线程。以下部分显示了更常用的线程技术的基本创建过程。

##### 使用 NSThread

有两种使用`NSThread`类创建线程的方法：

* 使用`detachNewThreadSelector：toTarget：withObject：`class方法来产生新的线程。

* 创建一个新的`NSThread`对象并调用其`start`方法。

这两种技术都会在应用程序中创建一个分离线程。分离线程意味着线程退出时线程的资源会被系统自动回收。这也意味着你的代码不需要以后明确加入到线程中。

因为`detachNewThreadSelector：toTarget：withObject：`方法在所有版本的 OS X 中都受支持，所以在现有的使用线程的 Cocoa 应用程序中经常会发现它。要分离新线程，只需提供您想要用作线程入口点的方法名称（指定为选择器），定义该方法的对象以及要在启动时传递给线程的任何数据。以下示例显示了此方法的基本调用，该方法使用当前对象的自定义方法生成线程。

```
[NSThread detachNewThreadSelector:@selector(myThreadMainMethod:) toTarget:self withObject:nil];
```

在OS X v10.5之前，您主要使用`NSThread`类来产生线程。虽然你可以得到一个`NSThread`对象并访问一些线程属性，但你只能在线程本身运行后才能这样做。在OS X v10.5中，添加了用于创建`NSThread`对象而不立即产生相应新线程的支持。这种支持使得可以在启动线程之前获取和设置各种线程属性。它还使得可以使用该线程对象稍后引用正在运行的线程。

在OS X v10.5及更高版本中初始化`NSThread`对象的简单方法是使用`initWithTarget：selector：object：`方法。此方法使用与`detachNewThreadSelector：toTarget：withObject：`方法完全相同的信息，并使用它来初始化新的`NSThread`实例。但是，它不启动线程。要启动线程，请明确调用线程对象的`start`方法，如下例所示：

```
NSThread* myThread = [[NSThread alloc] initWithTarget:self
                                        selector:@selector(myThreadMainMethod:)
                                        object:nil];
[myThread start];  // Actually create the thread
```

> 注意：使用`initWithTarget：selector：object：`方法的另一种方法是对`NSThread`进行子类化并覆盖其`main`方法。

如果您有一个线程当前正在运行的`NSThread`对象，则可以将消息发送到该线程的一种方式是使用应用程序中几乎任何对象的`performSelector：onThread：withObject：waitUntilDone：`方法。在OS X v10.5中引入了对线程（主线程除外）执行选择器的支持，并且是在线程之间进行通信的便捷方式。使用此技术发送的消息由其他线程直接执行，作为其正常运行循环处理的一部分。以这种方式进行通信时，您仍然可能需要某种形式的同步，但比在线程之间设置通信端口更简单。

##### 使用 POSIX（可移植操作系统接口） 线程

OS X 和 iOS 为使用 POSIX 线程 API 创建线程提供基于C的支持。该技术实际上可以用于任何类型的应用程序（包括Cocoa和Cocoa Touch应用程序），如果您正在为多个平台编写软件，该技术可能会更方便。你使`pthread_create`来创建 POSIX 线程。

下面显示了两个使用 POSIX 调用创建线程的自定义函数。`LaunchThread`函数创建一个新的线程，其主例程在`PosixThreadMainRoutine`函数中实现。由于 POSIX 默认将线程创建为可连接，因此此示例更改线程的属性以创建分离的线程。将线程标记为已分离，可让系统在退出时立即为该线程回收资源。

用C中创建一个线程：

```
#include <assert.h>
#include <pthread.h>

void* PosixThreadMainRoutine(void* data)
{
    // Do some work here.

    return NULL;
}

void LaunchThread()
{
    // Create the thread using POSIX routines.
    pthread_attr_t  attr;
    pthread_t       posixThreadID;
    int             returnVal;

    returnVal = pthread_attr_init(&attr);
    assert(!returnVal);
    returnVal = pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);
    assert(!returnVal);

    int     threadError = pthread_create(&posixThreadID, &attr, &PosixThreadMainRoutine, NULL);

    returnVal = pthread_attr_destroy(&attr);
    assert(!returnVal);
    if (threadError != 0)
    {
         // Report an error.
    }
}
```

如果将前面列表中的代码添加到其中一个源文件并调用`LaunchThread`函数，它将在您的应用程序中创建一个新的分离线程。

##### 使用 NSObject 来产生一个线程

`performSelectorInBackground：withObject：`方法创建一个新的分离线程，并使用指定的方法作为新线程的入口点。例如，如果您有一些对象（由变量myObj表示），并且该对象有一个名为`doS​​omething`的方法，您想在后台线程中运行该方法，则可以使用以下代码执行此操作：

```
[myObj performSelectorInBackground:@selector(doSomething) withObject:nil];
```

调用此方法的效果与将当前对象，选择器和参数对象作为参数调用`NSThread`的`detachNewThreadSelector：toTarget：withObject：`方法的效果相同。

##### 在 Cocoa 应用程序中使用 POSIX 线程

尽管`NSThread`类是在 Cocoa 应用程序中创建线程的主要接口，但如果您更方便使用 POSIX 线程，则可以自由使用 POSIX 线程。例如，如果您已经有使用它们的代码并且您不想重写它，则可以使用 POSIX 线程。如果你打算在 Cocoa 应用程序中使用 POSIX 线程，你仍然应该了解 Cocoa 和线程之间的交互，并遵循以下部分的指导原则。

###### 保护 Cocoa 框架

###### 混合 POSIX 和 Cocoa 锁

### 配置线程属性

在创建线程之后（有时在之前），您可能需要配置线程环境的不同部分。

##### 配置线程的堆栈大小

设置线程的堆栈大小：

* Cocoa

在iOS和OS X v10.5及更高版本中，分配并初始化`NSThread`对象（不要使用`detachNewThreadSelector：toTarget：withObject`：方法）。在调用线程对象的`start`方法之前，请使用`setStackSize`：方法来指定新的堆栈大小。

* POSIX

创建一个新的`pthread_attr_t`结构并使用`pthread_attr_setstacksize`函数更改默认堆栈大小。创建线程时，将属性传递给`pthread_create`函数。

* Multiprocessing Services

创建线程时，将相应的堆栈大小值传递给`MPCreateTask`函数。

##### 配置线程本地存储

##### 设置线程的分离状态

##### 设置线程优先级

### 编写你的线程入口程序

大多数情况下，线程入口点例程的结构在 OS X 中与其他平台上的结构相同。你初始化你的数据结构，做一些工作或者可选地设置一个运行循环，并在你的线程代码完成时清理。根据您的设计，在编写入口例程时可能需要执行一些额外的步骤。

##### 创建一个自动释放池

在 Objective-C 框架中链接的应用程序通常必须在其每个线程中至少创建一个自动释放池。如果应用程序使用托管模型（应用程序处理保留和释放对象的位置），自动释放池将捕获该线程中自动释放的所有对象。

如果应用程序使用垃圾回收而不是托管内存模型，则不需要创建 autorelease 池。

如果您的应用程序使用托管内存模型，那么创建自动释放池应该是您在线程入口例程中首先执行的操作。同样，销毁这个自动释放池应该是你在线程中做的最后一件事。该池确保自动释放的对象被捕获，尽管在线程本身退出之前它不会释放它们。

定义你的线程入口点例程：

```
- (void)myThreadMainRoutine
{
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init]; // Top-level pool

    // Do thread work here.

    [pool release];  // Release the objects in the pool.
}
```

由于顶级自动释放池在线程退出前不会释放其对象，因此长期线程应创建更多的自动释放池来更频繁地释放对象。

##### 设置一个异常处理程序

如果您的应用程序捕获并处理异常，则应准备好您的线程代码以捕获可能发生的任何异常。尽管最好在它们可能发生的地方处理异常，但如果未能在线程中捕获抛出的异常，则会导致应用程序退出。在线程入口例程中安装最终的 try / catch 可以让您捕获任何未知的异常并提供适当的响应。

##### 设置运行循环

在编写代码时，您希望在单独的线程上运行，您有两种选择。第一种选择是将线程的代码编写成一个很少或根本不中断的长任务，并在线程完成时退出线程。第二个选择是把你的线程放到一个循环中，让它在到达时动态地处理请求。第一个选项不需要为您的代码进行特殊设置;你只是开始做你想做的工作。然而，第二个选项涉及设置线程的运行循环。

OS X 和 iOS 为在每个线程中实现运行循环提供了内置支持。应用程序框架自动启动应用程序主线程的运行循环。如果您创建任何辅助线程，则必须配置运行循环并手动启动它。

### 终止线程

建议退出线程的方式是让它正常退出入口点例程。尽管 Cocoa，POSIX 和Multiprocessing Services 提供了直接杀死线程的例程，但是强烈建议不要使用这样的例程。杀死一个线程阻止线程自行清理。由该线程分配的内存可能会泄漏，并且线程当前正在使用的任何其他资源可能无法正确清理，从而在稍后创建潜在问题。

在长时间工作中检查退出条件：

```
- (void)threadMainRoutine
{
    BOOL moreWorkToDo = YES;
    BOOL exitNow = NO;
    NSRunLoop* runLoop = [NSRunLoop currentRunLoop];

    // Add the exitNow BOOL to the thread dictionary.
    NSMutableDictionary* threadDict = [[NSThread currentThread] threadDictionary];
    [threadDict setValue:[NSNumber numberWithBool:exitNow] forKey:@"ThreadShouldExitNow"];

    // Install an input source.
    [self myInstallCustomInputSource];

    while (moreWorkToDo && !exitNow)
    {
        // Do one chunk of a larger body of work here.
        // Change the value of the moreWorkToDo Boolean when done.

        // Run the run loop but timeout immediately if the input source isn't waiting to fire.
        [runLoop runUntilDate:[NSDate date]];

        // Check to see if an input source handler changed the exitNow value.
        exitNow = [[threadDict valueForKey:@"ThreadShouldExitNow"] boolValue];
    }
}
```



