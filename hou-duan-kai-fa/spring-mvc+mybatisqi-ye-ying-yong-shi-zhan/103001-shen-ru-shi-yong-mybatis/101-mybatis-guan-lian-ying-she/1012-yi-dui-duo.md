# 10.1.2 一对多

在 数据库 中 一对多 关系 通常 使用 主 外 键 关联， 外 键 列 应该 在 多方， 即 多方 维护 关系。

### 示例：oneToManyTest

给 之前 创建 的 mybatis 数据库 创建 两个 表 tb\_ clazz 和 tb\_ student， 并 插入 测试 数据。

![](/assets/创建tb_clazz和tb_student.png)

> 提示
>
> tb\_ student 表 的 clazz\_ id 作为 外 键 参照 tb\_ clazz 表 的 主 键 id。

创建 一个 Clazz 对象 和 一个 Student 对象 分别 映射 tb\_ clazz 和 tb\_ student 表。





