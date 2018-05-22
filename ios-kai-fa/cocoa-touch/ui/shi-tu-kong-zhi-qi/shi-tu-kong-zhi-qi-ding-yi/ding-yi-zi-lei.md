# 定义子类

内容视图控制器拥有它所有的视图，并对这些视图中的数据负责。

容器视图控制器不拥有它所有的视图;其一些视图由其他视图控制器管理。

对于内容视图控制器，最常见的父类如下所示：

* 当你的视图控制器的主视图是一个表时，使用 UITableViewController。

* 当你的视图控制器的主视图是一个集合视图时，使用UICollectionViewController。

* 对所有其他视图控制器使用 UIViewController。

### 定义用户界面

使用 Xcode 中的 storyboard 文件直观地定义视图控制器的 UI。虽然您也可以通过编程方式创建 UI，但故事板是一种很好的方式，可以将视图控制器的内容可视化，并针对不同环境自定义视图层次结构。![](/assets/A storyboard holds a set of view controllers and views.png)每个矩形区域代表视图控制器及其相关视图。视图控制器之间的箭头是视图控制器关系和 segues。关系将容器视图控制器连接到其子视图控制器。Segues可让您在界面中的视图控制器之间导航。

使用故事板编辑器执行以下操作：

* 添加，排列和配置视图控制器的视图。

* 连接 outlets 和操作（actions）。

* 在视图控制器之间创建关系和 segues。

* 为不同的 size class 定制您的布局和视图。

* 添加手势识别器来处理用户与视图的交互。

### 处理用户交互

应用程序的响应者对象处理传入的事件并采取适当的操作。虽然视图控制器是响应者对象，但他们很少直接处理触摸事件。相反，视图控制器通常通过以下方式处理事件。

* **视图控制器定义用于处理更高级别事件的操作方法。**Action 方法回应：
  * 特定动作（Specific actions.）控件和一些视图调用一个动作方法来报告特定的交互。

  * 手势识别器（Gesture recognizers）。手势识别器调用动作方法来报告手势的当前状态。使用您的视图控制器来处理状态更改或响应完成的手势。
* **视图控制器观察由系统或其他对象发送的通知。**通知报告更改并且是视图控制器更新其状态的一种方式。

* **视图控制器充当另一个对象的数据源或委托。**视图控制器通常用于管理表格和集合视图的数据。您还可以将它们用作CLLocationManager对象等对象的委托，该对象将更新的位置值发送给其委托。

响应事件通常涉及更新视图的内容，这需要引用这些视图。您的视图控制器是定义任何您需要稍后修改的视图的 outlets 的好地方。使用清单4-1中显示的语法将您的 outlets 声明为属性。清单中的自定义类定义了两个出口（由`IBOutlet`关键字指定）和一个动作方法（由`IBAction`返回类型指定）。outlets 存储对故事板（storyboard）中按钮和文本字段的引用，而操作方法响应按钮的点击。

清单4-1定义视图控制器类中的出口和动作

```
// Objective-C
@interface MyViewController : UIViewController
@property (weak, nonatomic) IBOutlet UIButton *myButton;
@property (weak, nonatomic) IBOutlet UITextField *myTextField;
 
- (IBAction)myButtonAction:(id)sender;
 
@end
```

```
// Swift
class MyViewController: UIViewController {
    @IBOutlet weak var myButton : UIButton!
    @IBOutlet weak var myTextField : UITextField!
    
    @IBAction func myButtonAction(sender: id)
}
```

在故事板中，请记住将视图控制器的出口和操作连接到相应的视图。在故事板文件中连接出口和动作可确保在视图加载时配置出口和动作。

### 在运行时显示视图

故事板使加载和显示视图控制器视图的过程非常简单。需要时，UIKit会自动从故事板文件加载视图。作为加载过程的一部分，UIKit执行以下任务序列：

1. 使用故事板文件中的信息实例化视图。

2. 连接所有出口（outlets）和操作（actions）。

3. 将根视图分配给视图控制器的`view`属性。

4. 调用视图控制器的`awakeFromNib`方法。

   当调用此方法时，视图控制器的特征集合为空，视图可能不在其最终位置。

5. 调用视图控制器的`viewDidLoad`方法。

   使用此方法添加或删除视图，修改布局约束并为视图加载数据。

在屏幕上显示视图控制器的视图之前，UIKit为您提供了一些额外的机会，可以在屏幕前后准备这些视图。具体来说，UIKit执行以下任务序列：

1. 调用视图控制器的`viewWillAppear：`方法，让它知道它的视图即将出现在屏幕上。

2. 更新视图的布局。

3. 在屏幕上显示视图。

4. 视图在屏幕上时调用`viewDidAppear：`方法。

添加，删除或修改视图的大小或位置时，请记住添加和删除适用于这些视图的任何约束。对视图层次结构进行与布局相关的更改会导致UIKit将布局标记为 dirty。在下一个更新周期中，布局引擎使用当前布局约束条件计算视图的大小和位置，并将这些更改应用于视图层次结构。

### 管理视图布局

当视图的大小和位置发生变化时，UIKit将更新视图层次结构的布局信息。对于使用自动布局（Auto Layout）配置的视图，UIKit会使用自动布局引擎并根据当前约束使用它来更新布局。UIKit还会让其他感兴趣的对象（如活动的展示控制器）知道与布局更改相关联，以便他们可以做出相应的响应。

在布局过程中，UIKit会在几个点通知您，以便您可以执行其他与布局相关的任务。使用这些通知来修改布局约束或在应用布局约束后对布局进行最终调整。在布局过程中，UIKit为每个受影响的视图控制器执行以下操作：

1. 根据需要更新视图控制器及其视图的特征集合（trait collections）；
2. 调用视图控制器的`viewWillLayoutSubviews`方法。

3. 调用当前`UIPresentationController`对象的`containerViewWillLayoutSubviews`方法。

4. 调用视图控制器根视图的`layoutSubviews`方法。

   此方法的默认实现使用可用的约束来计算新的布局信息。然后该方法遍历视图层次并为每个子视图调用`layoutSubviews`。

5. 将计算的布局信息应用于视图。

6. 调用视图控制器的`viewDidLayoutSubviews`方法。

7. 调用当前`UIPresentationController`对象的`containerViewDidLayoutSubviews`方法。

视图控制器可以使用`viewWillLayoutSubviews`和`viewDidLayoutSubviews`方法来执行可能影响布局过程的其他更新。在布局之前，您可以添加或删除视图，更新视图的大小或位置，更新约束或更新其他视图相关的属性。布局之后，您可能会重新加载表格数据，更新其他视图的内容，或者对视图的大小和位置进行最终调整。

以下是有效管理布局的一些提示：

* **使用自动布局。**使用“自动布局”创建的约束是一种灵活而简单的方法，可将您的内容放置在不同的屏幕尺寸上。

* **利用顶部和底部布局指南。**将内容布置到这些指南可确保您的内容始终可见。顶部布局引导因素的位置在状态栏和导航栏的高度。同样，底部布局指南的位置因素在标签栏或工具栏的高度。

* **记住在添加或删除视图时更新约束。**如果动态添加或删除视图，请记住更新相应的约束。

* **动画视图控制器的视图时暂时移除约束。**当使用UIKit核心动画对视图进行动画处理时，请在动画持续时间内移除约束条件，并在动画完成时将其添加回去。如果视图的位置或大小在动画过程中发生变化，请记得更新您的约束。

### 高效管理内存

尽管内存分配的大部分内容都由您决定，但表4-1列出了您最可能分配或释放内存的`UIViewController`的方法。大多数解除分配涉及删除对象的强引用。要删除对象的强引用，请将指向该对象的属性和变量设置为 nil。

表4-1分配和释放内存的位置：

* 分配视图控制器所需的关键数据结构。

  初始化方法

  你的自定义初始化方法（不管它是否被命名为init或其他）始终负责将视图控制器对象置于已知的良好状态。使用这些方法分配需要的任何数据结构以确保正确的操作。

* 分配或加载要在视图中显示的数据。

  viewDidLoad

  使用viewDidLoad方法加载您想要显示的任何数据对象。在调用此方法时，您的视图对象将保证存在并处于已知良好状态。

* 响应低内存通知。

  [didReceiveMemoryWarning](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621409-didreceivememorywarning)

* 释放视图控制器所需的关键数据结构。

  [dealloc](https://developer.apple.com/documentation/objectivec/nsobject/1571947-dealloc)

  重写此方法仅用于执行视图控制器类的任何最后一次清理。系统会自动释放存储在类的实例变量和属性中的对象，因此您不需要明确地释放这些对象。



