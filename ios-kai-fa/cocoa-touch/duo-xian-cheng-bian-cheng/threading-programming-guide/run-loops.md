# Run Loops

运行循环是与线程相关的基础架构的一部分。运行循环是一个事件处理循环，用于安排工作并协调接收到的事件。运行循环的目的是在有任务要做时让线程忙碌，并在没有任务时让线程进入睡眠状态。

运行循环管理不是完全自动的。您仍然必须设计您的线程代码以在适当的时间启动运行循环并响应传入的事件。Cocoa 和 Core Foundation 都提供运行循环对象，以帮助您配置和管理线程的运行循环。您的应用程序不需要明确创建这些对象;每个线程（包括应用程序的主线程）都有一个关联的运行循环对象。但是，只有次要线程需要显式运行其运行循环。应用程序框架自动设置并在主线程上运行运行循环，作为应用程序启动过程的一部分。

### 运行循环的解剖

运行循环非常类似于它的名字。它是一个循环，您的线程输入并用它来运行事件处理程序以响应传入事件。您的代码提供了用于实现运行循环的实际循环部分的控制语句 - 换句话说，您的代码提供了驱动运行循环的 while 或 for 循环。在循环中，使用运行循环对象来“运行”接收事件并调用已安装的处理程序的事件处理代码。

运行循环接收来自两种不同类型源的事件。输入源传递异步事件，通常是来自另一个线程或来自不同应用程序的消息。计时器源提供同步事件，发生在计划时间或重复间隔。这两种类型的源都使用特定于应用程序的处理程序来处理事件到达时的状态。

下图显示了运行循环和各种来源的概念结构。输入源将异步事件传递给相应的处理程序，并导致`runUntilDate：`方法（在线程的关联`NSRunLoop`对象上调用）退出。计时器源将事件传递到其处理程序例程，但不会导致运行循环退出。

运行循环的结构及其来源：

![](/assets/runloop.jpg)

除了处理输入源之外，运行循环还会生成有关运行循环行为的通知。已注册的运行循环观察程序可以接收这些通知并使用它们对线程执行附加处理。您可以使用 Core Foundation 在线程上安装运行循环观察器。

##### 运行循环模式

运行循环模式是要监视的输入源和定时器的集合，以及要通知的运行循环观察器的集合。每次运行运行循环时，都需要明确或隐式地指定要运行的特定“模式”。在运行循环的过程中，只监视与该模式相关的源并允许其发送事件。同样，只有与该模式相关的观察者才会收到运行循环进度的通知。与其他模式相关的源保持任何新事件，直到随后以适当的模式通过循环。

在你的代码中，你可以通过名字来识别模式。 Cocoa 和 Core Foundation 都定义了一个默认模式和几种常用模式，以及用于在代码中指定这些模式的字符串。您可以通过为模式名称指定自定义字符串来定义自定义模式。虽然分配给自定义模式的名称是任意的，但这些模式的内容却不是。您必须确保将一个或多个输入源，定时器或运行循环观察器添加到您创建的任何模式中，以便它们有用。

您可以使用模式在特定的运行循环中过滤掉不需要的源中的事件。大多数情况下，您需要在系统定义的“默认”模式下运行您的运行循环。但是，模态面板可能会以“模态”模式运行。在此模式下，只有与模态面板相关的源才会将事件传递给线程。对于辅助线程，您可以使用自定义模式来防止低优先级的源在时间关键型操作期间传递事件。

> 注意：模式根据事件的来源进行区分，而不是事件的类型。例如，您不会使用模式来仅匹配鼠标按下事件或仅匹配键盘事件。您可以使用模式来侦听不同的端口集，暂时暂停定时器，或者更改当前正在监视的源和运行循环观察器。

预定义的运行循环模式：

* 默认（Default）

[`NSDefaultRunLoopMode`](https://developer.apple.com/documentation/foundation/runloopmode/1409732-defaultrunloopmode)\(Cocoa\)

[`kCFRunLoopDefaultMode`](https://developer.apple.com/documentation/corefoundation/kcfrunloopdefaultmode)\(Core Foundation\)

默认模式是用于大多数操作的模式。大多数情况下，您应该使用此模式启动运行循环并配置输入源。

* 连接（Connection）

[`NSConnectionReplyMode`](https://developer.apple.com/documentation/foundation/nsconnectionreplymode)\(Cocoa\)

Cocoa将此模式与NSConnection对象一起使用来监视回复。你应该很少需要自己使用这种模式。

* Modal

[`NSModalPanelRunLoopMode`](https://developer.apple.com/documentation/appkit/nsmodalpanelrunloopmode)\(Cocoa\)

Cocoa 使用这种模式来识别用于模态面板的事件。

* 事件跟踪（Event tracking）

[`NSEventTrackingRunLoopMode`](https://developer.apple.com/documentation/foundation/runloopmode/1428765-eventtrackingrunloopmode)\(Cocoa\)

Cocoa使用这种模式来限制鼠标拖拽循环和其他类型的用户界面追踪循环中的传入事件。

* 通用模式（Common modes）

[`NSRunLoopCommonModes`](https://developer.apple.com/documentation/foundation/nsrunloopcommonmodes)\(Cocoa\)

[`kCFRunLoopCommonModes`](https://developer.apple.com/documentation/corefoundation/cfrunloopmode/1542364-commonmodes)\(Core Foundation\)

这是一个可配置的常用模式组。将输入源与此模式关联也会将其与组中的每个模式相关联。对于Cocoa应用程序，默认情况下，此集合包含默认，模式和事件跟踪模式。Core Foundation 最初只包含默认模式。您可以使用`CFRunLoopAddCommonMode` 函数将自定义模式添加到该集合。

##### 输入源

输入源以异步方式向您的线程传递事件。事件的来源取决于输入源的类型，输入源通常是两类中的一类。基于端口的输入源监视你的应用程序的 Mach 端口。自定义输入源监视自定义事件源。就您的运行循环而言，输入源是基于端口还是自定义应该没有关系。系统通常实现两种类型的输入源，您可以按原样使用它们。两个来源之间的唯一区别是它们如何发出信号。基于端口的源由内核自动发出信号，自定义源必须从另一个线程手动发送信号。

创建输入源时，可以将其分配给运行循环的一个或多个模式。模式会影响在任何特定时刻监视哪些输入源。大多数情况下，您在默认模式下运行循环，但您也可以指定自定义模式。如果输入源不处于当前监控的模式，则会生成其生成的所有事件，直到运行循环以正确的模式运行。

###### 基于端口的来源

Cocoa和Core Foundation为使用与端口相关的对象和函数创建基于端口的输入源提供了内置的支持。例如，在Cocoa中，您根本不需要直接创建输入源。您只需创建一个端口对象并使用NSPort的方法将该端口添加到运行循环。port对象为您处理所需输入源的创建和配置。

在Core Foundation中，您必须手动创建端口及其运行循环源。在这两种情况下，都使用与端口不透明类型（CFMachPortRef，CFMessagePortRef或CFSocketRef）关联的函数来创建适当的对象。

###### 自定义输入源

要创建自定义输入源，您必须使用与Core Foundation中的CFRunLoopSourceRef opaque类型关联的函数。您可以使用多个回调函数来配置自定义输入源。Core Foundation在不同的点调用这些函数来配置源代码，处理所有传入的事件，并在源代码从运行循环中移除时拆除源代码。

除了在事件到达时定义自定义源的行为之外，还必须定义事件传递机制。这部分源代码在单独的线程上运行，负责为输入源提供数据，并在数据准备好处理时用信号通知它。事件传递机制取决于你，但不必过于复杂。

###### Cocoa 执行选择器来源

除了基于端口的源代码之外，Cocoa还定义了一个自定义输入源，允许您在任何线程上执行选择器。与基于端口的源一样，执行选择器请求在目标线程上被序列化，从而减轻了在一个线程上运行多个方法时可能发生的许多同步问题。与基于端口的源不同，执行选择器源在执行其选择器后从运行循环中移除。

在另一个线程上执行选择器时，目标线程必须具有活动的运行循环。对于你创建的线程，这意味着等待你的代码明确地启动运行循环。但是，因为主线程启动自己的运行循环，只要应用程序调用应用程序委托的`applicationDidFinishLaunching`：方法，就可以开始在该线程上发出调用。运行循环每次通过循环处理所有排队的执行选择器调用，而不是在每次循环迭代期间处理一个。

在其他线程上执行选择器：

* [performSelectorOnMainThread:withObject:waitUntilDone:](https://developer.apple.com/documentation/objectivec/nsobject/1414900-performselectoronmainthread) [performSelectorOnMainThread:withObject:waitUntilDone:modes:](https://developer.apple.com/documentation/objectivec/nsobject/1411637-performselector)

在该线程的下一个运行循环中执行应用程序主线程上的指定选择器。这些方法使您可以选择阻止当前线程，直到执行选择器。

* [performSelector:onThread:withObject:waitUntilDone:](https://developer.apple.com/documentation/objectivec/nsobject/1414476-perform) [performSelector:onThread:withObject:waitUntilDone:modes:](https://developer.apple.com/documentation/objectivec/nsobject/1417922-perform)

在您拥有NSThread对象的任何线程上执行指定的选择器。这些方法使您可以选择阻止当前线程，直到执行选择器。

* [performSelector:withObject:afterDelay:](https://developer.apple.com/documentation/objectivec/nsobject/1416176-perform) [performSelector:withObject:afterDelay:inModes:](https://developer.apple.com/documentation/objectivec/nsobject/1415652-performselector)

在下一个运行循环周期和可选的延迟周期之后，在当前线程上执行指定的选择器。由于它等到下一个运行循环执行选择器，所以这些方法会从当前正在执行的代码中提供一个自动迷你延迟。多个排队选择器按照它们排队的顺序依次执行。

* [cancelPreviousPerformRequestsWithTarget:](https://developer.apple.com/documentation/objectivec/nsobject/1417611-cancelpreviousperformrequestswit) [cancelPreviousPerformRequestsWithTarget:selector:object:](https://developer.apple.com/documentation/objectivec/nsobject/1410849-cancelpreviousperformrequestswit)

允许您使用`performSelector：withObject：afterDelay`：或`performSelector：withObject：afterDelay：inModes`：方法取消发送到当前线程的消息。

##### 定时器来源

定时器源在未来的预设时间将事件同步传递给您的线程。定时器是线程通知自己做某事的一种方式。例如，搜索字段可以使用定时器在用户的连续击键之间经过一段时间后启动自动搜索。使用此延迟时间使用户有机会在开始搜索之前尽可能多地输入所需的搜索字符串。

虽然它生成基于时间的通知，但计时器不是实时机制。与输入源一样，定时器与运行循环的特定模式相关联。如果一个定时器不处于当前由运行循环监视的模式，那么只有在定时器的一种受支持模式下运行循环时才会触发定时器。同样，如果在运行循环处于执行处理程序例程的中间时触发定时器，则定时器将等待下一次通过运行循环来调用其处理程序例程。如果运行循环根本没有运行，则定时器不会启动。

您可以将定时器配置为仅生成一次或重复生成事件。重复计时器会根据预定的开火时间自动重新安排时间，而不是实际的开火时间。例如，如果定时器计划在特定时间和之后每隔5秒触发一次，则即使实际触发时间延迟，计划触发时间也会始终以原来的5秒时间间隔进行。如果发射时间延迟太多以至于未能达到一个或多个预定的发射时间，则在错过的时间段内，定时器仅被发射一次。在错过时间点火后，定时器重新安排下一次预定的射击时间。

##### 运行循环观察者

与发生适当异步事件或同步事件时触发的源相比，运行循环观察器在运行循环本身的执行过程中会在特定位置触发。您可以使用运行循环观察器来准备线程来处理给定的事件，或者在线程进入睡眠之前准备线程。您可以将运行循环观察程序与运行循环中的以下事件相关联：

* 运行循环的入口。
* 运行循环即将处理计时器时。

* 运行循环即将处理输入源时。

* 当运行循环即将进入睡眠状态时。

* 运行循环唤醒时，但在处理唤醒它的事件之前。

* 从运行循环退出。

您可以使用Core Foundation将运行循环观察器添加到应用程序。要创建运行循环观察程序，请创建CFRunLoopObserverRef不透明类型的新实例。此类型会跟踪您的自定义回调函数以及它感兴趣的活动。

与定时器类似，运行循环观察者可以使用一次或重复使用。一次观察者在触发后从运行循环中删除，而重复的观察者仍然附着。您可以指定观察者在创建时是运行一次还是反复运行。

##### 事件的运行循环序列

每次运行它时，线程的运行循环都会处理未决（pending）事件，并为任何附加的观察者生成通知。它的执行顺序非常具体，如下所示：

1. 通知观察者已经输入了运行循环

2. 通知观察者任何准备好的计时器即将触发。

3. 通知观察者，任何不是基于端口的输入源即将触发。

4. 启动任何可以触发的非基于端口的输入源。

5. 如果基于端口的输入源已准备好并正在等待触发，请立即处理该事件。转到第9步。

6. 通知观察者该线程即将入睡。

7. 让线程进入休眠状态，直到发生以下事件之一：

   * 一个基于端口的输入源事件到达。

   * 计时器启动。

   * 为运行循环设置的超时值已过期。

   * 运行循环被明确地唤醒。

8. 通知观察者线程刚刚醒来。

9. 处理未决事件。

   * 如果用户定义的定时器启动，则处理定时器事件并重新启动循环。转到第2步。

   * 如果输入源被触发，则交付事件。

   * 如果运行循环显式唤醒但尚未超时，请重新启动循环。转到第2步。

10. 通知观察者运行循环已经退出。

因为定时器和输入源的观察者通知是在这些事件实际发生之前交付的，所以通知时间与实际事件时间之间可能存在差距。如果这些事件之间的时间很关键，则可以使用睡眠和从睡眠中醒来的通知来帮助您关联实际事件之间的时间。

由于定时器和其他周期性事件是在运行运行循环时交付的，因此绕过该循环会中断这些事件的交付。无论何时通过输入一个循环并重复地从应用程序请求事件来实现鼠标跟踪例程，都会发生此行为的典型示例。由于您的代码直接抓取事件，而不是让应用程序正常调度这些事件，因此在您的鼠标跟踪例程退出并将控制返回给应用程序之前，激活的定时器将无法触发。

运行循环可以使用运行循环对象显式唤醒。其他事件也可能导致运行循环被唤醒。例如，添加另一个基于非端口的输入源会唤醒运行循环，以便可以立即处理输入源，而不是等待其他事件发生。

### 何时使用运行循环？

唯一需要明确运行运行循环的时候是为应用程序创建辅助线程。应用程序主线程的运行循环是基础架构中至关重要的一部分。因此，应用程序框架提供了运行主应用程序循环并自动启动该循环的代码。iOS中的UIApplication（或OS X中的NSApplication）的`run`方法启动应用程序的主循环，作为正常启动顺序的一部分。如果您使用Xcode模板项目来创建应用程序，则不应该显式调用这些例程。

对于辅助线程，您需要确定是否需要运行循环，如果是，则自行配置并启动它。在任何情况下，您都不需要启动线程的运行循环。例如，如果您使用线程执行一些长时间运行且预先确定的任务，则可以避免启动运行循环。运行循环适用于需要更多与线程交互的情况。例如，如果您打算执行以下任何操作，则需要启动运行循环：

* 使用端口或自定义输入源与其他线程进行通信。

* 在线程上使用计时器。

* 使用Cocoa应用程序中的任何`performSelector` ...方法。

* 保持线程执行定期任务。

### 使用运行循环对象

运行循环对象提供了将输入源，定时器和运行循环观察器添加到运行循环并运行它的主要接口。每个线程都有一个与之关联的运行循环对象。在Cocoa中，这个对象是NSRunLoop类的一个实例。在低级应用程序中，它是一个指向CFRunLoopRef不透明类型的指针。

##### 获取运行循环对象

##### 配置运行循环

##### 启动运行循环

##### 退出运行循环

##### 线程安全和运行循环对象

### 配置运行循环源

##### 定义自定义输入源

###### 定义输入源

###### 与输入源的客户端协调

###### 发信号输入源

##### 配置定时器源

##### 配置基于端口的输入源

###### 配置一个NSMachPort对象

**实现主线程代码**

**实现次要线程代码**

###### 配置一个NSMessagePort对象

###### 在Core Foundation中配置基于端口的输入源



