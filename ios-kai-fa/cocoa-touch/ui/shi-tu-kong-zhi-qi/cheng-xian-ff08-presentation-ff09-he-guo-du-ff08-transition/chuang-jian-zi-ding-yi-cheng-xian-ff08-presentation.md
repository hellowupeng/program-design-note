# 创建自定义呈现（Presentation）

UIKit将视图控制器的内容与内容在屏幕上呈现和显示的方式分开。呈现的视图控制器由底层的呈现控制器对象管理，该对象管理用于显示视图控制器视图的视觉样式。呈现控制器可以执行以下操作：

* 设置被呈现（presented）的视图控制器的大小。

* 添加自定义视图以更改所呈现（presented）内容的视觉外观。

* 为其任何自定义视图提供过渡动画。

* 当应用程序环境发生变化时，调整呈现的外观。

UIKit为标准呈现样式提供了呈现控制器。当您将视图控制器的呈现样式设置为`UIModalPresentationCustom`并提供合适的过渡代理时，UIKit会改为使用您的自定义呈现控制器。

### 自定义呈现过程

当呈现其呈现样式为`UIModalPresentationCustom`的视图控制器时，UIKit会查找自定义呈现控制器来管理呈现过程。随着呈现的进行，UIKit会调用呈现控制器的方法，使其有机会设置任何自定义视图并将它们动画地设置到位。

呈现控制器与任何动画器（animator）对象一起工作来实现整体过渡。动画器将视图控制器的内容动画化到屏幕上，而呈现控制器处理所有其他事情。通常，您的呈现控制器会为其自己的视图创建动画，但您也可以覆盖呈现控制器的`presentedView`方法，并让动画器（animator）对象为所有或部分视图设置动画效果。

在呈现期间，UIKit：

1. 调用过渡代理的`presentationControllerForPresentedViewController：presentingViewController：sourceViewController：`方法来检索自定义呈现控制器。

2. 询问过渡代理动画器和交互式动画器，如果有的话。

3. 调用呈现控制器的`presentationTransitionWillBegin`方法

   此方法的实现应该将任何自定义视图添加到视图层次结构中，并为这些视图配置动画。

4. 从您的呈现控制器中获取已呈现的视图

   该方法返回的视图由动画器对象动画定位（is animated into position by the animator objects.）。通常，此方法返回被呈现（presented）的视图控制器的根视图。您的呈现控制器可以根据需要使用自定义背景视图替换该视图。如果您确实指定了不同的视图，则必须将呈现的视图控制器的根视图嵌入视图层次结构中。

5. 执行过渡动画

   过渡动画包括动画器（animator）对象创建的主要动画以及您配置为与主要动画一起运行的任何动画。

   在动画过程中，UIKit会调用呈现控制器的`containerViewWillLayoutSubviews`和`containerViewDidLayoutSubviews`方法，以便您可以根据需要调整自定义视图的布局。

6. 过渡动画结束时调用`presentationTransitionDidEnd：`方法

在解除期间，UIKit：

1. 从当前可见的视图控制器中获取您的自定义呈现控制器

2. 询问过渡代理动画器和交互式动画器，如果有的话。

3. 调用呈现控制器的`dismissalTransitionWillBegin`方法

   此方法的实现应该将任何自定义视图添加到视图层次结构中，并为这些视图配置动画。

4. 从您的呈现控制器中获取 `presentedView`。

5. 执行过渡动画

   过渡动画包括动画器对象创建的主要动画以及你配置为与主要动画一起运行的任何动画。

   在动画过程中，UIKit会调用演示文稿控制器的`containerViewWillLayoutSubviews`和`containerViewDidLayoutSubviews`方法，以便删除任何自定义约束。

6. 过渡动画结束时调用`dismissalTransitionDidEnd：`方法

在呈现过程中，呈现控制器的`frameOfPresentedViewInContainerView`和`presentedView`方法可能会多次调用，因此您的实现应该很快返回。另外，您的`presentedView`方法的实现不应该尝试设置视图层次结构。视图层次结构应该已经在调用方法的时候进行配置。

### 创建自定义呈现控制器

要实现自定义呈现样式，请创建`UIPresentationController`子类并添加代码以为呈现创建视图和动画。创建自定义呈现控制器时，请考虑以下问题：

* 你想添加什么视图？

* 你想如何在屏幕上制作任何其他视图的动画效果？

* 呈现的视图控制器应该是多大？

* 呈现应该如何在横向正常和横向紧凑尺寸类之间进行适应？

* 呈现完成后，应否移除呈现视图控制器的视图？

所有这些决定都要求覆盖`UIPresentationController`类的不同方法。

##### 设置被呈现（Presented）的视图控制器的frame

您可以修改被呈现（presented）的视图控制器的frame矩形，使其仅填充部分可用空间。默认情况下，被呈现（presented）的视图控制器的大小可以完全填充容器视图的 frame。要更改frame矩形，请覆盖您的呈现控制器的`frameOfPresentedViewInContainerView`方法。清单11-1显示了一个示例，其中frame被更改为仅覆盖容器视图的右半部分。在这种情况下，呈现控制器使用背景调光视图来覆盖容器的另一半。

清单11-1 改变一个被呈现（presented）的视图控制器的frame

```
- (CGRect)frameOfPresentedViewInContainerView {
    CGRect presentedViewFrame = CGRectZero;
    CGRect containerBounds = [[self containerView] bounds];
 
    presentedViewFrame.size = CGSizeMake(floorf(containerBounds.size.width / 2.0),
                                         containerBounds.size.height);
    presentedViewFrame.origin.x = containerBounds.size.width -
                                    presentedViewFrame.size.width;
    return presentedViewFrame;
}
```

##### 管理和动画自定义视图

自定义呈现通常涉及向被呈现（presented）的内容添加自定义视图。使用自定义视图来实现纯粹的视觉装饰，或使用它们将实际行为添加到呈现中。例如，背景视图可能包含手势识别器来跟踪呈现内容边界之外的特定操作。

呈现控制器负责创建和管理与其呈现相关的所有自定义视图。通常，您在呈现控制器的初始化过程中创建自定义视图。清单11-2显示了创建自己的调光视图的自定义视图控制器的初始化方法。此方法创建视图并执行一些最小配置。

清单11-2 初始化呈现控制器

```
- (instancetype)initWithPresentedViewController:(UIViewController *)presentedViewController
                    presentingViewController:(UIViewController *)presentingViewController {
    self = [super initWithPresentedViewController:presentedViewController
                         presentingViewController:presentingViewController];
    if(self) {
        // Create the dimming view and set its initial appearance.
        self.dimmingView = [[UIView alloc] init];
        [self.dimmingView setBackgroundColor:[UIColor colorWithWhite:0.0 alpha:0.4]];
        [self.dimmingView setAlpha:0.0];
    }
    return self;
}
```

使用`presentationTransitionWillBegin`方法将自定义视图动画化到屏幕上。在此方法中，配置您的自定义视图并将它们添加到容器视图中，如清单11-3所示。使用被呈现或呈现视图控制器的过渡协调器来创建任何动画。不要修改此方法中被呈现（presented）的视图控制器的视图。动画器对象负责将呈现的视图控制器动画化为从`frameOfPresentedViewInContainerView`方法返回的frame矩形。

清单11-3 将调光视图移动到屏幕上

```
- (void)presentationTransitionWillBegin {
    // Get critical information about the presentation.
    UIView* containerView = [self containerView];
    UIViewController* presentedViewController = [self presentedViewController];
 
    // Set the dimming view to the size of the container's
    // bounds, and make it transparent initially.
    [[self dimmingView] setFrame:[containerView bounds]];
    [[self dimmingView] setAlpha:0.0];
 
    // Insert the dimming view below everything else.
    [containerView insertSubview:[self dimmingView] atIndex:0];
 
    // Set up the animations for fading in the dimming view.
    if([presentedViewController transitionCoordinator]) {
        [[presentedViewController transitionCoordinator]
               animateAlongsideTransition:^(id<UIViewControllerTransitionCoordinatorContext>
                                            context) {
            // Fade in the dimming view.
            [[self dimmingView] setAlpha:1.0];
        } completion:nil];
    }
    else {
        [[self dimmingView] setAlpha:1.0];
    }
}
```



### 将您的呈现控制器“售卖”给UIKit

### 适应不同的Size Classes

### 



