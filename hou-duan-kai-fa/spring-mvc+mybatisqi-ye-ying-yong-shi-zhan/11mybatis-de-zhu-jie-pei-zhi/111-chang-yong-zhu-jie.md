# 11.1 常用注解

MyBatis 的 注解 位于 org. apache. ibatis. annotations 包 下。 常用 的 注解

**Select**。 映射 查询 的 SQL 语句。

**SelectProvider**。 Select 语句 的 动态 SQL 映射。 允许 指定 一个 类 名 和 一个 方法 在 执行 时 返回 运行 的 查询 语句。 有两 个 属性： type 和 method， type 属性 是 类 的 完全 限定 名， method 是 该类 中的 方 法名。

**Insert**。 映射 插入 的 SQL 语句。

**InsertProvider**。 Insert 语句 的 动态 SQL 映射。 允许 指定 一个 类 名 和 一个 方法 在 执行 时 返回 运行 的 插入 语句。 有两 个 属性： type 和 method， type 属性 是 类 的 完全 限定 名， method 是 该类 中的 方 法名。

**Update**。 映射 更新 的 SQL 语句。

**UpdateProvider**。 Update 语句 的 动态 SQL 映射。 允许 指定 一个 类 名 和 一个 方法 在 执行 时 返回 运行 的 更新 语句。 有两 个 属性： type 和 method， type 属性 是 类 的 完全 限定 名， method 是 该类 中的 方 法名。

**Delete**。 映射 删除 的 SQL 语句。

**DeleteProvider**。 Delete 语句 的 动态 SQL 映射。 允许 指定 一个 类 名 和 一个 方法在 执行 时 返回 运行 的 删除 语句。 有两 个 属性： type 和 method， type 属性 是 类 的 完全 限定 名， method 是 该类 中的 方 法名。

**Result**。 在 列 和 属性 之间 的 单独 结果 映射。 属性 包括： id、 column、 property、 javaType、 jdbcType、 type Handler、 one、 many。 id 属性 是一 个 布尔 值， 表示 是否 被 用于 主 键 映射。 one 属性 是 单独 的 映射， 和 XML 配置 中的 ＜ association ＞ 相似， 而 many 属性 是对 集合 而言 的， 和 XML 配置 的 ＜ collection ＞ 相似。

**Results**。 多个 结果 映射（ Result） 列表。

**Options**。 提供 配置 选项 的 附加 值， 它们 通常 在 映射 语句 上作 为 附加 功能 配置 出现。

**One**。 复杂 类型 的 单独 属性 值 映射。 必须 指定 select 属性， 表示 已 映射 的 SQL 语句 的 完全 限定 名。

**Many**。 复杂 类型 的 集合 属性 映射。 必须 指定 select 属性， 表示 已 映射 的 SQL 语句 的 完全 限定 名。

**Param**。 当 映射 器 方法 需要 多个 参数 时， 这个 注解 可以 被 应用于 映射 器 方法 参数 来给 每个 参数 取 一个 名字。 否则， 多 参数 将会 以 它们 的 顺序 位置 和 SQL 语句 中的 表达式 进行 映射， 这是 默认 的。 使用@ Param（＂ id＂） 时， SQL 中 参数 应该 被 命名为＃{ id}。



