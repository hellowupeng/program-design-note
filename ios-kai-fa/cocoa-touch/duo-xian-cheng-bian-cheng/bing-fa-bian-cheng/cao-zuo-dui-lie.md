# 操作队列

Cocoa 操作（operation）是一种面向对象的方式来封装你想异步执行的工作。操作旨在与操作队列一起使用，或者单独使用。

### 关于操作对象

操作对象是 NSOperation 类（在 Foundation 框架中）的一个实例，用于封装希望应用程序执行的工作。NSOperation 类本身是一个抽象基类，为了做任何有用的工作，它必须被分类。尽管是抽象的，但这个类也提供了大量的基础设施，以尽量减少您在自己的子类中所要完成的工作量。另外，Foundation 框架提供了两个具体的子类，你可以在现有代码里直接使用。

* [NSInvocationOperation](https://developer.apple.com/documentation/foundation/nsinvocationoperation)

基于应用程序中的对象和选择器创建操作对象的类。如果你有一个已经执行所需任务的现有方法，则可以使用此类。因为它不需要子类化，所以还可以使用此类以更动态的方式创建操作对象。

* [NSBlockOperation](https://developer.apple.com/documentation/foundation/nsblockoperation)

能同时执行一个或多个块对象的类。因为它可以执行多个块，所以块操作对象使用组语义进行操作;只有当所有相关的块已经完成执行时，操作本身才算完成。

* [NSOperation](https://developer.apple.com/documentation/foundation/nsoperation)

用于定义自定义操作对象的基类。通过NSOperation的子类化，您可以完全控制自己操作的实现，包括更改操作执行的默认方式并报告其状态的功能。

所有操作对象都支持以下主要功能：

* 支持在操作对象之间建立基于图的依赖关系。这些依赖关系会阻止给定的操作运行，直到它所依赖的所有操作都已完成运行。

* 支持可选的完成块，该块在操作的主任务完成后执行。

* 支持使用 KVO 通知监视对操作执行状态的更改。

* 支持对操作进行优先级排序，从而影响其相对执行顺序。

* 支持取消允许你在执行时暂停操作的语义。

### 并发与非并发操作



### 创建一个 NSInvocationOperation 对象

### 创建一个 NSBlockOperation 对象

### 定义一个自定义操作（Operation）对象

### 定制操作对象的执行行为

### 实现操作对象的技巧

### 确定操作对象的适用范围

### 执行操作





