# 10.5.1 一级缓存（SqlSession级别）

MyBatis 的 一级 缓存 是 SqlSession 级别 的 缓存。 在 操作 数据库 时 需要 构造 SqlSession 对象， 在 SqlSession 对象 中有 一个 HashMap 用于 存储 缓存 数据。 不同 的 SqlSession 之间 的 缓存 数据 区域（ HashMap） 是 互相 不 影响 的。

一级 缓存 的 作用域 是 SqlSession 范围 的， 当 在 同一个 SqlSession 中 执行 两次 相同 的 SQL 语句 时， 第一次 执行 完毕 会 将从 数据库 中 查询 的 数据 写到 缓存（ 内存）， 第二次 查询 时会 从 缓存 中 获取 数据， 不再 去 底层 数据库 查询， 从而 提高 查询 效率。 需要 注意 的 是， 如果 SqlSession 执行 了 DML 操作（ insert、 update 和 delete）， 并提 交到 数据库， MyBatis 则 会 清空 SqlSession 中的 一级 缓存， 这样做 的 目的 是 为了 保证 缓存 中 存储 的 是最 新的 信息， 避免 出现 脏 读 现象。

当 一个 SqlSession 结束 后 该 SqlSession 中的 一级 缓存 也就 不存在 了。 Mybatis 默认 开启 一级 缓存， 不需要 进行 任何 配置。





