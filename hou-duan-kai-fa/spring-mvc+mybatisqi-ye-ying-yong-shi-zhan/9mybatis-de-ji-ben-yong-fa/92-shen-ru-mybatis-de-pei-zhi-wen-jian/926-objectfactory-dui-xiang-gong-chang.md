# 9.2.6 objectFactory 对象工厂

MyBatis 每次 创建 结果 对象 的 新 实例 时， 它 都会 使用 一个 对象 工厂（ ObjectFactory） 实例 来 完成。

默认 的 对象 工厂 需 要做 的 仅仅是 实例 化 目标 类， 要么 通过 默认 构造 方法， 要么 在 参数 映射 存在 的 时候 通过 参数 构造 方法 来 实例 化。 如果 想 覆盖 对象 工厂 的 默认 行为， 则 可以 通过 创建 自己的 对象 工厂 来 实现。

在 MyBatis 配置文件 中 配置 自定义 对象 工厂：![](/assets/配置自定义对象工厂.png)![](/assets/自定义对象工厂.png)



