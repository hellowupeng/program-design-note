# 8.2.1 MyBatis 下载和安装

示例：MyBatis 的数据库操作入门![](/assets/UserMapperxml.png)代码解释：

1. ＜ mapper namespace=＂ org. fkit. mapper. UserMapper＂ ＞， 为 这个 mapper 指定 一个 唯一 的 namespace， namespace 的 值 习惯上 设置 成包 名+ SQL 映射 文件名， 这样 就能 够 保证 namespace 的 值 是 唯一 的， 例如 namespace=＂ org. fkit. mapper. UserMapper＂ 就是 org. fkit. mapper（ 包 名）+ UserMapper （UserMapper. xml 文件 去除 后缀）。
2. （2） 在 insert 标签 中 编写 了 SQL 插入 语句， 设置 insert 标签 的 id 属性 值 为 save。 id 属性 值 必须 是 唯一 的， 不能 够 重复。
3. 使用 parameterType 属性 指明 插入 时 使用 的 参数 类型。
4. 使用 useGeneratedKeys=＂ true＂ 表示 使用 数据库 的 自动 增长 策略， 这 需要 底层 数据库 的 支持。
5. （5） insert 标签 中 只有 一条 标准 的 insert 语句， 用来 向 TB\_ USER 表 插入 一条 数据，＃{ name} 表示 取 参数 中的 对象 的 name 属性 值。

程序清单：codes/08/MyBatisQs/src/mybatis-config.xml![](/assets/mybatis-config.png)MyBatis 配置文件 是 一个 XML 文件， 该文 件 第 一行 是 XML 文件 声明， 指定 该 XML 文件 的 版本 和 存储 该文 件 所用 的 字符 集。

MyBatis 配置文件 的 根 元素 是 ＜ configuration.../ ＞， 根 元素 中有 ＜ settings.../ ＞ 子 元素， 该 元素 有很 多子 元素， 本 示例 只是 配置 了 日志 信息， 之后 可以 在 控制台 看到 输出 的 SQL 语句， 其 在调 试 中非 常有 用。

根 元素 中 还有 ＜ environments.../ ＞ 子 元素， 用来 配置 MyBatis 的 环境， 即 连接 的 数据库， 该 子 元素 用于 将 SQL 映射 应用于 多种 数据库 中。

每个 数据库 对应 一个 SqlSessionFactory， 可以 配置 多种 环境， 但 只能 为 SqlSessionFactory 实例 选择 一个 环境， default 属性 表示 选择 的 环境。

＜ environment.../ ＞ 子 元素 用于 配置 一个 环境， ＜ transactionManager.../ ＞ 子 元素 用来 配置 MyBatis 当中 的 事务管理， JDBC 属性 表示 直接 简单 使用 JDBC 的 提交 和 回 滚 设置。

＜ dataSource.../ ＞ 子 元素 用来 配置 数据 源， MyBatis 并不 推荐 采用 DriverManager 来 连接 数据库， 而是 推荐 使用 数据 源 来 管理 数据库 连接， 这样 能 保证 最好 的 性能。

该 元素 依次 有 很多 ＜ property.../ ＞ 子 元素， 这些 ＜ property.../ ＞ 子 元素 用于 配置 MyBatis 连接 数据库 的 必要 信息， 如 连接 数据库 的 驱动、 URL、 用 户名、 密码 等 信息。

> 提示
>
> 数据 源 是一 种 用来 提高 数据库 连接 性能 的 常规 手段， 数据 源 会 负责 维持 一个 数据库 连接 池， 当 程序 创建 数据 源 实例 时， 系统 会 一次 性地 创建 多个 数据库 连接， 并把 这些 数据库 连接 保存 在 连接 池 中。 当 程序 需要 进行 数据库 访问 时， 无须 重新 获得 数据库 连接， 而是 从 连接 池 中 取出 一个 空闲 的 数据库 连接， 当 程序 使用 数据库 连接 访问 数据库 结束 后， 无须 关闭 数据库 连接， 而 是将 数据库 连接 归还 给 连接 池 即可。 通过 这种 方式， 就可 避免 频繁 地 获取 数据库 连接、 关闭 数据库 连接 所 导致 的 性能 下降。

根 元素 中 还有 ＜ mappers.../ ＞ 子 元素， 它可 以 支持 多个 ＜ mapper.../ ＞ 子 元素， 每个 ＜ mapper.../ ＞ 子 元素 用于 指定 一个 持久 化 配置文件。![](/assets/MyBatis操作示例.png)

**使用 MyBatis 进行 持久 化 操作， 通常 需要 执行 如下 操作步骤**：

1. 开发 持久 化 类 PO 和 编写 持久 化 操作 的 Mapper. xml， 在其中 定义 要 执行 的 SQL 语句。
2. 获取 SqlSessionFactory。
3. 获取 SqlSession。
4. 用 面向 对象 的 方式 操作 数据库。
5. 关闭 事务， 关闭 SqlSession。

对 PO 的 操作 必须 在 SqlSession 的 管理 下 才能 同步 到 数据库。 SqlSession 由 SqlSessionFactory 工厂 产生， SqlSessionFactory 是 数据库 编译 后的 内存 镜像， 通常 一个 应用 对应 一个 SqlSessionFactory 对象。 SqlSessionFactory 对象 通过 加载 mybatis- config. xml 配置文件 生成。

MyBatis 比较 JDBC 的 两个 显著 优点：

只需 要在 Mapper. xml 配置文件 中 编写 SQL 语句， 在 应用 程序 中就 可以 采用 OO 方式 来 访问 数据库。

在 JDBC 访问 过程中 大量 的 checked 异常 被 包装 成 MyBatis 的 Runtime 异常， 从而 不再 要求 程序 必须 处理 所有 异常。



