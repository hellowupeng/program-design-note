# 显示 SnackBar

在某些情况下，当发生某些操作时，可以方便地向用户简单通知。例如，当用户在列表中删除消息时，我们可能想通知他们消息已被删除。我们甚至可能想给他们一个撤消动作的选择。

在Material Design中，这是SnackBar的工作。

### 1.创建一个脚手架（Scaffold）

在创建遵循 Material Design 设计指南的应用程序时，我们希望为我们的应用程序提供一致的可视化结构。在这种情况下，我们需要在屏幕底部显示SnackBar，而不会与其他重要的Widget重叠，例如 `FloatingActionButton`

材料库中的脚手架小工具为我们创建了这个视觉结构，并确保重要的小工具不会重叠！

### 2.显示一个 SnackBar

使用脚手架，我们可以展示 SnackBar！首先，我们需要创建一个SnackBar，然后使用Scaffold显示它。

### 3.提供额外的动作

在某些情况下，我们可能希望在显示 SnackBar 时向用户提供额外的操作。例如，如果他们意外删除了一条消息，我们可以提供一个撤消该更改的操作。

为了达到这个目的，我们可以为 SnackBar Widget 提供额外的操作。




