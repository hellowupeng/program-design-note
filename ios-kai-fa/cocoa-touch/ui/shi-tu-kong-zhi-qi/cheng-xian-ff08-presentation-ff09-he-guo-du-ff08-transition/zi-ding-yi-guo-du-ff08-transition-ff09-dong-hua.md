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
  * 计算“from”视图的结束位置。这个视图属于目前被解雇的视图控制器。

  * 将“to”视图添加为容器视图的子视图。

    在呈现期间，当过渡完成时，属于呈现（presenting）视图控制器的视图将被删除。因此，您必须在解雇操作期间将该视图添加回容器。

  * 创建动画。

    * 在您的动画块中，将“from”视图动画到容器视图中的最终位置。将任何其他属性也设置为它们的最终值。

    * 在完成块中，从视图层次结构中移除“from”视图并调用`completeTransition：`方法。根据需要执行任何其他清理。

图10-5显示了一个自定义的呈现和解除过渡，可以对角地呈现视图。在呈现过程中，呈现的视图从屏幕开始并沿对角线向上并向左移动，直至可见。在解除期间，视图颠倒了方向，向下并向右移动，直到它再次离开屏幕。

图10-5 自定义呈现和解除![](/assets/A custom presentation and dismissal.png)清单10-2显示了如何实现图10-5中所示的过渡。在检索动画所需的对象之后，`animateTransition：`方法计算受影响视图的frame矩形。在呈现期间，被呈现（presented）的视图由`toView`变量表示。在解雇中，被解雇的视图由`fromView`变量表示。`presenting`属性是动画器对象本身的自定义属性，当创建动画制作器时，过渡代理设置为适当的值。

清单10-2 实现对角呈现和解除动画

```c
- (void)animateTransition:(id<UIViewControllerContextTransitioning>)transitionContext {
    // Get the set of relevant objects.
    UIView *containerView = [transitionContext containerView];
    UIViewController *fromVC = [transitionContext
            viewControllerForKey:UITransitionContextFromViewControllerKey];
    UIViewController *toVC   = [transitionContext
            viewControllerForKey:UITransitionContextToViewControllerKey];

    UIView *toView = [transitionContext viewForKey:UITransitionContextToViewKey];
    UIView *fromView = [transitionContext viewForKey:UITransitionContextFromViewKey];

    // Set up some variables for the animation.
    CGRect containerFrame = containerView.frame;
    CGRect toViewStartFrame = [transitionContext initialFrameForViewController:toVC];
    CGRect toViewFinalFrame = [transitionContext finalFrameForViewController:toVC];
    CGRect fromViewFinalFrame = [transitionContext finalFrameForViewController:fromVC];

    // Set up the animation parameters.
    if (self.presenting) {
        // Modify the frame of the presented view so that it starts
        // offscreen at the lower-right corner of the container.
        toViewStartFrame.origin.x = containerFrame.size.width;
        toViewStartFrame.origin.y = containerFrame.size.height;
    }
    else {
        // Modify the frame of the dismissed view so it ends in
        // the lower-right corner of the container view.
        fromViewFinalFrame = CGRectMake(containerFrame.size.width,
                                      containerFrame.size.height,
                                      toView.frame.size.width,
                                      toView.frame.size.height);
    }

    // Always add the "to" view to the container.
    // And it doesn't hurt to set its start frame.
    [containerView addSubview:toView];
    toView.frame = toViewStartFrame;

    // Animate using the animator's own duration value.
    [UIView animateWithDuration:[self transitionDuration:transitionContext]
                     animations:^{
                         if (self.presenting) {
                             // Move the presented view into position.
                             [toView setFrame:toViewFinalFrame];
                         }
                         else {
                             // Move the dismissed view offscreen.
                             [fromView setFrame:fromViewFinalFrame];
                         }
                     }
                     completion:^(BOOL finished){
                         BOOL success = ![transitionContext transitionWasCancelled];

                         // After a failed presentation or successful dismissal, remove the view.
                         if ((self.presenting && !success) || (!self.presenting && success)) {
                             [toView removeFromSuperview];
                         }

                         // Notify UIKit that the transition has finished
                         [transitionContext completeTransition:success];
                     }];

}
```

##### 动画后清理

在过渡动画结束时，调用`completeTransition：`方法至关重要。调用该方法会告诉UIKit过渡已完成，并且用户可能会开始使用所呈现的视图控制器。调用该方法还会触发其他完成处理程序的级联，包括`presentViewController：animated：completion：`方法和animator对象自己的`animationEnded：`方法。调用completeTransition：方法的最佳位置在动画块的完成处理程序中。

因为可以取消转换，所以应该使用上下文对象的transitionWasCancelled方法的返回值来确定需要进行的清理。当呈现被取消时，动画器必须撤消对视图层次结构所做的任何修改。成功的解除需要采取类似的行动。

### 为过渡添加互动性（Interactivity）

使动画可交互的最简单方法是使用`UIPercentDrivenInteractiveTransition`对象。`UIPercentDrivenInteractiveTransition`对象与您现有的动画器对象配合使用，以控制动画制作的时间。它使用您提供的完成百分比值执行此操作。您只需设置计算完成百分比值所需的事件处理代码，并在每个新事件到达时更新它。

您可以使用带有或不带有子类的`UIPercentDrivenInteractiveTransition`类。如果您是子类，则使用子类的`init`方法（或`startInteractiveTransition：`方法）执行事件处理代码的一次性设置。之后，使用您的自定义事件处理代码来计算新的完成百分比值并调用`updateInteractiveTransition：`方法。当您的代码确定完成转换时，请调用`finishInteractiveTransition`方法。

清单10-3显示了`UIPercentDrivenInteractiveTransition`子类的`startInteractiveTransition：`方法的自定义实现。此方法设置了一个手势识别器（pan-gesture recognizer）来跟踪触摸事件，并在动画的容器视图上安装该手势识别器。它还保存对转换上下文的引用以供以后使用。

清单10-3 配置一个百分比驱动的交互式动画器

```
- (void)startInteractiveTransition:(id<UIViewControllerContextTransitioning>)transitionContext {
   // Always call super first.
   [super startInteractiveTransition:transitionContext];

   // Save the transition context for future reference.
   self.contextData = transitionContext;

   // Create a pan gesture recognizer to monitor events.
   self.panGesture = [[UIPanGestureRecognizer alloc]
                        initWithTarget:self action:@selector(handleSwipeUpdate:)];
   self.panGesture.maximumNumberOfTouches = 1;

   // Add the gesture recognizer to the container view.
   UIView* container = [transitionContext containerView];
   [container addGestureRecognizer:self.panGesture];
}
```

手势识别器为每个到达的新事件调用其操作方法。操作方法的实现可以使用手势识别器的状态信息来确定手势是成功，失败还是仍在进行中。同时，您可以使用最新的触摸事件信息来计算手势的新百分比值。

清单10-4显示了清单10-3中配置的平移手势识别器调用的方法。当新事件到达时，此方法使用垂直行进距离来计算动画的完成百分比。当手势结束时，该方法完成过渡。

清单10-4使用事件更新动画进度

```
-(void)handleSwipeUpdate:(UIGestureRecognizer *)gestureRecognizer {
    UIView* container = [self.contextData containerView];

    if (gestureRecognizer.state == UIGestureRecognizerStateBegan) {
        // Reset the translation value at the beginning of the gesture.
        [self.panGesture setTranslation:CGPointMake(0, 0) inView:container];
    }
    else if (gestureRecognizer.state == UIGestureRecognizerStateChanged) {
        // Get the current translation value.
        CGPoint translation = [self.panGesture translationInView:container];

        // Compute how far the gesture has travelled vertically,
        //  relative to the height of the container view.
        CGFloat percentage = fabs(translation.y / CGRectGetHeight(container.bounds));

        // Use the translation value to update the interactive animator.
        [self updateInteractiveTransition:percentage];
    }
    else if (gestureRecognizer.state >= UIGestureRecognizerStateEnded) {
        // Finish the transition and remove the gesture recognizer.
        [self finishInteractiveTransition];
        [[self.contextData containerView] removeGestureRecognizer:self.panGesture];
    }
}
```

> **注意**
>
> 你计算的值代表整个动画长度的完成百分比。对于交互式动画，您可能需要避免非线性效应，例如动画本身的初始速度，阻尼值和非线性完成曲线。这种影响倾向于将事件的触摸位置与任何底层视图的移动分开。

### 创建与过渡同时运行的动画

参与过渡的视图控制器可以在任何呈现或过渡动画之上执行其他动画。例如，被呈现的（presented）视图控制器可以在过渡期间对其自己的视图层次结构进行动画处理，并在过渡发生时添加运动效果或其他视觉反馈。任何对象都可以创建动画，只要它能够访问被呈现（presented）或呈现（presenting）的视图控制器的`transitionCoordinator`属性即可。过渡协调器只在过渡过程中才存在。

要创建动画，请调用过渡协调器的`animateAlongsideTransition：completion：`或`animateAlongsideTransitionInView：animation：completion：`方法。您提供的块将存储直到过渡动画开始，此时它们将与其余的过渡动画一起执行。

### 在动画中使用呈现控制器

对于自定义呈现，您可以提供自己的呈现控制器，为呈现视图控制器提供自定义外观。呈现控制器管理与视图控制器及其内容分离的任何自定义镶边。例如，放置在视图控制器视图后面的调光视图将由呈现控制器管理。它不管理特定视图控制器视图的事实意味着您可以在您的应用中使用与任何视图控制器相同的呈现控制器。

您可以从呈现的视图控制器的过渡代理中提供自定义呈现控制器。视图控制器的`modalTransitionStyle`属性必须是`UIModalPresentationCustom`。）呈现控制器与任何动画器对象并行运行。随着动画器对象将视图控制器的视图制作成动画，呈现控制器将任何其他视图动画化。在过渡结束时，呈现控制器有机会对视图层次结构执行任何最终调整。

###### 参考资料

1. [Customizing the Transition Animations](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/CustomizingtheTransitionAnimations.html#//apple_ref/doc/uid/TP40007457-CH16-SW1)



