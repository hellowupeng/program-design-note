# Flutter Widget 

### 介绍

Flutter小部件从React中获得灵感采用了 react-style 框架构建。核心思想是从小部件中构建你的UI。小组件描述了他们的视图在给定其当前配置和状态时应该看起来像什么。当小部件的状态发生变化时，小部件会重新构建它的描述，该描述与前面的描述不同，以确定底层渲染树从一个状态转换到下一个状态所需的最小更改。

`runApp` 函数使用给定的 Widget 并使其成为 Widget 树的根。

### 基础小部件（widgets）

* Text: `Text` 小部件可让您在应用程序中创建一段样式文本。
* Row, Column: 这些 flex 小部件可让您在水平（Row）和垂直（Column）方向上创建灵活的布局。

* Stack: `Stack` 小部件可让你按部件的顺序堆叠小部件，而不是线性定位（水平或垂直）。然后，你可以在堆栈的子项上使用 `Positioned` 小部件，以相对于堆栈的顶部，右侧，底部或左侧边缘定位它们。Stack 基于 Web 的绝对定位布局模型。

* Container:  `Container` 小部件允许您创建矩形视觉元素。`Container` 可以由一个 `BoxDecoration` 装饰，比如背景，边框或阴影。`Container` 也可以有应用于其大小外边距（margin），内边距（padding）和约束（constraint）。另外，Container可以使用矩阵在三维空间中转换。

### State

StatefulWidget 的逻辑和内部状态。

状态可以在构建小部件时同步读取，并且可以在小部件的生命周期期间改变。小部件实施者有责任确保使用 State.setState 在状态（state）发生更改时及时通知状态。

状态（State）对象由框架通过调用 StatefulWidget.createState 方法创建，方法是在将StatefulWidget 插入树中时调用它。

状态（State）对象具有以下生命周期：  


### Keys

###  

  




