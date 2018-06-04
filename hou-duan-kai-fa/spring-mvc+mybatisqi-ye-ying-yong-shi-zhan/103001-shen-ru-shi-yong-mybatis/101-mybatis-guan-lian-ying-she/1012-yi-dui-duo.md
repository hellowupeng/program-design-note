# 10.1.2 一对多

在 数据库 中 一对多 关系 通常 使用 主 外 键 关联， 外 键 列 应该 在 多方， 即 多方 维护 关系。

### 示例：oneToManyTest

给 之前 创建 的 mybatis 数据库 创建 两个 表 tb\_ clazz 和 tb\_ student， 并 插入 测试 数据。

![](/assets/创建tb_clazz和tb_student.png)

> 提示
>
> tb\_ student 表 的 clazz\_ id 作为 外 键 参照 tb\_ clazz 表 的 主 键 id。

创建 一个 Clazz 对象 和 一个 Student 对象 分别 映射 tb\_ clazz 和 tb\_ student 表。

Clazz.java

![](/assets/onetomanyclazz.png)

Student.java

![](/assets/onetomanyStudent.png)

ClazzMapper.java

![](/assets/clazzmapper.png)\

ClazzMapper. xml 中 定义 了 一个 ＜ select.../ ＞， 其 根据 id 查询 班级 信息。 由于 Clazz 类 除了 简单 的 属性 id、 code、 name 以外， 还有 一个 关联 对象 students， 所以 返回 的 是一 个 名为 clazzResultMap 的 resultMap。 由于 students 是 一个 List 集合， 所以 clazzResultMap 中 使用 了 ＜ collection.../ ＞ 元素 映射 一对 多的 关联 关系， select 属性 表示 会 使用 column 属性 的 id 值 作为 参数 执行 StudentMapper 中 定义 的 selectStudentByClazzId 查询 该 班级 对应 的 所有 学生 数据， 查询 出 的 数据 将被 封装 到 property 表示 的 students 对象对象 当中。

还 使用 了 一个 新的 属性 fetchType， 该 属性 的 取值 有 eager 和 lazy， eager 表示 立即 加载， 即 查询 Clazz 对象 的 时候， 会 立即 执行 关联 的 selectStudentByClazzId 中 定义 的 SQL 语句 去 查询 班级 的 所有 学生； lazy 表示 懒 加载， 其 不会 立即 发送 SQL 语句 去 查询 班级 的 所有 学生， 而是 等到 需要 使用 到 班级 的 students 属性 时， 才会 发送 SQL 语句 去 查询 班级 的 所有 学生。 fetch 机制 更 多的 是 为了 性能 考虑， 如果 查询 班级 时 确定 会 访问 班级 的 所有 学生， 则 该 属性 应该 被 设置 为 eager； 如果 查询 班级 时 只是 查询 班级 信息， 有可能 不会 访问 班级 的 所有 学生， 则 该 属性 应该 被 设置 为 lazy。 正常 情况下， 一对多 所 关联 的 集合 对象， 都 应该 被 设置 成 lazy。

使用 懒 加载 还需 要在 mybatis- config. xml 中 增加 如下 配置：![](/assets/开启懒加载配置.png)

lazyLoadingEnabled 属性 表示 延迟 加载 的 全局 开关。 当 开启 时， 所有 关联 对象 都会 延迟 加载。 默认 为 false。 aggressiveLazyLoading 属性 启用 时， 会使 带有 延迟 加载 属性 的 对象 立即 加载； 反之， 每种 属性 将会 按 需 加载。 默认 为 true， 所以 这里 需要 设置 成 false。

StudentMapp.java![](/assets/onetomanystudentmapper.png)

