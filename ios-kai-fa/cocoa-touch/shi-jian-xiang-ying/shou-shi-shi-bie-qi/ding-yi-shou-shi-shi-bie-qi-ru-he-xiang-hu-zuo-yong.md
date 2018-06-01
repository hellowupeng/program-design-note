# 定义手势识别器如何交互

通常，当您将手势识别器添加到应用程序时，您需要具体说明您希望识别器如何与应用程序中的对方或任何其他触摸事件处理代码进行交互。

### 手势识别器在有限状态机中操作

手势识别器以预定义的方式从一个状态转换到另一个状态。从每个状态，他们都可以根据他们是否符合某些条件，进入几个可能的状态之一。确切的状态机根据手势识别器是离散还是连续而变化，如图1-3所示。所有手势识别器都以可能状态（UIGestureRecognizerStatePossible）开始。他们分析他们接收到的任何多点触控序列，并且在分析过程中他们识别或无法识别手势。未识别手势意味着手势识别器转换到失败状态（`UIGestureRecognizerStateFailed`）。

图1-3手势识别器的状态机

![](/assets/手势识别器的状态机.png)

![](/assets/手势识别器的状态机2.png)

当离散手势识别器识别其手势时，手势识别器从可能转换到识别（`UIGestureRecognizerStateRecognized`）并且识别完成。

对于连续的手势，当手势第一次被识别时，手势识别器从可能转换到开始（`UIGestureRecognizerStateBegan`）。然后，它从开始切换到已更改（`UIGestureRecognizerStateChanged`），并在手势发生时继续从已更改更改为已更改。当用户的最后一根手指从视图中抬起时，手势识别器转换到结束状态（`UIGestureRecognizerStateEnded`），识别完成。请注意，Ended状态是Recognized状态的别名。

如果确定手势不再符合预期模式，则连续手势的识别器也可以从已更改转换为取消（`UIGestureRecognizerStateCancelled`）。

每次手势识别器改变状态时，手势识别器都会向其目标发送操作消息，除非其转换为失败或取消。因此，离散手势识别器在从可能识别转换为可识别时仅发送单个动作消息。连续的手势识别器在改变状态时发送许多动作消息。

当手势识别器达到识别（或结束）状态时，它将其状态重置为可能。转换回可能不会触发操作消息。

### 与其他手势识别器交互

一个视图可以附加多个手势识别器。使用视图的`gestureRecognizers`属性来确定附加到视图的手势识别器。您还可以分别使用`addGestureRecognizer：`和`removeGestureRecognizer：`方法，通过从视图中添加或删除手势识别器来动态更改视图处理手势的方式。

当一个视图有多个手势识别器附加到它时，您可能想要改变竞争手势识别器接收和分析触摸事件的方式。默认情况下，手势识别器首先接收触摸时没有设置的顺序，因此触摸可以每次以不同的顺序传递给手势识别器。您可以覆盖此默认行为：

* 指定一个手势识别器应该在另一个手势识别器之前分析触摸。
* 允许两个手势识别器同时操作。
* 防止手势识别器分析触摸。

使用`UIGestureRecognizer`类方法，委托方法和子类覆盖的方法来实现这些行为。

###### 为两个手势识别器声明特定的顺序

设想你想要识别一个滑动和平移手势，并且你想让这两个手势触发不同的动作。默认情况下，当用户尝试滑动时，手势将被解释为平移。这是因为一个滑动手势符合必要的条件，在它满足必要的条件以解释为滑动（离散手势）之前，将其解释为平移（连续手势）。

为了使视图识别滑动和平移，您希望滑动手势识别器在平移手势识别器执行之前分析触摸事件。如果滑动手势识别器确定触摸是轻扫，则平移手势识别器从不需要分析触摸。如果滑动手势识别器确定触摸不是轻扫，则移动到失败状态，并且平移手势识别器应该开始分析触摸事件。

您可以通过调用手势识别器上的requireGestureRecognizerToFail：方法来指示两个手势识别器之间的这种类型的关系，如代码1-6所示。在此列表中，两个手势识别器都附加到相同的视图。

清单1-6 平移手势识别器需要滑动手势识别器才能失败

```
- (void)viewDidLoad {
       [super viewDidLoad];
}
// Do any additional setup after loading the view, typically from a nib
[self.panRecognizer requireGestureRecognizerToFail:self.swipeRecognizer];
```

`requireGestureRecognizerToFail：`方法向接收方发送一条消息，并指定一些必须在接收识别器可以开始之前失败的其他`GestureRecognizer`。在等待另一手势识别器转换到失败状态时，接收识别器保持在可能状态。如果其他手势识别器失败，则接收识别器分析触摸事件并移至其下一个状态。另一方面，如果另一手势识别器转变为识别或开始，则接收识别器移动到失败状态。

> 注意：如果你的应用识别单击、双击并且你的单击手势识别器不需要双击手势识别器失败，在双击动作之前你应该希望接收单动作，即使用户双击了。
>
> 如果您希望这两个操作相互排斥，您的单击识别器必须要求双击识别器识别失败。但是，您的单击操作会稍微落后于用户的输入，因为单击识别器会延迟到双击识别器识别失败。

###### 防止手势识别器分析触摸

您可以通过将代理对象添加到手势识别器来更改手势识别器的行为。`UIGestureRecognizerDelegate`协议提供了几种方法，可以防止手势识别器分析触摸。您可以使用`gestureRecognizer：shouldReceiveTouch：`方法或`gestureRecognizerShouldBegin：`方法 - 它们都是`UIGestureRecognizerDelegate`协议的可选方法。

触摸开始时，如果您可以立即确定您的手势识别器是否应考虑该触摸，请使用`gestureRecognizer：shouldReceiveTouch：`方法。每次有新的触摸时都会调用此方法。返回“NO”防止手势识别器被通知发生了触摸。默认值是YES。此方法不会更改手势识别器的状态。

清单1-7使用`gestureRecognizer：shouldReceiveTouch：`delegate方法来防止点击手势识别器接收自定义子视图内的触摸。触摸发生时，将调用`gestureRecognizer：shouldReceiveTouch：`方法。它确定用户是否触摸了自定义视图，并且如果是，则防止轻击手势识别器接收触摸事件。

列表1-7 防止手势识别器接收触摸

```
- (void)viewDidLoad {
    [super viewDidLoad];
    // Add the delegate to the tap gesture recognizer
    self.tapGestureRecognizer.delegate = self;
}

// Implement the UIGestureRecognizerDelegate method
-(BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer
shouldReceiveTouch:(UITouch *)touch {
    // Determine if the touch is inside the custom subview
    if ([touch view] == self.customSubview)){
        // If it is, prevent all of the delegate's gesture recognizers
        // from receiving the touch
        return NO;
}
return YES; }
```

如果您需要尽可能长时间地确定手势识别器是否应分析触摸，请使用`gestureRecognizerShouldBegin：`delegate方法。通常，如果您有一个具有自定义触摸事件处理功能的UIView或UIControl子类与手势识别器竞争，则可以使用此方法。返回“NO”会导致手势识别器立即失败，从而允许进行其他触摸处理。如果手势识别会阻止视图或控件接收触摸，则手势识别器尝试转换到可能状态时会调用此方法。

如果您的视图或视图控制器不能成为手势识别器的代理，则可以使用UIView 的 `gestureRecognizerShouldBegin：`方法。这个方法签名和实现是相同的。

###### 允许同时识别手势

默认情况下，两个手势识别器不能同时识别其各自的手势。但是，例如，假设您希望用户能够同时捏和旋转视图。您需要通过实现`gestureRecognizer:shouldRecognizeSimultaneouslyWithGestureRecognizer：`方法来更改默认行为，这是`UIGestureRecognizerDelegate`协议的可选方法。当一个手势识别器对手势的分析会阻止另一个手势识别器识别其手势时调用此方法，反之亦然。此方法默认返回NO。 当您希望两个手势识别器同时分析他们的手势时，返回YES。

> 注意：您需要实现一个委托并仅在您的一个手势识别器上返回YES以允许同时识别。但是，这也意味着返回NO不一定会阻止同时识别，因为其他手势识别器的代表可能会返回YES。

###### 指定两个手势识别器之间的单向关系

如果要控制两个识别器如何相互交互，但需要指定单向关系，则可以覆盖`canPreventGestureRecognizer：`或`canBePreventedByGestureRecognizer：`子类方法以返回NO（默认值为YES）。例如，如果您想要旋转手势来防止捏合手势，但您不想捏合手势来阻止旋转手势，则可以指定：

```
[rotationGestureRecognizer canPreventGestureRecognizer:pinchGestureRecognizer];
```

并重写旋转手势识别器的子类方法以返回NO。如果两个手势都不应该阻止其他手势，请使用`gestureRecognizer：shouldRecognizeSimultaneouslyWithGestureRecognizer：`

默认情况下，捏合手势会阻止旋转，反之亦然，因为无法同时识别两个手势。

### 与其他用户界面控件交互

在iOS 6.0及更高版本中，默认控件操作可防止重叠的手势识别器行为。例如，按钮的默认操作是单击。如果您将一个轻击手势识别器附加到按钮的父视图，并且用户点击该按钮，则按钮的操作方法将接收触摸事件而不是手势识别器。这仅适用于与控件的默认操作重叠的手势识别，其中包括：

* UIButton，UISwitch，UIStepper，UISegmentedControl和UIPageControl上的单指单击。
* 用一根手指在与滑块平行的方向上滑动UISlider的旋钮。
* UISwitch旋钮上的一个手指平移手势，平行于开关。

如果您有这些控件之一的自定义子类，并且您想要更改默认操作，请将手势识别器直接附加到控件而不是父视图。然后，手势识别器首先接收触摸事件。



