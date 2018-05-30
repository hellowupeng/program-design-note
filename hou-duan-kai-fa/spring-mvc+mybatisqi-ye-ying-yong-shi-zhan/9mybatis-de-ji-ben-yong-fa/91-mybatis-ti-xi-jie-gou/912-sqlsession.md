# 9.1.2 SqlSession

SqlSession 是 MyBatis 的 关键 对象， 它是 执行 持久 化 操作 的 对象， 类似于 JDBC 中的 Connection。它是 应用 程序 与 持久 存储 层 之间 执行 交互 操作 的 一个 单线 程 对象， 也是 MyBatis 执行 持久 化 操作 的 关键 对象。SqlSession 对象 完全 包含 以 数据库 为 背景 的 所有 执行 SQL 操作 的 方法， 它的 底层 封装 了 JDBC 连接， 可 以用 SqlSession 实例 来 直接 执行 已 映射 的 SQL 语句。

每个 线程 都应 该有 它自己 的 SqlSession 实例。 SqlSession 的 实例 不能 被 共享， 也是 线程 不安全 的， 绝对 不 能将 SqlSession 实例 的 引用 放在 一个 类 的 静态 字段 甚至 是 实例 字段 中。 也 绝不 能将 SqlSession 实例 的 引用 放在 任何 类型 的 管理 范围 中， 比如 Serlvet 当中 的 HttpSession 对象 中。 使 用完 SqlSession 之后 关闭 Session 很重 要， 应该 确保 使用 finally 块 来 关闭 它。

> 提示
>
> 在 实际 应用 中 很少 会 直接 使用 DriverManager 来 获取 数据库 连接， 通常 都会 使用 DataSource 来 获取 数据库 连接， SqlSessionFactory 底层 封装 了 DataSource。





