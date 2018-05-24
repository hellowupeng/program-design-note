# 呈现视图控制器

有两种方式可以在屏幕上显示视图控制器：将其嵌入到容器视图控制器中或呈现它。容器视图控制器提供了一个应用程序的主要导航，但呈现视图控制器也是一个重要的导航工具。您可以使用直接呈现在当前显示器上显示新的视图控制器。通常，当你想要实现模态界面时，你需要呈现视图控制器，但是你也可以将它们用于其他目的。

对呈现视图控制器的支持内置在`UIViewController`类中，并可用于所有视图控制器对象。您可以从任何其他视图控制器呈现任何视图控制器，但UIKit可能会将请求重新路由到不同的视图控制器。呈现视图控制器会创建原始视图控制器（称为呈现视图控制器）和要显示的新视图控制器（称为被呈现视图控制器）之间的关系。这种关系构成了视图控制器层次结构的一部分，并保持原样，直到视图控制器被解除为止。

### 呈现和过渡过程

呈现视图控制器是将新内容动画地展示到屏幕上的一种快捷方式。内置于UIKit中的呈现机制允许您使用内置或自定义动画显示新的视图控制器。内置的呈现和动画只需要很少的代码，因为UIKit可以处理所有的工作。你还可以创建自定义的呈现和动画并和任何视图控制器一起使用它们。

你可以通过编程或使用 segues 来初始化视图控制器的呈现。如果你在设计时了解应用导航，segues 则是发起呈现最简单的方式。对于更动态的接口，或者在没有专门的控件来启动segue的情况下，使用UIViewController的方法来呈现您的视图控制器。

##### 呈现样式

在设计应用程序时，请选择最适合您尝试执行的呈现样式，然后将相应的常量分配给要呈现的视图控制器的`modalPresentationStyle`属性。

###### 全屏呈现样式

全屏呈现样式覆盖整个屏幕，防止与底层内容的交互。在横向常规环境中，只有一种全屏样式完全覆盖底层内容。在水平紧凑的环境中，全屏呈现会自动适应`UIModalPresentationFullScreen`样式并覆盖所有底层内容。

图8-1说明了在水平常规环境中使用`UIModalPresentationFullScreen`，`UIModalPresentationPageSheet`和`U​​IModalPresentationFormSheet`样式进行呈现的外观。

图8-1 全屏演示样式![](/assets/The full screen presentation styles.png)

> 注意：
>
> 当使用`UIModalPresentationFullScreen`样式呈现视图控制器时，UIKit通常会在转换动画完成后删除底层视图控制器的视图。您可以通过指定`UIModalPresentationOverFullScreen`样式来防止删除这些视图。

###### Popover样式

`UIModalPresentationPopover`样式在弹窗视图中显示视图控制器。弹出窗口对于显示附加信息或与聚焦或选定对象相关的项目列表非常有用。在横向常规环境中，弹窗视图仅覆盖屏幕的一部分，如图8-2所示。在水平紧凑的环境中，默认情况下，弹出窗口采用`UIModalPresentationOverFullScreen`呈现样式。在弹窗视图外点击会自动关闭弹出窗口。

图8-2 popover呈现风格

![](/assets/The popover presentation style.png)

由于popover在水平紧凑环境采用全屏呈现样式，你通常需要修改popover代码来处理这种适应。在全屏模式下，您需要一种方法来解除呈现的弹出窗口。您可以通过添加按钮，将弹出窗口嵌入容器视图控制器中，或者改变适配行为本身来实现。

###### 当前上下文样式

`UIModalPresentationCurrentContext`样式覆盖了界面中的特定视图控制器。使用上下文样式时，通过将其`defineDesentationContext`属性设置为`YES`来指定要覆盖哪个视图控制器。图8-3说明了当前上下文呈现，其中仅包含分割视图控制器的一个子视图控制器。

图8-3 当前上下文呈现样式![](/assets/The current context presentation style.png)定义呈现上下文的视图控制器也可以在呈现期间定义要使用的过渡动画。通常，UIKit使用呈现的视图控制器的`modalTransitionStyle`属性中的值为屏幕上的视图控制器设置动画。如果表示上下文视图控制器的`providePresentationContextTransitionStyle`设置为`YES`，则UIKit将使用该视图控制器的`modalTransitionStyle`属性中的值。

###### 自定义呈现样式

`UIModalPresentationCustom`样式可让您使用您定义的自定义样式呈现视图控制器。创建自定义样式涉及到`UIPresentationController`的子类并使用其方法来将任何自定义视图动画到屏幕上，并设置被呈现的视图控制器的大小和位置。

##### 过渡样式（Transition Styles）

过渡样式确定用于显示呈现视图控制器的动画类型。对于内置的过渡样式，可以将其中一种标准过渡样式分配给要呈现的视图控制器的`modalTransitionStyle`属性。当您呈现视图控制器时，UIKit将创建与该样式相对应的动画。例如，图8-4说明了标准的滑动过渡（`UIModalTransitionStyleCoverVertical`）如何为屏幕上的视图控制器生成动画。当视图控制器B被解散时，动画反转，以便B滑落以显示A.

图8-4 视图控制器的过渡动画![](/assets/A transition animation for a view controller.png)您可以使用动画制作者（animator）对象和过渡委托（transitioning delegate）来创建自定义过渡。动画制作者（animator）对象创建用于将视图控制器置于屏幕上的过渡动画。过渡委托（transitioning delegate）在恰当的时间将该动画器对象提供给UIKit。

##### 呈现与显示视图控制器

`UIViewController`类提供了两种显示视图控制器的方法：

* `showViewController：sender：`和`showDetailViewController：sender：`方法为显示视图控制器提供了最自适应且最灵活的方式。这些方法让呈现视图控制器决定如何最好地处理呈现。例如，容器视图控制器可能会将视图控制器作为一个子视图控制器而不是以模态方式呈现。默认行为以模态方式呈现视图控制器。

* `presentViewController：animated：completion：`方法总是以模态方式显示视图控制器。调用此方法的视图控制器可能不会最终处理呈现，但呈现始终为模态。此方法适用于水平紧凑环境的呈现样式。

`showViewController：sender：`和`showDetailViewController：sender：`方法是发起呈现的首选方式。视图控制器可以在不知道视图控制器层次结构的其余部分或当前视图控制器在该层次结构中的位置的情况下调用它们。这些方法还可以更轻松地在应用程序的不同部分重用使用视图控制器，而无需编写条件代码路径。

### 呈现视图控制器

有几种方法可以启动视图控制器的呈现：

* 使用segue自动呈现视图控制器。segue使用您在Interface Builder中指定的信息实例化并呈现视图控制器。

* 使用`showViewController：sender：`或`showDetailViewController：sender：`方法显示视图控制器。在自定义视图控制器中，您可以将这些方法的行为更改为更适合您的视图控制器的行为。

* 调用`presentViewController：animated：completion：`方法以模态方式呈现视图控制器。

##### 显示视图控制器

当使用`showViewController：sender：`和`showDetailViewController：sender：`方法时，在屏幕上获取新视图控制器的过程很简单：

1. 创建您想要呈现的视图控制器对象。在创建视图控制器时，您有责任使用任何需要执行其任务的数据对其进行初始化。

2. 将新视图控制器的`modalPresentationStyle`属性设置为偏好的呈现样式。这种样式可能不会用在最终的呈现样式中。

3. 将视图控制器的`modalTransitionStyle`属性设置为所需的过渡动画样式。这种样式可能不会用在最终的动画中。

4. 调用当前视图控制器的`showViewController：sender：`和`showDetailViewController：sender：`方法。

UIKit将调用`showViewController：sender：`和`showDetailViewController：sender：`方法转发给相应的呈现视图控制器。该视图控制器可以决定如何最好地执行呈现，并可根据需要更改呈现和过渡样式。例如，导航控制器可能会将视图控制器推到其导航堆栈上。

##### 以模态方式呈现视图控制器

当直接呈现视图控制器时，您可以告诉UIKit如何显示新的视图控制器以及如何在屏幕上显示动画。

1. 创建您想要呈现的视图控制器对象。

   在创建视图控制器时，您有责任使用任何需要执行其任务的数据对其进行初始化。

2. 将新视图控制器的`modalPresentationStyle`属性设置为所需的呈现样式。

3. 将视图控制器的`modalTransitionStyle`属性设置为所需的动画样式。

4. 调用当前视图控制器的`presentViewController：animated：completion：`方法。

调用`presentViewController：animated：completion：`方法的视图控制器可能不是实际执行模态呈现的视图控制器。呈现样式决定了如何呈现视图控制器，包括呈现视图控制器所需的特性。例如，全屏呈现必须由全屏视图控制器启动。如果当前呈现的视图控制器不合适，UIKit将遍历视图控制器层次结构，直到找到一个为止。模态呈现完成后，UIKit将更新受影响的视图控制器的`presentingViewController`和`presentedViewController`属性。

清单8-1演示了如何以编程方式呈现视图控制器。当用户添加新食谱（recipe）时，应用程序会通过显示导航控制器来提示用户有关食谱的基本信息。导航控制器被选中，这样就会有一个标准的放置取消和完成按钮的地方。使用导航控制器还可以使未来扩展新食谱界面变得更加容易。您只需在导航堆栈上推送新的视图控制器即可。

清单8-1 以编程方式呈现视图控制器

```
- (void)add:(id)sender {
   // Create the root view controller for the navigation controller
   // The new view controller configures a Cancel and Done button for the
   // navigation bar.
   RecipeAddViewController *addController = [[RecipeAddViewController alloc] init];

   addController.modalPresentationStyle = UIModalPresentationFullScreen;
   addController.transitionStyle = UIModalTransitionStyleCoverVertical;
   [self presentViewController:addController animated:YES completion: nil];
}
```

##### 用popover呈现视图控制器

在展示它们之前，Popovers需要额外的配置。将模态呈现样式设置为`UIModalPresentationPopover`后，配置以下与popover式相关的属性：

* 将视图控制器的`preferredContentSize`属性设置为所需的大小。

* 使用关联的`UIPopoverPresentationController`对象设置popover定位点（anchor point），该对象可从视图控制器的`popoverPresentationController`属性访问。只设置下列其中一项：

  * 将`barButtonItem`属性设置为一个bar button item。

  * 将`sourceView`和`sourceRect`属性设置为您的某个视图中的特定区域。

您可以使用UIPopoverPresentationController对象根据需要对popover的外观进行其他调整。popover呈现控制器还支持可用于在呈现过程中响应更改的代理对象。例如，当popover出现，消失或在屏幕上重新定位时，您可以使用委托进行响应。

### 关闭呈现的视图控制器

要关闭一个呈现的视图控制器，请调用呈现（presenting）视图控制器的`dismissViewControllerAnimated：completion：`方法。您也可以在被呈现（presented）的视图控制器本身上调用此方法。当您在被呈现（presented）的视图控制器上调用该方法时，UIKit会自动将该请求转发给呈现（presenting）视图控制器。

解除视图控制器前始终保存任何重要信息。关闭视图控制器会将其从视图控制器层次结构中删除，并从屏幕中删除其视图。如果您没有强烈的引用存储在别处的视图控制器，解除它将释放与其关联的内存。

如果呈现的视图控制器必须将数据返回到呈现视图控制器，请使用委托设计模式来促进传输。委托可以更轻松地在应用程序的不同部分重用视图控制器。通过委托，被呈现（presented）的视图控制器存储对实现正式协议中方法的委托对象的引用。当它收集结果时，被呈现（presented）的视图控制器会在其委托上调用这些方法。在典型的实现中，呈现（presenting）视图控制器使其自己成为其呈现（presented）的视图控制器的代理。

### 呈现定义在不同故事板（Storyboard）中的视图控制器

尽管您可以在同一个故事板中的视图控制器之间创建segues，但不能在故事板之间创建segues。当你想显示一个存储在不同故事板中的视图控制器时，你必须在显示它之前明确地实例化该视图控制器，如清单8-2所示。该示例以模态方式呈现视图控制器，但您可以将其推到导航控制器上或以其他方式显示。

清单8-2 从故事板载入视图控制器

```
UIStoryboard* sb = [UIStoryboard storyboardWithName:@"SecondStoryboard" bundle:nil];
MyViewController* myVC = [sb instantiateViewControllerWithIdentifier:@"MyViewController"];

// Configure the view controller.

// Display the view controller
[self presentViewController:myVC animated:YES completion:nil];
```



