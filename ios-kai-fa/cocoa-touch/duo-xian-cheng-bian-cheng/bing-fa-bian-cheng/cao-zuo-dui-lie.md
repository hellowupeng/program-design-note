# 操作队列

Cocoa 操作（operation）是一种面向对象的方式来封装你想异步执行的工作。操作旨在与操作队列一起使用，或者单独使用。

### 关于操作对象

操作对象是 NSOperation 类（在 Foundation 框架中）的一个实例，用于封装希望应用程序执行的工作。NSOperation 类本身是一个抽象基类，为了做任何有用的工作，它必须被分类。尽管是抽象的，但这个类也提供了大量的基础设施，以尽量减少您在自己的子类中所要完成的工作量。另外，Foundation 框架提供了两个具体的子类，你可以在现有代码里直接使用。

* [NSInvocationOperation](https://developer.apple.com/documentation/foundation/nsinvocationoperation)

基于应用程序中的对象和选择器创建操作对象的类。如果你有一个已经执行所需任务的现有方法，则可以使用此类。因为它不需要子类化，所以还可以使用此类以更动态的方式创建操作对象。

* [NSBlockOperation](https://developer.apple.com/documentation/foundation/nsblockoperation)

能同时执行一个或多个块对象的类。因为它可以执行多个块，所以块操作对象使用组语义进行操作;只有当所有相关的块已经完成执行时，操作本身才算完成。

* [NSOperation](https://developer.apple.com/documentation/foundation/nsoperation)

用于定义自定义操作对象的基类。通过NSOperation的子类化，您可以完全控制自己操作的实现，包括更改操作执行的默认方式并报告其状态的功能。

所有操作对象都支持以下主要功能：

* 支持在操作对象之间建立基于图的依赖关系。这些依赖关系会阻止给定的操作运行，直到它所依赖的所有操作都已完成运行。

* 支持可选的完成块，该块在操作的主任务完成后执行。

* 支持使用 KVO 通知监视对操作执行状态的更改。

* 支持对操作进行优先级排序，从而影响其相对执行顺序。

* 支持取消允许你在执行时暂停操作的语义。

### 并发与非并发操作

虽然通常通过将操作添加到操作队列来执行操作，但这不是必需的。也可以通过调用它的 start 方法手动执行一个操作对象，但这样做并不能保证该操作与其他代码同时运行。NSOperation 类的 isConcurrent 方法告诉您一个操作是相对于调用 start 方法的线程同步或异步运行的。默认情况下，此方法返回 NO，这意味着该操作在调用线程中同步运行。

如果你想实现一个并发操作 - 也就是说，相对于调用线程异步运行的操作 - 你必须编写额外的代码来异步启动操作。例如，您可能会产生一个单独的线程，调用异步系统函数或执行其他任何操作来确保 start 方法启动任务并立即返回，并且很可能在任务完成之前返回。

大多数开发人员不应该需要实现并发操作对象。

### 创建一个 NSInvocationOperation 对象

NSInvocationOperation 类是 NSOperation 的具体子类，它在运行时会调用您在指定的对象上指定的选择器。避免为应用程序中的每个任务定义大量自定义操作对象;特别是如果您要修改现有的应用程序并且已经拥有执行必要任务所需的对象和方法。例如，您可以使用调用操作来执行基于用户输入动态选择的选择器。

创建调用操作的过程非常简单。你可以创建并初始化类的新实例，将所需的对象和选择器传递给初始化方法。

创建一个 NSInvocationOperation 对象：

```
@implementation MyCustomClass
- (NSOperation*)taskWithData:(id)data {
    NSInvocationOperation* theOp = [[NSInvocationOperation alloc] initWithTarget:self
                    selector:@selector(myTaskMethod:) object:data];

   return theOp;
}

// This is the method that does the actual work of the task.
- (void)myTaskMethod:(id)data {
    // Perform the task.
}
@end
```

### 创建一个 NSBlockOperation 对象

NSBlockOperation 类是 NSOperation 的具体子类，充当一个或多个块对象的包装。此类为已经使用操作队列并且不想创建调度队列的应用程序提供面向对象的包装器。您还可以使用块操作来利用操作依赖关系，KVO通知以及可能不适用于调度队列的其他功能。

在创建块操作时，通常在初始化时至少添加一个块;您可以稍后根据需要添加更多块。当需要执行 NSBlockOperation 对象时，对象将其所有块提交给默认优先级并发调度队列。该对象然后等待，直到所有块完成执行。当最后一个块完成执行时，操作对象将自己标记为已完成。因此，您可以使用块操作来跟踪一组正在执行的块，就像使用线程连接合并多个线程的结果一样。区别在于，因为块操作本身在单独的线程上运行，所以应用程序的其他线程可以在等待块操作完成的同时继续工作。

创建一个 NSBlockOperation 对象：

```
NSBlockOperation* theOp = [NSBlockOperation blockOperationWithBlock: ^{
      NSLog(@"Beginning operation.\n");
      // Do some work.
   }];
```

创建块操作对象后，可以使用 addExecutionBlock：方法向其添加更多块。如果需要连续执行块，则必须将它们直接提交到所需的调度队列。

### 定义一个自定义操作（Operation）对象

如果块操作和调用操作对象不能完全满足应用程序的需求，则可以直接子类化 NSOperation 并添加所需的任何行为。NSOperation 类为所有操作对象提供了一个通用的子类化点。该类还提供了大量的基础设施来处理依赖关系和 KVO 通知所需的大部分工作。

定义非并发操作比定义并发操作简单得多。对于非并行操作，您只需执行主要任务并对取消事件做出适当回应;现有的类基础设施为您完成所有其他工作。对于并发操作，您必须用您的自定义代码替换一些现有的基础架构。

##### 执行主要（Main）任务

每个操作对象至少应该至少实现以下方法：

* 自定义初始化方法

* main

您需要一个自定义的初始化方法，将您的操作对象置于已知状态和自定义主要方法来执行您的任务。当然，您可以根据需要实施其他方法，如下所示：

* 您计划从主方法的实现中调用的自定义方法

* 用于设置数据值和访问操作结果的访问器方法

* NSCoding 协议的方法允许您存档和取消存档操作对象

定义一个简单的操作对象：

```
@interface MyNonConcurrentOperation : NSOperation
@property id (strong) myData;
-(id)initWithData:(id)data;
@end

@implementation MyNonConcurrentOperation
- (id)initWithData:(id)data {
   if (self = [super init])
      myData = data;
   return self;
}

-(void)main {
   @try {
      // Do some work on myData and report the results.
   }
   @catch(...) {
      // Do not rethrow exceptions.
   }
}
@end
```

##### 响应取消事件

在一个操作开始执行之后，它会继续执行它的任务，直到它完成或直到你的代码明确地取消操作。即使在操作开始执行之前，取消也可能随时发生。尽管NSOperation 类为客户提供取消操作的方法，但认识到取消事件是必要的自愿行为。如果一项行动彻底终止，可能无法收回已分配的资源。因此，操作对象需要检查取消事件，并在操作过程中正常退出。

为了支持操作对象中的取消操作，您只需定期从自定义代码中调用对象的isCancelled 方法，并在返回 YES 时立即返回。无论您的操作持续时间如何，或者您是直接子类化 NSOperation 还是使用其中一个具体的子类，支持取消都很重要。isCancelled 方法本身非常轻便，可以频繁调用，不会有任何显着的性能损失。在设计操作对象时，应考虑在代码的以下位置调用 isCancelled 方法：

* 在您执行任何实际工作之前

* 在循环的每次迭代中至少一次，或者如果每次迭代相对较长，则更频繁

* 在你的代码中的任何地方，相对容易中止操作

回应取消请求：

```
- (void)main {
   @try {
      BOOL isDone = NO;

      while (![self isCancelled] && !isDone) {
          // Do some work and set isDone to YES when finished
      }
   }
   @catch(...) {
      // Do not rethrow exceptions.
   }
}
```

虽然前面的例子不包含清理代码，但您自己的代码应该确保释放由您的自定义代码分配的任何资源。

##### 为并发执行配置操作

##### 维护 KVO 合规性

NSOperation 类是对以下关键路径的键值观察（KVO）兼容的：

* [`isCancelled`](https://developer.apple.com/documentation/foundation/nsoperation/1408418-cancelled)

* [`isConcurrent`](https://developer.apple.com/documentation/foundation/operation/1411089-isconcurrent)

* [`isExecuting`](https://developer.apple.com/documentation/foundation/operation/1415621-isexecuting)

* [`isFinished`](https://developer.apple.com/documentation/foundation/nsoperation/1413540-finished)

* [`isReady`](https://developer.apple.com/documentation/foundation/operation/1412992-isready)

* [`dependencies`](https://developer.apple.com/documentation/foundation/operation/1416668-dependencies)

* [`queuePriority`](https://developer.apple.com/documentation/foundation/operation/1411204-queuepriority)

* [`completionBlock`](https://developer.apple.com/documentation/foundation/nsoperation/1408085-completionblock)

### 定制操作对象的执行行为

操作对象的配置在创建它们之后但在将它们添加到队列之前发生。

##### 配置互操作相关性（Configuring Interoperation Dependencies）

依赖关系是一种序列化不同操作对象执行的方式。依赖于其他操作的操作无法开始执行，直到它所依赖的所有操作都已完成执行。因此，您可以使用依赖关系在两个操作对象之间创建简单的一对一依赖关系或构建复杂的对象依赖关系图。

要建立两个操作对象之间的依赖关系，可以使用 NSOperation 的 addDependency：方法。此方法创建从当前操作对象到您作为参数指定的目标操作的单向依赖关系。这种依赖意味着当前对象不能开始执行，直到目标对象完成执行。依赖关系也不限于同一队列中的操作。操作对象管理它们自己的依赖关系，因此在操作之间创建依赖关系并将它们全部添加到不同的队列是完全可以接受的。然而，有一件不可接受的事情是在操作之间创建循环依赖关系。这样做是程序员错误，会阻止受影响的操作运行。

当一个操作的所有依赖都已经完成时，操作对象通常会准备好执行。如果操作对象在队列中，则队列可以随时开始执行该操作。如果您计划手动执行操作，则由您来调用操作的启动方法。

> 重要：在运行操作或将它们添加到操作队列之前，应始终配置依赖关系。之后添加的依赖项可能无法阻止给定的操作对象运行。

只要对象的状态发生变化，依赖关系就会依赖每个操作对象发送适当的 KVO 通知。如果您自定义操作对象的行为，则可能需要从自定义代码生成适当的KVO通知，以避免导致依赖关系出现问题。

##### 更改操作的执行优先级

对于添加到队列中的操作，执行顺序首先由排队操作准备情况决定，然后由相对优先级决定。准备就绪取决于操作对其他操作的依赖性，但优先级是操作对象本身的属性。默认情况下，所有新的操作对象都具有“正常”优先级，但可以通过调用对象的 setQueuePriority：方法来根据需要增加或减少该优先级。

优先级仅适用于同一操作队列中的操作。如果您的应用程序有多个操作队列，则每个操作队列都独立于其他队列的优先级。因此，低优先级操作仍然可以在不同队列中的高优先级操作之前执行。

优先级不能取代依赖关系。优先级决定操作队列开始执行的顺序，只有那些当前就绪的操作。

##### 更改底层线程优先级

在OS X v10.6及更高版本中，可以配置操作基础线程的执行优先级。系统中的线程策略本身由内核管理，但通常优先级较高的线程比较低优先级的线程有更多的运行机会。

要设置操作的线程优先级，必须先将操作对象的setThreadPriority：方法添加到队列中（或手动执行），然后调用它。

##### 设置完成块

在OS X v10.6及更高版本中，当主任务完成执行时，操作可以执行完成块。您可以使用完成块来执行任何您不认为是主要任务的工作。例如，您可以使用此块通知感兴趣的客户端操作本身已完成。并发操作对象可能会使用此块来生成其最终的KVO通知。

要设置完成块，请使用NSOperation的setCompletionBlock：方法。您传递给此方法的块应该没有参数并且没有返回值。

### 实现操作对象的技巧

##### 管理操作对象中的内存

###### Avoid Per-Thread Storage

###### 根据需要保持对操作对象的引用

##### 处理错误和异常

### 确定操作对象的适用范围

### 执行操作

##### 将操作添加到操作队列

到目前为止，执行操作的最简单方法是使用操作队列，该操作队列是 NSOperationQueue 类的一个实例。你的应用程序负责创建和维护它打算使用的任何操作队列。应用程序可以有任意数量的队列，但是在给定的时间点可以执行的操作数有实际的限制。操作队列与系统一起工作，将并发操作数限制为适合可用内核和系统负载的值。因此，创建额外的队列并不意味着您可以执行其他操作。

要创建一个队列，您可以像在任何其他对象中一样在应用程序中分配它：

```
NSOperationQueue* aQueue = [[NSOperationQueue alloc] init];
```

要将操作添加到队列中，请使用 `addOperation：`方法。在 OS X v10.6 及更高版本中，您可以使用 `addOperations：waitUntilFinished：`方法添加操作组，也可以使用 `addOperationWithBlock：`方法将块对象直接添加到队列（没有相应的操作对象）。这些方法中的每一个都将一个操作（或多个操作）排队，并通知队列它应该开始处理它们。如果排队的操作依赖于尚未完成的其他操作，则执行可能会延迟。如果操作队列本身暂停或正在执行其最大并发操作数，则执行也可能会延迟。以下示例显示了将操作添加到队列的基本语法。

```
[aQueue addOperation:anOp]; // Add a single operation
[aQueue addOperations:anArrayOfOps waitUntilFinished:NO]; // Add multiple operations
[aQueue addOperationWithBlock:^{
   /* Do something. */
}];
```

在将操作对象添加到队列之前，您应该对操作对象进行所有必要的配置和修改，因为一旦添加操作对象，该操作可能随时运行，这对于更改具有预期效果可能太迟了。

尽管 `NSOperationQueue` 类是为并发执行操作而设计的，但可以强制一个队列一次仅运行一个操作。`setMaxConcurrentOperationCount：`方法允许您为操作队列对象配置最大并发操作数。将值1传递给此方法会导致队列一次只执行一个操作。

##### 手动执行操作

##### 取消操作

一旦添加到操作队列中，操作对象实际上由队列拥有并且不能被删除。从队列中移除操作的唯一方法是取消操作。您可以通过调用其`cancel`方法来取消单个操作对象，也可以通过调用队列对象的`cancelAllOperations`方法来取消队列中的所有操作对象。

##### 等待操作完成

为了获得最佳性能，您应该将您的操作设计为尽可能异步，使应用程序在执行操作时可以自由地执行额外的工作。如果创建操作的代码也处理该操作的结果，则可以使用`NSOperation`的`waitUntilFinished`方法来阻止该代码，直到操作完成。但是，一般来说，如果可以提供帮助，最好避免调用此方法。阻止当前线程可能是一个方便的解决方案，但它确实会在您的代码中引入更多序列化并限制并发的总体数量。

除了等待单个操作完成之外，您还可以通过调用`NSOperationQueue`的`waitUntilAllOperationsAreFinished`方法来等待队列中的所有操作。当等待整个队列完成时，请注意您的应用程序的其他线程仍然可以将操作添加到队列中，从而延长等待时间。

##### 暂停和恢复队列

如果您想暂时停止执行操作，则可以使用`setSuspended：`方法挂起相应的操作队列。暂停队列不会导致已执行的操作在其任务中间暂停。

