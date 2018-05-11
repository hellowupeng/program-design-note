# Working with Tabs

使用选项卡是遵循 Material Design 指南的应用程序中的常见模式。

### 1.创建一个TabController

为了让标签生效，我们需要保持所选标签和内容部分的同步。这是TabController的工作。

我们可以手动创建 TabController 或使用 DefaultTabController Widget。使用DefaultTabController 是最简单的选择，因为它会为我们创建一个 TabController 并使其可供所有后代控件使用。

### 2.创建选项卡（tab）

既然我们有一个 TabController 可以使用，我们可以使用 TabBar Widget 创建我们的选项卡。

### 3.为每个选项卡（tab）创建内容

现在我们有了选项卡，我们希望在选中选项卡时显示内容。为此，我们将使用 TabBarView Widget。

