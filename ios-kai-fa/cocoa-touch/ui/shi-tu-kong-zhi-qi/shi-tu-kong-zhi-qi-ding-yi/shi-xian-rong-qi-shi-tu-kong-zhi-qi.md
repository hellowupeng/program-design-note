# 实现容器视图控制器

容器视图控制器是一种将来自多个视图控制器的内容组合到一个用户界面中的方法。容器视图控制器通常用于促进导航并基于现有内容创建新的用户界面类型。UIKit中容器视图控制器的示例包括UINavigationController，UITabBarController和UISplitViewController，所有这些都有助于在用户界面的不同部分之间进行导航。

### 设计自定义容器视图控制器

容器视图控制器始终像其他任何内容视图控制器一样管理根视图和一些内容。区别在于容器视图控制器从其他视图控制器获取部分内容。它获得的内容仅限于其他视图控制器的视图，嵌入在其自己的视图层次结构中。容器视图控制器设置任何嵌入视图的大小和位置，但原始视图控制器仍然管理这些视图内的内容。

在设计自己的容器视图控制器时，始终要理解容器和包含的视图控制器之间的关系。视图控制器的关系可以帮助告知他们的内容应该如何显示在屏幕上以及您的容器如何在内部管理它们。在设计过程中，问问自己以下问题：

* 容器的作用是什么？它的子视图控制器扮演什么角色？

* 同时显示多少个子视图控制器？

* 同级视图控制器之间的关系（如果有的话）是什么？

* 子视图控制器如何添加到容器或从容器中移除？

* 子视图控制器的大小或位置可以改变吗？这些变化发生在什么条件下？

* 容器是否提供任何装饰或导航相关的视图？

* 容器和子视图控制器之间需要什么样的通信？容器是否需要向UIViewController类定义的标准事件以外的其他子视图控制器报告特定的事件？

* 容器的外观是否可以用不同的方式配置？如果是这样，怎么样做？

在定义了各种对象的角色后，容器视图控制器的实现相对比较简单。UIKit的唯一要求是您在容器视图控制器和任何子视图控制器之间建立正式的父子关系。父子关系确保子视图控制器们收到任何相关的系统消息。除此之外，大多数真正的工作都发生在包含视图的布局和管理过程中，对于每个容器来说都是不同的。您可以将视图放置在容器的内容区域的任何位置，然后根据需要调整这些视图的大小。您还可以将自定义视图添加到视图层次结构中，以提供修饰或辅助导航。

##### 示例：导航控制器

`UINavigationController`对象支持通过分层数据集进行导航。导航界面一次显示一个子视图控制器。界面顶部的导航栏显示数据层次结构中的当前位置，并显示后退按钮以向后移动一个级别。向下导航到数据层次结构留给子视图控制器，并可能涉及使用表视图或按钮。

视图控制器之间的导航由导航控制器及其子视图控制器共同管理。当用户与子视图控制器的按钮或表格行进行交互时，孩子会要求导航控制器将新的视图控制器推入视图。子视图控制器处理新视图控制器内容的配置，但导航控制器管理过渡动画。导航控制器还管理导航栏，该导航栏显示用于解除最顶层视图控制器的后退按钮。

图5-1显示了导航控制器及其视图的结构。大多数内容区域由最顶层的子视图控制器填充，导航栏只占用一小部分内容区域。

图5-1 导航界面的结构![](/assets/Structure of a navigation interface.png)

在紧凑和常规环境中，导航控制器一次只显示一个子视图控制器。导航控制器调整其子视图控制器以适应可用空间。

##### 示例：分割视图控制器

`UISplitViewController`对象以主 - 细节（master-detail）布置显示两个视图控制器的内容。在这种安排中，一个视图控制器（主视图控制器）的内容决定了其他视图控制器显示的细节。两个视图控制器的可见性是可配置的，但也受当前环境的支配。在规则的水平环境中，分割视图控制器可以同时显示两个子视图控制器，也可以隐藏主视图控制器并根据需要显示。在紧凑的环境中，分割视图控制器一次只显示一个视图控制器。

分割视图控制器本身默认只有其容器视图。在示例中，两个子视图并排显示。

图5-2分割视图界面![](/assets/A split view interface.png)

### 在界面构建器中配置容器

要在设计时创建父子容器关系，请将容器视图对象添加到故事板场景（storyboard scene）中，如图5-3所示。容器视图对象是一个代表子视图控制器内容的占位符对象。使用该视图根据容器中的其他视图调整和定位子视图控制器的根视图。

图5-3 在Interface Builder中添加一个容器视图![](/assets/Adding a container view in Interface Builder.png)当您使用一个或多个容器视图加载视图控制器时，Interface Builder 还会加载与这些视图关联的子视图控制器。子视图控制器必须与父视图控制器同时实例化，以便可以创建适当的父子关系。

如果您不使用Interface Builder来设置父 - 子容器关系，则必须以编程方式通过将每个子项添加到容器视图控制器来创建这些关系。

### 实现自定义容器视图控制器

要实现容器视图控制器，您必须在您的视图控制器及其子视图控制器之间建立关系。在尝试管理任何子视图控制器的视图之前，建立这些父子关系是必需的。这样做可以让UIKit知道您的视图控制器正在管理孩子的大小和位置。您可以在Interface Builder中创建这些关系或以编程方式创建它们。以编程方式创建父子关系时，您明确地添加和删除子视图控制器作为视图控制器设置的一部分。

##### 将子视图控制器添加到您的内容

要以编程方式将子视图控制器合并到内容中，请执行以下操作在相关视图控制器之间创建父 - 子关系：

1. 调用容器视图控制器的`addChildViewController：`方法。

   此方法告诉UIKit，您的容器视图控制器现在正在管理子视图控制器的视图。

2. 将孩子的根视图添加到容器的视图层次结构中。

   永远记得设置子视图控制器frame的大小和位置，作为这个过程的一部分。

3. 添加任何约束条件来管理子视图的大小和位置。

4. 调用子视图控制器的`didMoveToParentViewController：`方法。

清单5-1显示了一个容器如何在其容器中嵌入一个子视图控制器。建立父子关系后，容器设置其子视图控制器的frame并将子视图添加到其自己的视图层次结构中。设置子视图控制器视图的frame大小很重要，并确保视图在容器中正确显示。在添加视图之后，容器调用子视图控制器的`didMoveToParentViewController：`方法，以使子视图控制器有机会响应视图所有权的更改。

清单5-1 将一个子视图控制器添加到一个容器：

```
- (void) displayContentController: (UIViewController*) content {
   [self addChildViewController:content];
   content.view.frame = [self frameForContentController];
   [self.view addSubview:self.currentClientView];
   [content didMoveToParentViewController:self];
}
```

在前面的示例中，请注意您只调用子视图控制器的`didMoveToParentViewController：`方法。这是因为`addChildViewController：`方法会为您调用孩子的`willMoveToParentViewController：`方法。您必须自己调用`didMoveToParentViewController：`方法的原因是，只有在将子视图嵌入容器的视图层次结构后，方法才能被调用。

使用自动布局时，在将子视图控制器添加到容器的视图层次结构后，在容器和子视图控制器之间设置约束。您的约束条件只会影响孩子的根视图的大小和位置。请勿更改子视图层次结构中根视图或任何其他视图的内容。

##### 删除子视图控制器

要从内容中删除子视图控制器，请通过执行以下操作来删除视图控制器之间的父子关系：

1. 用 `nil` 值调用子视图控制器`willMoveToParentViewController：`方法。
2. 移除子视图控制器的根视图配置的所有约束。
3. 从容器的视图层次结构中移除子视图控制器的根视图。

4. 调用子视图控制器的`removeFromParentViewController`来结束父子关系。

删除子视图控制器将永久切断容器与子视图控制器之间的关系。仅当您不再需要引用子视图控制器时，才可以删除它。例如，当新导航控制器被推入导航堆栈时，导航控制器不会移除其当前的子视图控制器。只有当它们从堆栈中弹出时才会删除它们。

清单5-2显示了如何从其容器中删除子视图控制器。用 `nil` 值调用`willMoveToParentViewController：`方法为子视图控制器提供了一个准备更改的机会。`removeFromParentViewController`方法也调用子视图控制器的`didMoveToParentViewController：`方法，传递该方法的值为`nil`。将父视图控制器设置为 `nil` 将最终从容器中删除子视图控制器的视图。

清单5-2 从容器中移除一个子视图控制器

```
- (void) hideContentController: (UIViewController*) content {
   [content willMoveToParentViewController:nil];
   [content.view removeFromSuperview];
   [content removeFromParentViewController];
}
```

##### 在子视图控制器之间切换（Transitioning）

当您想用另一个视图控制器动画替换一个子视图控制器时，将子视图控制器的添加和删除合并到过渡动画过程中。在动画之前，请确保两个子视图控制器都是您的内容的一部分，但让当前的子视图控制器知道它即将消失。在您的动画中，将新的子视图控制器的视图移动到位并移除旧的子视图控制器的视图。完成动画后，完成子视图控制器的移除。

清单5-3显示了如何使用过渡动画将一个子视图控制器过渡为另一个子视图控制器的示例。在这个例子中，新的视图控制器被动画为现有的子视图控制器当前占用的矩形，该控制器被移出屏幕。动画完成后，完成块从容器中移除子视图控制器。在这个例子中，`transitionFromViewController：toViewController：duration：options：animations：completion：`方法自动更新容器的视图层次结构，所以你不需要自己添加和移除视图。

清单5-3 两个子视图控制器之间的转换

```
- (void)cycleFromViewController: (UIViewController*) oldVC
               toViewController: (UIViewController*) newVC {
   // Prepare the two view controllers for the change.
   [oldVC willMoveToParentViewController:nil];
   [self addChildViewController:newVC];

   // Get the start frame of the new view controller and the end frame
   // for the old view controller. Both rectangles are offscreen.
   newVC.view.frame = [self newViewStartFrame];
   CGRect endFrame = [self oldViewEndFrame];

   // Queue up the transition animation.
   [self transitionFromViewController: oldVC toViewController: newVC
        duration: 0.25 options:0
        animations:^{
            // Animate the views to their final positions.
            newVC.view.frame = oldVC.view.frame;
            oldVC.view.frame = endFrame;
        }
        completion:^(BOOL finished) {
           // Remove the old view controller and send the final
           // notification to the new view controller.
           [oldVC removeFromParentViewController];
           [newVC didMoveToParentViewController:self];
        }];
}
```

##### 管理子视图控制器的外观更新

在将子视图控制器添加到容器后，容器会自动将外观相关的消息转发给子视图控制器。这通常是您想要的行为，因为它确保所有事件都能正确发送。但是，有时默认行为可能会以对您的容器无意义的顺序发送这些事件。例如，如果多个孩子同时更改其视图状态，则可能需要合并这些更改，以使外观回调都以更合理的顺序同时发生。

要接管外观回调的责任，请覆盖容器视图控制器中的`shouldAutomaticallyForwardAppearanceMethods`方法，并返回`NO`，如清单5-4所示。返回NO让UIKit知道你的容器视图控制器通知其子视图控制器的外观变化。

清单5-4 禁用自动外观转发

```
- (BOOL) shouldAutomaticallyForwardAppearanceMethods {
    return NO;
}
```

当出现外观转换时，根据需要调用孩子的`beginAppearanceTransition：animated：`或`endAppearanceTransition`方法。例如，如果您的容器有一个由`child`属性引用的单个子视图控制器，那么您的容器会将这些消息转发给子视图控制器，如清单5-5所示。

清单5-5 当容器出现或消失时转发外观消息

```
-(void) viewWillAppear:(BOOL)animated {
    [self.child beginAppearanceTransition: YES animated: animated];
}

-(void) viewDidAppear:(BOOL)animated {
    [self.child endAppearanceTransition];
}

-(void) viewWillDisappear:(BOOL)animated {
    [self.child beginAppearanceTransition: NO animated: animated];
}

-(void) viewDidDisappear:(BOOL)animated {
    [self.child endAppearanceTransition];
}
```

### 建立一个容器视图控制器的建议

设计，开发和测试新的容器视图控制器需要时间。在实现自己的容器类时考虑以下技巧：

* **只访问子视图控制器的根视图。**一个容器只应该访问每个子视图控制器的根视图，也就是子视图控制器的`view`属性返回的视图。它不应该访问任何孩子的其他视图。
* **子视图控制器应该对其容器有最少的了解。**子视图控制器应该关注自己的内容。如果容器允许其行为受到孩子的影响，则应使用委托设计模式来管理这些交互。

* **首先使用常规视图设计您的容器。**使用常规视图（而不是来自子视图控制器的视图）使您有机会在简化的环境中测试布局约束和动画过渡。当常规视图按预期工作时，将它们交换为子视图控制器的视图。

### 将控制委派给子视图控制器

容器视图控制器可以将其自身外观的某些方面委托给其一个或多个子视图控制器。您可以通过以下方式委派控制权：

* **让一个子视图控制器确定状态栏的样式。**要将状态栏外观委托给子视图控制器，请覆盖容器视图控制器中的一个或两个`childViewControllerForStatusBarStyle`和`childViewControllerForStatusBarHidden`方法。

* **让子视图控制器指定自己喜欢的尺寸**。具有灵活布局的容器可以使用孩子自己的`preferredContentSize`属性来帮助确定孩子的大小。



