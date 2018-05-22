# 手势识别器

手势识别器将低级事件处理代码转换为更高级别的操作。 他们是附加到视图的对象，允许视图按照控件的方式响应操作。手势识别器解释触摸以确定它们是否与某个特定的手势相对应，例如滑动（swipe），捏（pinch），或回转（rotation）。如果他们识别出他们分配的手势，他们会向目标对象发送操作消息。 目标对象通常是视图的视图控制器，它响应图1-1所示的手势。这种设计

模式既强大又简单; 您可以动态地确定视图响应的操作和您可以将手势识别器添加到视图中，而无需对视图进行子类化。

图1-1 连接到视图的手势识别器

![](/assets/A gesture recognizer attached to a view.png)

### 使用手势识别器简化事件处理

UIKit框架提供了检测常见手势的预定义手势识别器。 在可能的情况下最好使用预定义手势识别器，因为它们的简单性会减少你要写的代码量。另外，使用标准手势识别器而不是自己编写可确保您的应用按用户期望的方式行事。

如果您希望自己的应用能够识别唯一的手势，例如选中标记或旋转运动，则可以创建您自己的自定义手势识别器。

##### 内置手势识别器识别常见手势

在设计您的应用程序时，请考虑要启用的手势。 然后，对于每个手势，确定表1-1中列出的预定义手势识别器是否足够。

表1-1 UIKit框架的手势识别器类:

* 点击（任意数量的点击）
  UITapGestureRecognizer

* 挤入和挤出（Pinching in and out ）（用于缩放视图）

  UIPinchGestureRecognizer

* 平移或拖动

  UIPanGestureRecognizer

* 轻扫（任何方向）

  UISwipeGestureRecognizer

* 旋转（手指向相反方向移动）

  UIRotationGestureRecognizer

* 长按（也称为“触摸并按住”）

  UILongPressGestureRecognizer

您的应用应该以用户期望的方式响应手势。例如，捏合应该放大和而点击应该选择一些东西。

##### 手势识别器连接到视图

每个手势识别器都与一个视图相关联。相比之下，一个视图可以有多个手势识别器，因为单个视图可能会响应许多不同的手势。当用户触摸视图，手势识别器在视图对象之前接收发生触摸的消息。 结果是，手势识别器可以代表视图对触摸做出响应。

##### 手势触发操作消息

当手势识别器识别其指定的手势时，它会向其目标发送操作消息。要创建一个手势识别器，你用一个目标和一个动作初始化它。

###### 离散和连续手势

手势不是离散的就是连续的。一个离散的手势，比如一次点击，会出现一次。 连续的手势，如捏，发生一段时间。对于离散手势，手势识别器发送其目标一条单一的动作消息。用于连续手势的手势识别器不断向其发送操作消息

直到多点触控序列结束，如图1-2所示。

图1-2 离散和连续手势：

![](/assets/Discrete and continuous gestures.png)

### 用手势识别器响应事件

您需要做三件事才能将内置手势识别器添加到您的应用中：

1. 创建并配置一个手势识别器实例。
   这一步包括分配一个目标，动作，有时分配特定于手势的属性（例如
   `numberOfTapsRequired`）。
2. 将手势识别器附加到视图。
3. 实现处理手势的操作方法。

##### 使用界面生成器将手势识别器添加到您的应用程序

在Xcode的Interface Builder中，以与添加任何对象到您的用户界面相同的方式将手势识别器添加到您的应用程序中

 - 将手势识别器从对象库拖动到视图。当你这样做时，手势识别器会自动附加到该视图。您可以检查您的手势识别器附加到的视图，并且如有必要，更改nib文件中的连接。

创建手势识别器对象后，您需要创建并连接一个操作方法。这种方法每当连接的手势识别器识别出手势时调用。如果你需要引用在此操作方法之外的手势识别器，您还应该创建并连接手势识别器的出口（outlets）。

清单1-1使用Interface Builder将手势识别器添加到您的应用程序：

```
@interface APLGestureRecognizerViewController ()
@property (nonatomic, strong) IBOutlet UITapGestureRecognizer *tapRecognizer;
@end

@implementation

- (IBAction)displayGestureForTapRecognizer:(UITapGestureRecognizer *)recognizer
// Will implement method later...
}

@end
```

##### 以编程方式添加手势识别器

您可以通过分配和初始化具体实例来以编程方式创建手势识别器`UIGestureRecognizer`子类，如`UIPinchGestureRecognizer`。初始化手势识别器时，请指定一个目标对象和一个动作选择器，如清单1-2所示。 通常，目标对象是视图的视图控制器。

如果以编程方式创建手势识别器，则需要使用该手势将其附加到视图`addGestureRecognizer：`方法。清单1-2创建一个轻击手势识别器，指定一个轻击需要识别的手势，然后将手势识别器对象附加到视图。通常情况下，

您在视图控制器的`viewDidLoad`方法中创建一个手势识别器，如清单1-2所示。

清单1-2 以编程方式创建一个轻击手势识别器:

```
- (void)viewDidLoad {
    [super viewDidLoad];// Create and initialize a tap gesture
    
    UITapGestureRecognizer *tapRecognizer = [[UITapGestureRecognizer alloc]
    initWithTarget:self action:@selector(respondToTapGesture:)];
    
    // Specify that the gesture must be a single tap
    tapRecognizer.numberOfTapsRequired = 1;
    
    // Add the tap gesture recognizer to the view
    [self.view addGestureRecognizer:tapRecognizer];
    
    // Do any additional setup after loading the view, typically from a nib
}
```

##### 响应离散手势

当您创建手势识别器时，您将识别器连接到一个操作方法。清单1-3提供了一个响应离散手势的例子。当用户点击手势识别器所附的视图时，视图控制器将显示表示“点击”的图片视图。showGestureForTapRecognizer：方法从识别器的 locationInView 属性确定手势的位置并在该位置上显示图片。

清单1-3 处理双击手势：

```
- (IBAction)showGestureForTapRecognizer:(UITapGestureRecognizer *)recognizer {
    // Get the location of the gesture
    CGPoint location = [recognizer locationInView:self.view];
    
    // Display an image view at that location
    [self drawImageForGestureRecognizer:recognizer atPoint:location];
    
    // Animate the image view so that it fades out
    [UIView animateWithDuration:0.5 animations:^{
        self.imageView.alpha = 0.0;
    }];
}
```

每个手势识别器都有自己的一组属性。例如，在清单1-4中，showGestureForSwipeRecognizer：方法使用滑动手势识别器的direction属性确定用户是向左还是向右滑动。然后，它使用该值使图像淡入与滑动方向相同。

清单1-4 响应左手或右手轻扫手势：

```
// Respond to a swipe gesture
- (IBAction)showGestureForSwipeRecognizer:(UISwipeGestureRecognizer *)recognizer
{
    // Get the location of the gesture
    CGPoint location = [recognizer locationInView:self.view];
    
    // Display an image view at that location
    [self drawImageForGestureRecognizer:recognizer atPoint:location];
    
    // If gesture is a left swipe, specify an end location
    // to the left of the current location
    if (recognizer.direction == UISwipeGestureRecognizerDirectionLeft) {
        location.x -= 220.0;
    } else {
        location.x += 220.0;
    }
    
    // Animate the image view in the direction of the swipe as it fades out
    [UIView animateWithDuration:0.5 animations:^{
        self.imageView.alpha = 0.0;
        self.imageView.center = location;
    }];
}
```

##### 响应连续手势



### 定义手势识别器如何相互作用

### 手势识别器解释原始触摸事件

### 调整触摸到视图的传递

### 创建自定义手势识别器



