# 自定义过渡（Transition）动画

UIKit提供了一组用于呈现视图控制器的标准过渡样式，您可以使用自定义过渡来补充标准过渡。

### 过渡动画序列

过渡动画将一个视图控制器的内容交换为另一个视图控制器的内容。有两种类型的过渡：呈现和解除。呈现过渡会将新的视图控制器添加到您的应用程序的视图控制器层次结构中，而解除（dismissal）过渡会从层次结构中删除一个或多个视图控制器。

实现过渡动画需要很多对象。UIKit提供了过渡中涉及的所有对象的默认版本，您可以自定义他们。如果你选择了正确的对象集，你应该可以用少量代码创建动画。如果您利用UIKit提供的现有代码，即使包含交互的动画也可以轻松实现。

##### 过渡代理（Transitioning Delegate）

过渡代理是过渡动画和自定义呈现的起点。过渡代理是您定义的并符合`UIViewControllerTransitioningDelegate`协议的对象。它的工作是为UIKit提供以下对象：

* **动画制造者对象（Animator objects）**。动画制造者对象负责创建用于显示或隐藏视图控制器视图的动画。过渡代理可以提供单独的动画制造者对象来呈现和解除视图控制器。动画制造者对象符合`UIViewControllerAnimatedTransitioning`协议。

* **交互式动画制造者对象（Interactive animator objects）。**交互式动画制作者对象使用触摸事件或手势识别器驱动自定义动画的时间。交互式动画师对象符合`UIViewControllerInteractiveTransitioning`协议。

  创建交互式动画制造者的最简单方法是继承`UIPercentDrivenInteractiveTransition`类并将事件处理代码添加到您的子类中。该类控制使用现有动画制造者对象创建动画的时间。如果您创建自己的交互式动画制造者，则必须自己渲染动画的每个帧。

* **呈现控制器（Presentation controller）。**呈现控制器管理视图控制器在屏幕上的呈现样式。系统为内置呈现样式提供呈现控制器，您可以为自己的呈现样式提供自定义呈现控制器。

将过渡代理分配给视图控制器的`transitioningDelegate`属性将告知UIKit您要执行自定义过渡或呈现。你的代理可以选择它提供的对象。如果您不提供动画制造者（animator）对象，则UIKit在视图控制器的`modalTransitionStyle`属性中使用标准过渡动画。

图10-1显示了过渡代理和动画制造者（animator）对象与被呈现（presented）的视图控制器的关系。仅当视图控制器的`modalPresentationStyle`属性设置为`UIModalPresentationCustom`时才使用呈现控制器。

图10-1 自定义呈现和动画制造者对象![](/assets/The custom presentation and animator objects.png)

##### 自定义动画序列

当被呈现（presented）视图控制器的`transitioningDelegate`属性包含有效对象时，UIKit会使用您提供的自定义动画制造者对象呈现该视图控制器。在准备呈现时，UIKit会调用您的过渡代理的`animationControllerForPresentedController：presentingController：sourceController：`方法来检索自定义动画制造者对象。如果有对象可用，UIKit将执行以下步骤：

1. UIKit调用转换委托的`interactionControllerForPresentation：`方法来查看交互式动画器对象是否可用。如果该方法返回nil，UIKit将执行动画而没有用户交互。

2. UIKit调用动画器对象的`transitionDuration：`方法来获取动画持续时间。

3. UIKit调用适当的方法来启动动画：

   * 对于非交互式动画，UIKit调用animator对象的`animateTransition：`方法。

   * 对于交互式动画，UIKit调用交互式动画器对象的`startInteractiveTransition：`方法。

4. UIKit等待animator对象调用上下文过渡对象的`completeTransition：`方法。

   您的自定义动画器在其动画完成后调用此方法，通常在动画的完成块中。调用这个方法结束过渡并让UIKit知道它能调用`presentViewController:animated:completion:`方法的完成操作者和调用动画器对象自己的`animationEnded:`方法。

解雇视图控制器时，UIKit会调用过渡代理的`animationControllerForDismissedController：`方法，并执行以下步骤：

1. UIKit调用转换委托的`interactionControllerForDismissal：`方法来查看交互式动画器对象是否可用。如果该方法返回nil，UIKit将执行动画而没有用户交互。

2. UIKit调用动画器对象的`transitionDuration：`方法来获取动画持续时间。

3. UIKit调用适当的方法来启动动画：

   * 对于非交互式动画，UIKit调用animator对象的`animateTransition：`方法。

   * 对于交互式动画，UIKit调用交互式动画器对象的`startInteractiveTransition：`方法。

4. UIKit等待animator对象调用上下文过渡对象的`completeTransition：`方法。

   您的自定义动画器在其动画完成后调用此方法，通常在动画的完成块中。调用这个方法结束过渡并让UIKit知道它能调用`presentViewController:animated:completion:`方法的完成操作者和调用动画器对象自己的`animationEnded:`方法。

> **重要**
>
> 在动画结束时调用`completeTransition：`方法是必需的。UIKit不会结束过渡过程，不会将控制权返回给您的应用程序，直到您调用该方法。

##### 过渡上下文对象（Transitioning Context Object）

在过渡动画开始之前，UIKit会创建一个过渡上下文对象并填充关于如何执行动画的信息。过渡上下文对象是您代码的重要组成部分。它实现了`UIViewControllerContextTransitioning`协议并存储对转换中涉及的视图控制器和视图的引用。它还存储有关如何执行过渡的信息，包括动画是否为交互式。您的动画器对象需要所有这些信息来设置和执行实际的动画。

> 重要
>
> 设置自定义动画时，请始终使用过渡上下文对象中的对象和数据，而不是您自己管理的任何缓存信息。过渡可能发生在各种情况下，其中一些情况可能会改变动画参数。过渡的上下文对象保证具有执行动画所需的正确信息，而在调用动画器的方法时，缓存的信息可能会过时。

图10-2显示了过渡上下文对象如何与其他对象进行交互。您的动画器对象在其`animateTransition：`方法中接收该对象。您创建的动画应该在提供的容器视图中进行。例如，在呈现视图控制器时，将其视图添加为容器视图的子视图。容器视图可能是窗口或常规视图，但它始终配置为运行您的动画。

图10-2 过渡上下文对象![](/assets/The transitioning context object.png)

##### 过渡协调器（Transition Coordinator）

对于内置过渡和自定义过渡，UIKit都会创建一个过渡协调器对象，以促进您可能需要执行的任何额外动画。除了呈现和解除视图控制器之外，当发生界面旋转或视图控制器的frame更改时，可能会发生过渡。所有这些过渡都代表对视图层次结构的更改。过渡协调器是一种跟踪这些更改并同时激活自己的内容的方式。要访问过渡协调器，请获取受影响的视图控制器的`transitionCoordinator`属性中的对象。过渡协调器仅在过渡期间存在。

图10-3显示了过渡协调器与呈现中涉及的视图控制器之间的关系。使用过渡协调器获取有关过渡的信息，并注册想要与过渡动画同时执行的动画块。过渡协调器对象符合`UIViewControllerTransitionCoordinatorContext`协议，该协议提供时间信息，有关动画当前状态的信息以及过渡中涉及的视图和视图控制器。当你的动画块被执行时，它们同样会收到一个具有相同信息的上下文对象。

图10-3 过渡协调器对象![](/assets/The transition coordinator objects.png)

### 使用自定义动画呈现视图控制器

要使用自定义动画呈现视图控制器，请在现有视图控制器的操作方法中执行以下操作：

1. 创建您想要呈现的视图控制器。

2. 创建您的自定义过渡代理对象并将其分配给视图控制器的`transitioningDelegate`属性。过渡代理的方法应该在被问到时创建并返回自定义的动画器对象。

3. 调用`presentViewController：animated：completion：`方法来呈现视图控制器。

当您调用`presentViewController：animated：completion：`方法时，UIKit会启动呈现过程。呈现在下次运行循环迭代期间开始并继续，直到您的自定义动画器调用`completeTransition：`方法。交互式过渡允许您在过渡过程中处理触摸事件，但非交互式转换会在动画器对象指定的持续时间内运行。

### 实现过渡代理（Transitioning Delegate）

过渡代理的目的是创建并返回您的自定义对象。程序清单10-1显示了过渡方法的实现是多么简单。本示例创建并返回一个自定义动画器对象。大部分实际工作都由动画制作者对象本身处理。

清单10-1 创建一个动画器对象

```
- (id<UIViewControllerAnimatedTransitioning>)
    animationControllerForPresentedController:(UIViewController *)presented
                         presentingController:(UIViewController *)presenting
                             sourceController:(UIViewController *)source {
    MyAnimator* animator = [[MyAnimator alloc] init];
    return animator;
}
```

您的过渡代理的其他方法可以与前面列表中的方法一样简单。您还可以结合自定义逻辑，根据应用程序的当前状态返回不同的动画制作器对象。

### 实现动画制作器对象（Animator Objects）

动画器对象是采用`UIViewControllerAnimatedTransitioning`协议的任何对象。动画器对象创建在固定时间段内执行的动画。animator对象的关键是`animateTransition：`方法，您可以使用它来创建实际的动画。动画过程大致分为以下几个部分：

1. 获取动画参数。

2. 使用Core Animation或UIView动画方法创建动画。

3. 清理并完成过渡。

##### 获取动画参数

传递给您的`animateTransition：`方法的上下文过渡对象包含执行动画时要使用的数据。从上下文过渡对象获取更多最新信息时，切勿使用自己的缓存信息或从视图控制器获取信息。呈现和解除视图控制器有时会涉及视图控制器之外的对象。例如，自定义呈现控制器可能会将背景视图添加为演示文稿的一部分。上下文过渡对象会考虑额外的视图和对象，并为您提供正确的动画视图。

* 调用`viewControllerForKey：`方法两次以获得转换中涉及的“from”和“to”视图控制器。切勿假设您知道哪些视图控制器参与过渡。UIKit可能会更改视图控制器，同时适应新的特征环境或响应来自应用程序的请求。

* 调用`containerView`方法来获取动画的父视图。将所有关键子视图添加到此视图。例如，在呈现期间，将被呈现（presented）的视图控制器的视图添加到此视图。

* 调用`viewForKey：`方法来添加或删除视图。视图控制器的视图可能不是在过渡期间添加或删除的唯一视图。呈现控制器可能会将视图插入到层次结构中，这些视图也必须添加或删除。`viewForKey：`方法返回包含您需要添加或删除的所有内容的根视图。

* 调用`finalFrameForViewController：`方法来获取添加或删除视图的最终frame矩形。

上下文过渡对象使用“from”和“to”命名来标识过渡中涉及的视图控制器，视图和frame矩形。“from”视图控制器始终是其视图在过渡开始时在屏幕上的视图控制器，而“to”视图控制器是其视图在过渡结束时可见的视图控制器。如图10-4所示，“from”和“to”视图控制器在呈现和解除之间交换位置。

图10-4 from和to对象![](/assets/The from and to objects.png)

交换这些值可以更轻松地编写处理呈现和解除的单个动画器。当你设计你的动画器时，你所要做的就是包含一个属性，以知道它是动画呈现还是解雇。两者之间唯一需要的区别如下：

* 对于呈现，请将“to”视图添加到容器视图层次结构中。

* 对于解雇，从容器视图层次结构中删除“from”视图。

##### 创建过渡动画

在典型的呈现中，属于被呈现的视图控制器的视图被放置到位。其他视图可能会作为呈现的一部分进行动画，但动画的主要目标始终是要添加到视图层次结构中的视图。

在为主视图制作动画时，您配置动画的基本操作是相同的。您可以从过渡的上下文对象中获取所需的对象和数据，并使用该信息创建实际的动画。

* 呈现动画：
  * 使用`viewControllerForKey：`和`viewForKey：`方法来检索过渡中涉及的视图控制器和视图。

  * 设置“to”视图的起始位置。将其他任何属性也设置为它们的起始值。

  * 从上下文过渡对象的`finalFrameForViewController：`方法获取“to”视图的结束位置。

  * 将“to”视图添加为容器视图的子视图。

  * 创建动画。

    * 在动画块中，将“to”视图动画化为容器视图中的最终位置。

    * 在完成块中，调用`completeTransition：`方法并执行任何其他清理。
* 解除动画：
  * 使用`viewControllerForKey：`和`viewForKey：`方法来检索过渡中涉及的视图控制器和视图。

##### 动画后清理

### 为过渡添加互动性（Interactivity）

### 创建与过渡同时运行的动画

### 在动画中使用呈现控制器

###### 参考资料

1. [Customizing the Transition Animations](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/CustomizingtheTransitionAnimations.html#//apple_ref/doc/uid/TP40007457-CH16-SW1)



