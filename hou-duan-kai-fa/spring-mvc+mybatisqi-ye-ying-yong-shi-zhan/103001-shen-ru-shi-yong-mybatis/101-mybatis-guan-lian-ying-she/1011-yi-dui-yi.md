# 10.1.1 一对一

关系。 一对一 关系 推荐 使用 唯一 主 外 键 关联， 即 两张 表 使用 外 键 关联 关系， 由于 是 一对一 关联， 因此 还需 要给 外 键 列 增加 unique 唯一 约束。

示例：OneToOneTest

创建 两个 表 tb\_ card 和 tb\_ person:

![](/assets/创建两个表tb_card和tb_person.png)

> 提示
>
> tb\_ person 表 的 card\_ id 作为 外 键 参照 tb\_ card 表 的 主 键 id， 因为 是 一对一 关系， 即 一个 card 只能 让 一个 person 使用， 所以 把 card\_ id 做成 了 唯一 键 约束。 如此 一来， 当 一个 person 使用 了 一个 card 之后， 其他 的 person 就不能 使用 该 card 了。





