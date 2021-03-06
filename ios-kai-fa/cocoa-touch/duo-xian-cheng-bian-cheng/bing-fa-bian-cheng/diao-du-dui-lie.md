# 调度队列（Dispatch Queues）

Grand Central Dispatch（GCD）调度队列是执行任务的强大工具。调度队列让你可以与调用者异步或同步地执行任意代码块。您可以使用调度队列来执行几乎所有用于在单独的线程上执行的任务。调度队列的优点是它们比相应的线程代码更简单，更有效地执行这些任务。

#### 关于调度队列

调度队列是一种在应用程序中异步执行任务的简单方法。任务只是您的应用程序需要执行的一些工作。例如，您可以定义一个任务来执行一些计算，创建或修改数据结构，处理从文件读取的某些数据或任何数量的事物。您可以通过将相应的代码放入函数或块对象中并将其添加到调度队列来定义任务。

调度队列是一个类似于对象的结构，用于管理您向其提交的任务。所有的调度队列都是先进先出的数据结构。因此，添加到队列中的任务始终以与添加队列相同的顺序开始。GCD会自动为您提供一些调度队列，但您可以为特定目的创建其他调度队列。

调度队列的类型：

* Serial（串行）

串行队列（也称为私有调度队列）按照它们被添加到队列中的顺序每次执行一个任务。当前正在执行的任务运行在由调度队列管理的不同线程上（可能因任务而异）。串行队列通常用于同步对特定资源的访问。

您可以根据需要创建尽可能多的串行队列，并且每个队列都可以与所有其他队列同时运行。换句话说，如果你创建了四个串行队列，每个队列一次只执行一个任务，但最多可以同时执行四个任务，每个队列一个。

* Concurrent（并行）

并发队列（也称为全局调度队列）同时执行一个或多个任务，但任务仍按其添加到队列中的顺序启动。当前正在执行的任务在由调度队列管理的不同线程上运行。在任何给定点执行的任务的确切数量是可变的，并取决于系统条件。

在 iOS 5 以后的版本，您可以通过将 DISPATCH\_QUEUE\_CONCURRENT 指定为队列类型来自己创建并发调度队列。另外，还有四个预定义的全局并发队列供您的应用程序使用。

* Main dispatch queue（主调度队列）

主调度队列是一个全局可用的串行队列，用于执行应用程序主线程上的任务。该队列与应用程序的运行循环（如果存在的话）一起工作，以将排队任务的执行与附加到运行循环的其他事件源的执行交错​​。因为它运行在应用程序的主线程上，所以主队列通常用作应用程序的关键同步点。虽然您不需要创建主调度队列，但您确实需要确保您的应用程序正确排除它。

当向应用程序添加并发时，调度队列相对于线程提供了几个优点。最直接的优点是工作队列编程模型的简单性。使用线程，您必须为您要执行的工作以及创建和管理线程本身编写代码。调度队列让您专注于您实际想要执行的工作，而无需担心线程创建和管理。相反，系统会为您处理所有的线程创建和管理。他的优势在于系统能够比任何单个应用程序都更有效地管理线程。系统可以根据可用资源和当前系统条件动态扩展线程数量。另外，系统通常能够比你自己创建线程更快地开始运行您的任务。

尽管您可能认为为调度队列重写代码会很困难，但为编写调度队列编写代码通常比为线程编写代码更容易。编写代码的关键是设计独立并且能够异步运行的任务。但是，调度队列的优势在于可预测性。如果您有两项访问相同共享资源但在不同线程上运行的任务，则任一线程都可以先修改资源，并且您需要使用锁定以确保两个任务不会同时修改该资源。使用分派队列，您可以将两个任务添加到串行分派队列，以确保在任何给定时间只有一个任务修改了资源。这种基于队列的同步比锁更有效，因为在竞争和无争议的情况下，锁总是需要昂贵的内核陷阱，而派遣队列主要在应用程序的进程空间中工作，并且只在绝对必要时调用内核。

虽然你会正确地指出在串行队列中运行的两个任务不会同时运行，您必须记住，如果两个线程同时进行锁定，则线程提供的任何并发都会丢失或显着减少。更重要的是，线程模型需要创建两个线程，它们同时占用内核和用户空间内存。调度队列不会为它们的线程支付相同的内存损失，并且它们使用的线程保持繁忙并且不被阻塞。

有关调度队列的其他一些关键要点包括以下内容：

* 调度队列相对于其他调度队列同时执行其任务。任务序列化仅限于单个调度队列中的任务。
* 系统确定任何时候执行的任务总数。因此，100个不同队列中有100个任务的应用程序可能不会同时执行所有这些任务（除非它具有100个或更多有效内核）。

* 系统在选择要开始的新任务时考虑队列优先级。

* 队列中的任务在添加到队列时必须准备好执行。

* 私有调度队列是引用计数的对象。除了在自己的代码中保留队列之外，请注意，调度源也可以附加到队列中，并增加其保留计数。因此，您必须确保所有调度源都被取消，并且所有保留呼叫均通过适当的释放呼叫进行平衡。

### 与队列相关的技术

使用调度队列的技术：

* 调度组（Dispatch groups）

调度组是一种监视一组块对象以完成的方法。组为依赖于其他任务完成的代码提供有用的同步机制。

* 调度信号量（Dispatch semaphores）

调度信号与传统信号相似，但通常更高效。只有当调用线程需要被阻塞，因为信号量不可用时，调度信号才会调用内核。如果信号量可用，则不进行内核调用。

* 调度源（Dispatch sources）

调度源生成通知以响应特定类型的系统事件。您可以使用调度源来监视事件，例如进程通知，信号和描述符事件等。发生事件时，调度源将您的任务代码异步提交给指定的调度队列进行处理。

### 使用块实现任务

块对象是一种基于C语言的功能，可以在 C，Objective-C 和 C ++ 代码中使用。块可以很容易地定义一个独立的工作单元。虽然它们看起来可能类似于函数指针，但块实际上是由类似于对象的基础数据结构表示的，并且由编译器为您创建和管理。编译器将您提供的代码（以及任何相关数据）打包，并将其封装在可存放在堆中并可在应用程序中传递的表单中。

块的关键优势之一是它们能够使用自己词汇范围之外的变量。当你在一个函数或方法中定义一个块时，这个块在某些方面会起传统的代码块的作用。例如，块可以读取父范围中定义的变量的值。该块访问的变量被复制到堆上的块数据结构中，以便该块稍后可以访问它们。将块添加到分派队列时，这些值通常必须保留为只读格式。但是，同步执行的块也可以使用带有 \_\_block 关键字的变量，以便将数据返回到父级的调用作用域。

使用与用于函数指针的语法类似的语法，将代码块与代码一起声明。块和函数指针之间的主要区别在于块名前面有一个插入符号（^）而不是星号（\*）。就像函数指针一样，您可以将参数传递给块并从中接收返回值。

一个简单的块例子：

```
int x = 123;
int y = 456;

// Block declaration and assignment
void (^aBlock)(int) = ^(int z) {
    printf("%d %d %d\n", x, y, z);
};

// Execute the block
aBlock(789);   // prints: 123 456 789
```

以下是您在设计模块时应考虑的一些主要指导方针的总结：

* 对于您计划使用调度队列异步执行的块，从父函数或方法捕获标量变量并在块中使用它们是安全的。但是，您不应该尝试捕获调用上下文分配和删除的大型结构或其他基于指针的变量。当你的块被执行时，那个指针引用的内存可能会消失。当然，自己分配内存（或对象）是安全的，并明确地将该内存的所有权移交给该块。

* 调度队列复制添加到它们的块，并在完成执行时释放块。换句话说，在将块添加到队列之前，您不需要显式复制块。

* 尽管在执行小任务时队列比原始线程更有效，但在创建块并在队列上执行它们方面仍存在开销。如果一个块的功能太少，那么执行内联比将其派送到队列可能更便宜。判断块是否做得太少的方法是使用性能工具收集每条路径的指标并进行比较。

* 不要缓存相对于底层线程的数据，并期望可以从不同的块访问数据。如果同一队列中的任务需要共享数据，请使用调度队列的上下文指针来存储数据。

* 如果您的块创建了多个 Objective-C 对象，则可能需要将块的部分代码放在 @autorelease 块中以处理这些对象的内存管理。尽管 GCD 调度队列拥有自己的自动释放池，但他们无法保证这些池何时耗尽。如果您的应用程序受内存限制，则创建您自己的自动释放池可以让您以更常规的时间间隔为自动释放对象释放内存。

### 创建并管理调度队列

在将任务添加到队列之前，您必须确定要使用的队列类型以及您打算如何使用它。调度队列可以串行或并行执行任务。另外，如果您有针对队列的特定用途，则可以相应地配置队列属性。

##### 获取全局并发调度队列

当您有多个可以并行运行的任务时，并发调度队列很有用。并发队列仍然是一个队列，它按先进先出的顺序出队;但是，并发队列可能会在任何先前任务完成之前将额外任务出队。并发队列在任何给定时刻执行的实际任务数量都是可变的，并且可以随应用程序中的条件更改而动态更改。许多因素会影响并发队列执行的任务数量，包括可用核心数量，其他进程执行的工作量以及其他串行调度队列中的任务数量和优先级。

系统为每个应用程序提供四个并发调度队列。这些队列对于应用程序来说是全局的，并且仅通过它们的优先级来区分。因为它们是全局的，所以不要明确地创建它们。而是使用 `dispatch_get_global_queue` 函数请求其中一个队列，如以下示例所示：

```
dispatch_queue_t aQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
```

除了获取默认并发队列之外，您还可以通过将`DISPATCH_QUEUE_PRIORITY_HIGH`和`DISPATCH_QUEUE_PRIORITY_LOW`常量传递给函数来获得高优先级和低优先级的队列，或通过传递`DISPATCH_QUEUE_PRIORITY_BACKGROUND`常量来获得后台队列。正如您所预料的那样，高优先级并发队列中的任务在默认和低优先级队列中的任务之前执行。同样，默认队列中的任务在低优先级队列中的任务之前执行。

> 注意：`dispatch_get_global_queue`函数的第二个参数保留给将来扩展。现在，您应该始终将0传递给此参数。

虽然调度队列是引用计数的对象，但您不需要保留和释放全局并发队列。因为它们对应用程序是全局的，所以保留和释放这些队列的调用将被忽略。因此，您不需要存储对这些队列的引用。只要需要对其中一个引用，就可以调用`dispatch_get_global_queue`函数。

##### 创建串行调度队列

当您希望您的任务按特定顺序执行时，串行队列很有用。串行队列一次只执行一个任务，并且始终从队列头部抽取任务。您可以使用串行队列而不是锁来保护共享资源或可变数据结构。与锁不同，串行队列确保任务按可预测的顺序执行。只要您将任务异步提交到串行队列，队列就永远不会死锁。

与为您创建的并发队列不同，您必须明确创建并管理您要使用的任何串行队列。您可以为应用程序创建任意数量的串行队列，但应该避免单独创建大量的串行队列，以便尽可能多地同时执行多个任务。如果您想同时执行大量任务，请将它们提交到全局并发队列之一。创建串行队列时，尝试确定每个队列的用途，例如保护资源或同步应用程序的某些关键行为。

`dispatch_queue_create`函数有两个参数：队列名称和一组队列属性。调试器和性能工具显示队列名称，以帮助您跟踪您的任务如何执行。队列属性保留供将来使用，并应为`NULL`。

创建一个新的串行队列:

```
dispatch_queue_t queue;
queue = dispatch_queue_create("com.example.MyQueue", NULL);
```

除了您创建的任何自定义队列之外，系统还会自动创建一个串行队列并将其绑定到应用程序的主线程。

##### 运行时获取通用队列

Grand Central Dispatch 提供的功能允许您从应用程序访问几个常见的调度队列：

* 使用`dispatch_get_current_queue`函数进行调试或测试当前队列的标识。从块对象中调用此函数将返回该块已提交到的队列（并且现在假定它正在运行）。从块外部调用此函数会返回应用程序的默认并发队列。
* 使用`dispatch_get_main_queue`函数获取与您的应用程序主线程相关联的串行调度队列。此队列为 Cocoa 应用程序以及调用`dispatch_main`函数或在主线程上配置运行循环（使用`CFRunLoopRef`类型或`NSRunLoop`对象）的应用程序自动创建。

* 使用`dispatch_get_global_queue`函数获取任何共享并发队列。

##### 调度队列内存管理

调度队列和其他调度对象是引用计数的数据类型。创建串行调度队列时，它的初始引用计数为1。您可以使用`dispatch_retain`和`dispatch_release`函数根据需要递增和递减引用计数。当队列的引用计数达到零时，系统异步解除分配（deallocates）队列。  
保留和释放调度对象（如队列）以确保它们在使用时保留在内存中很重要。与Cocoa 对象的内存管理一样，一般规则是，如果您打算使用传递给您的代码的队列，则应在使用该队列之前保留该队列，并在不再需要时释放它。这种基本模式可确保只要您使用队列，队列就会保留在内存中。

> 注意：您不需要保留或释放任何全局分派队列，包括并发调度队列或主调度队列。任何保留或释放队列的尝试都会被忽略。

即使您实施垃圾收集应用程序，您仍然必须保留并释放您的调度队列和其他调度对象。Grand Central Dispatch 不支持用于回收内存的垃圾回收模型。

##### 使用队列存储自定义上下文信息

所有调度对象（包括调度队列）都允许您将自定义上下文数据与对象相关联。要在给定的对象上设置和获取这些数据，可以使用`dispatch_set_context`和`dispatch_get_context`函数。系统不会以任何方式使用您的自定义数据，并且由您在适当的时间分配和取消分配数据。

对于队列，您可以使用上下文数据来存储指向 Objective-C 对象或其他数据结构的指针，以帮助识别队列或其代码的预期用法。您可以使用队列的终结函数在解除分配之前将队列数据从队列中释放（或解除关联）。

##### 为队列提供清理功能

创建串行调度队列后，可以附加终结器函数以在队列解除分配时执行任何自定义清理。调度队列是引用计数对象，您可以使用`dispatch_set_finalizer_f`函数指定当队列的引用计数达到零时执行的函数。您使用此函数来清理与队列关联的上下文数据，并且仅当上下文指针不为`NULL`时才会调用该函数。

下面显示了自定义终结器函数和创建队列并安装该终结器的函数。队列使用终结函数来释放存储在队列上下文指针中的数据。传递给终结函数的上下文指针包含与队列关联的数据对象。

安装队列清理功能：

```
void myFinalizerFunction(void *context)
{
    MyDataContext* theData = (MyDataContext*)context;

    // Clean up the contents of the structure
    myCleanUpDataContextFunction(theData);

    // Now release the structure itself.
    free(theData);
}

dispatch_queue_t createMyQueue()
{
    MyDataContext*  data = (MyDataContext*) malloc(sizeof(MyDataContext));
    myInitializeDataContextFunction(data);

    // Create the queue and set the context data.
    dispatch_queue_t serialQueue = dispatch_queue_create("com.example.CriticalTaskQueue", NULL);
    dispatch_set_context(serialQueue, data);
    dispatch_set_finalizer_f(serialQueue, &myFinalizerFunction);

    return serialQueue;
}
```

### 将任务添加到队列

要执行任务，您必须将其派发到适当的调度队列。您可以同步或异步调度任务，并且可以单独或成组地调度它们。一旦进入队列，由于其约束和现有任务已经在队列中，队列将尽快负责执行您的任务。

##### 将单个任务添加到队列

有两种方法可以将任务添加到队列：异步或同步。如果可能，使用`dispatch_async`和`dispatch_async_f`函数的异步执行优于同步替代。当您将一个块对象或函数添加到队列中时，无法知道该代码何时执行。因此，通过异步添加块或函数，您可以计划代码的执行并继续从调用线程执行其他工作。如果您正在从您的应用程序的主线程安排任务（这可能是为了响应某些用户事件），这一点尤其重要。

尽管您应尽可能异步添加任务，但仍可能有时需要同步添加任务以防止竞争状况或其他同步错误。在这些情况下，您可以使用`dispatch_sync`和`dispatch_sync_f`函数将任务添加到队列中。这些函数会阻止当前的执行线程，直到指定的任务完成执行。

> 重要：您不应该从正在计划传递给该函数的同一队列中执行的任务调用`dispatch_sync`或`dispatch_sync_f`函数。这对保证死锁的串行队列尤其重要，但对于并发队列也应该避免。

以下示例显示如何使用基于块的变体异步与同步地调度任务：

```
dispatch_queue_t myCustomQueue;
myCustomQueue = dispatch_queue_create("com.example.MyCustomQueue", NULL);

dispatch_async(myCustomQueue, ^{
    printf("Do some work here.\n");
});

printf("The first block may or may not have run.\n");

dispatch_sync(myCustomQueue, ^{
    printf("Do some more work here.\n");
});
printf("Both blocks have completed.\n");
```

##### 任务完成时执行完成块

就其性质而言，派发到队列中的任务独立于创建它们的代码运行。但是，当任务完成时，您的应用程序可能仍然需要通知该事实，以便它可以包含结果。使用传统的异步编程，您可以使用回调机制来完成此操作，但对于调度队列，您可以使用完成块。

完成块只是您在原始任务结束时分派给队列的另一段代码。调用代码通常在完成任务时提供完成块作为参数。所有任务代码所要做的就是在指定的队列完成工作时将指定的块或函数提交给指定的队列。

执行任务后执行完成回调:

```
void average_async(int *data, size_t len,
   dispatch_queue_t queue, void (^block)(int))
{
   // Retain the queue provided by the user to make
   // sure it does not disappear before the completion
   // block can be called.
   dispatch_retain(queue);

   // Do the work on the default concurrent queue and then
   // call the user-provided block with the results.
   dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
      int avg = average(data, len);
      dispatch_async(queue, ^{ block(avg);});

      // Release the user-provided queue when done
      dispatch_release(queue);
   });
}
```

##### 并行执行循环迭代

并发调度队列可能会提高性能的一个地方是你有一个循环执行固定迭代次数的地方。例如，假设你有一个for循环，通过每个循环迭代完成一些工作：

```
for (i = 0; i < count; i++) {
   printf("%u\n",i);
}
```

如果在每次迭代期间执行的工作与在所有其他迭代期间执行的工作不同，并且每个后续循环完成的顺序不重要，你可以用调用`dispatch_apply`或`dispatch_apply_f`函数替换循环。这些函数为每个循环迭代提交指定的块或函数一次。当调度到并发队列时，可以同时执行多个循环迭代。

调用`dispatch_apply`或`dispatch_apply_f`时，您可以指定一个串行队列或一个并发队列。传入并发队列允许您同时执行多个循环迭代，并且是使用这些函数的最常用方式。虽然使用串行队列是允许的并且为您的代码执行正确的操作，但使用这样的队列在保留循环方面没有真正的性能优势。

并行执行for循环的迭代：

```
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

dispatch_apply(count, queue, ^(size_t i) {
   printf("%u\n",i);
});
```

##### 在主线程上执行任务

Grand Central Dispatch 提供了一个特殊的调度队列，您可以使用它来执行应用程序主线程上的任务。该队列为所有应用程序自动提供，并由任何在其主线程上设置运行循环（由`CFRunLoopRef`类型或`NSRunLoop`对象管理）的应用程序自动排空\(drained\)。如果您没有创建Cocoa应用程序，也不想显式设置运行循环，则必须调用`dispatch_main`函数来显式排空主要调度队列。您仍然可以将任务添加到队列中，但是如果您不调用此函数，那么这些任务将永远不会执行。

您可以通过调用`dispatch_get_main_queue`函数来获取应用程序主线程的调度队列。添加到该队列的任务在主线程本身上串行执行。因此，您可以将此队列用作同步点，以便在应用程序的其他部分完成工作。

##### 在您的任务中使用 Objective-C 对象

GCD为Cocoa 内存管理技术提供了内置支持，因此您可以在您提交的块中自由使用Objective-C 对象来调度队列。每个调度队列维护自己的自动释放池以确保自动释放的对象在某一时刻被释放;队列不能保证它们何时实际释放这些对象。

如果您的应用程序受内存限制，并且您的块创建了多个自动释放对象，则创建您自己的自动释放池是确保及时释放对象的唯一方法。如果您的块创建了数百个对象，则可能需要定期创建多个自动释放池或耗尽您的池。

### 暂停和恢复队列

您可以通过挂起暂时阻止队列暂时执行块对象。您使用`dispatch_suspend`函数暂停调度队列，并使用`dispatch_resume`函数恢复它。调用`dispatch_suspend`递增队列的暂停引用计数，并调用`dispatch_resume`递减引用计数。当引用计数大于零时，队列保持挂起状态。

> 重要：暂停和恢复调用是异步的，只在执行块之间生效。暂停队列不会导致正在执行的块停止。

### 使用调度信号来调节有限资源的使用

如果提交给调度队列的任务访问某些有限资源，则可能需要使用调度信号来调节同时访问该资源的任务数量。

### 等待排队任务组

调度组是阻塞线程的一种方式，直到一个或多个任务完成执行。您可以在完成所有指定任务之前无法进展的地方使用此行为。例如，在调度几个任务来计算某些数据之后，您可以使用一个组来等待这些任务，然后在完成时处理结果。另一种使用分派组的方法是作为线程连接的替代方法。您可以将相应的任务添加到一个调度组，然后等待整个组，而不是启动多个子线程并加入其中的每个线程。

等待异步任务：

```
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
dispatch_group_t group = dispatch_group_create();

// Add a task to the group
dispatch_group_async(group, queue, ^{
   // Some asynchronous work
});

// Do some other work while the tasks execute.

// When you cannot make any more forward progress,
// wait on the group to block the current thread.
dispatch_group_wait(group, DISPATCH_TIME_FOREVER);

// Release the group when it is no longer needed.
dispatch_release(group);
```

### 调度队列和线程安全

在调度队列中讨论线程安全可能看起来很奇怪，但线程安全仍然是一个相关主题。任何时候在应用程序中实现并发时，都应该知道几件事情：

* 调度队列本身是线程安全的。换句话说，您可以将任务从系统上的任何线程提交到调度队列，而无需先取得锁定或同步对队列的访问。

* 不要从在传递给函数调用的同一队列中执行的任务调用`dispatch_sync`函数。这样做会使队列死锁。如果您需要分派到当前队列，请使用`dispatch_async`函数异步执行。

* 避免从提交给调度队列的任务中获取锁定。虽然使用来自任务的锁定是安全的，但是当您获取锁定时，如果该锁定不可用，则可能会完全阻塞串行队列。同样，对于并​​发队列，等待锁定可能会阻止执行其他任务。如果您需要同步部分代码，请使用串行调度队列而不是锁定。

* 虽然您可以获取有关运行任务的基础线程的信息，但最好避免这样做。



