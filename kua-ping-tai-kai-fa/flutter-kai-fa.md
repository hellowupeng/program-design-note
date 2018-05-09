# Flutter 开发

### Flutter 的布局方式

* 小部件是用于构建 UI 的类。

* 小部件用于布局和 UI 元素。

* 写简单的部件来构建复杂的部件。

Flutter 的布局机制的核心是小部件。在 Flutter 中，几乎所有东西都是一个小部件 - 甚至包括布局模型（layout models）都是小部件。

Container是一个小部件，允许您自定义其子部件。如果要添加 padding，margins，borders 或background color，使用容器（Container）来命名其功能。

列（Column）和行（Row）的属性允许您指定他们的孩子（children，子部件）如何垂直或水平对齐，以及子部件应该占据多少空间。

### 布局小部件

* 应用程序本身也是一个小部件。

* 创建一个小部件并将其添加到布局小部件很容易。

* 要在设备上显示小部件，将布局小部件添加到应用小部件。

* 使用 Scaffold 是很容易的，它是 Material Components 库中的一个小部件，它提供了一个默认横幅（banner），背景颜色（background color），并且具有添加抽屉（drawer），snack bar 和底部表单（bottom sheet）的API。

* 如果您愿意，可以构建仅使用小部件库中的标准小部件的应用程序。

### 垂直和水平布局多个小部件

* 行（Row）和（Column）列是两种最常用的布局模式。

* 行（Row）和（Column）列分别携带多个子窗口小部件。

* 子部件本身可以是行（Row），列（Column）或其他复杂小部件。

* 您可以指定行或列如何在垂直和水平方向上对齐其子项。

* 您可以拉伸（stretch）或约束（constrain）特定的子部件。

* 您可以指定子部件如何使用行或列的可用空间。

###### 参考资料

1. [Building Layouts in Flutter](https://flutter.io/tutorials/layout/#lay-out-a-widget)



