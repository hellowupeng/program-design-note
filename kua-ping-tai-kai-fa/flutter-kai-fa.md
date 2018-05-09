# Flutter 开发

### Flutter 的布局方式

* 小部件是用于构建 UI 的类。

* 小部件用于布局和 UI 元素。

* 写简单的部件来构建复杂的部件。

Flutter 的布局机制的核心是小部件。在 Flutter 中，几乎所有东西都是一个小部件 - 甚至包括布局模型（layout models）都是小部件。

Container是一个小部件，允许您自定义其子部件。如果要添加 padding，margins，borders 或background color，使用容器（Container）来命名其功能。

列（Column）和行（Row）的属性允许您指定他们的孩子（children，子部件）如何垂直或水平对齐，以及子部件应该占据多少空间。

