# 9.1.1 SqlSessionFactory

SqlSessionFactory 是 MyBatis 的 关键 对象， 它是 单个 数据库 映射 关系 经过 编译 后的 内存 镜像。SqlSessionFactory 对象 的 实例 可以 通过 SqlSessionFactoryBuilder 对象 来 获得， 而 SqlSessionFactoryBuilder 则 可以 从 XML 配置文件 或 一个 预先 定制 的 Configuration 的 实例 构建 出 SqlSessionFactory 的 实例。

每一个 MyBatis 的 应用 程序 都以 一个 SqlSessionFactory 对象 的 实例 为 核心。 其 也是 线程 安全 的， SqlSessionFactory 一旦 被 创建， 应该 在 应用 执行 期间 都存 在。 在 应用 运行 期间 不需要 重复 创建 多次， 建议 使用 单 例 模式。 SqlSessionFactory 是 创建 SqlSession 的 工厂。



