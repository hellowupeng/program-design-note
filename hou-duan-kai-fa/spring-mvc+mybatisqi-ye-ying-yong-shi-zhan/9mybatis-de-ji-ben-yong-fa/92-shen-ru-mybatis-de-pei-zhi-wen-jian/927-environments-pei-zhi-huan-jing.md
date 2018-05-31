# 9.2.7 environments 配置环境

MyBatis 的 环境 配置 实际 就是 数据 源 的 配置。 MyBatis 可以 配置 多种 环境， 这种 机制 使得 MyBatis 可以 将 SQL 映射 应用于 多种 数据库 中。 例如， 开发、 测试 和 生产 环境 需要 有 不同 的 配置； 多个 生产 数据库 想 使用 相同 的 SQL 映射 等等。

> 提示
>
> 尽管 可以 配置 多个 环境， 但是 每个 SqlSessionFactory 实例 只能 选择 一个 环境， 即 每个 数据库 对应 一个 SqlSessionFactory 实例。 所以， 如果 你想 连接 两个 数据库， 就 需要 创建 两个 SqlSessionFactory 实例， 每个 数据库 对应 一个。 而 如果 是 三个 数据库， 就 需要 三个 实例， 依此类推。

环境配置：![](/assets/环境配置.png)关键点：

默认 的 环境 id（ 比如， default=＂ development＂）。 每个 environment 元素 定义 的 环境 id（ 比如， id=＂ development＂）。 事务 管理器 的 配置（ 比如， type=＂ JDBC＂）。 数据 源 的 配置（ 比如， type=＂ POOLED＂）。 环境 id 可以 随意 命名， 建议 简洁 有意义， 而 默认 环境 一定 要 匹配 定义 的 其中 一个 环境 id。

＜ transactionManager.../ ＞ 表示 事务管理 器 配置， 在 MyBatis 中有 JDBC 和 MANAGED 两种 类型 的 事务 管理器：

* JDBC：这个 配置 直接 使用 了 JDBC 的 提交 和 回 滚 设置， 它 依赖于 从 数据 源 得到 的 连接 来 管理 事务 范围。
* MANAGED：这个 配置 几乎 没 做 什么。 它 从来不 提交 或 回 滚 一个 连接， 而是 让 容器 来 管理 事务 的 整个 生命 周期（ 比如 Java EE 应用 服务器 的 上下文）。 默认 情况下 它 会 关闭 连接， 然而 一些 容器 并不 希望 这样， 可以 将 closeConnection 属性 设置 为 false 来 阻止 它 默认 的 关闭 行为。

如果 开发者 使用 Spring+ MyBatis， 则 没有 必要 配置 事务 管理器， 因为 Spring 模块 会使 用 自带 的 管理器 来 覆盖 MyBatis 的 事务管理 器 配置。

＜ dataSource.../ ＞ 表示 数据 源 配置， 在 MyBatis 中有 UNPOOLED、 POOLED 和 JNDI 三种 数据 源 类型：

* **UNPOOLED**。这个 数据 源 的 实现 只是 每次 被 请求 时 打开 和 关闭 连接。 它对 没有性能 要求 的 简单 应用 程序 是一 个 很好 的 选择。 不同 的 数据库 在这方面 表现 也是 不一样 的。 UNPOOLED 类型 的 数据 源 仅仅 需要 配置 以下 5 种 属性：
  * **driver**。这是 JDBC 驱动 的 Java 类 的 完全 限定 名（ 并不是 JDBC 驱动 中 可能 包含 的 数据 源 类）。
  * **url**。这是 数据库 的 JDBC URL 地址。
  * **username**。登录 数据库 的 用 户名。
  * **password**。登录 数据库 的 密码。
  * defaultTransactionIslationLevel。默认 的 连接 事务 隔离 级别。
* **POOLED**。这种 数据 源 的 实现 利用“ 池” 的 概念 将 JDBC 连接 对象 组织 起来， 避免 了 创建 新的 连接 实例 时 所 必需 的 初始化 和 认证 时间。 这是 一种 使得 并发 Web 应用 快速 响应 请求 的 流行 处理 方式。 除了 上述 提到 的 UNPOOLED 的 5 种 属性 外， 还可以 使用 更多 属性 来 配置 POOLED 的 数据 源：
  * **poolMaximumActiveConnections**。在任 意 时间 可以 存在 的 活动（ 也就是 正在 使用） 连接 数量， 默认值 是 10。
  * poolMaximumIdleConnections。
  * poolMaximumCheckoutTime。
  * poolTimeToWait。
  * poolPingQuery。
  * poolPingEnabled。
  * poolPingConnectionsNotUsedFor。
* **JNDI**。这个 数据 源 的 实现 是 为了 能在 如 EJB 或 应用 服务器 这类 容器 中 使用， 容器 可以 集中 或在 外部 配置 数据 源， 然后 放置 一个 JNDI 上下文 的 引用。 这种 数据 源 配置 只需 要 两个 属性：
  * initial\_context。
  * data\_source。



