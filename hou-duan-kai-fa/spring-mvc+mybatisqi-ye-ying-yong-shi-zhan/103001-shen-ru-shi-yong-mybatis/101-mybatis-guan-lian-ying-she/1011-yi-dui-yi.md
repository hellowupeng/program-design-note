# 10.1.1 一对一

关系。 一对一 关系 推荐 使用 唯一 主 外 键 关联， 即 两张 表 使用 外 键 关联 关系， 由于 是 一对一 关联， 因此 还需 要给 外 键 列 增加 unique 唯一 约束。

示例：OneToOneTest

创建 两个 表 tb\_ card 和 tb\_ person:

![](/assets/创建两个表tb_card和tb_person.png)

> 提示
>
> tb\_ person 表 的 card\_ id 作为 外 键 参照 tb\_ card 表 的 主 键 id， 因为 是 一对一 关系， 即 一个 card 只能 让 一个 person 使用， 所以 把 card\_ id 做成 了 唯一 键 约束。 如此 一来， 当 一个 person 使用 了 一个 card 之后， 其他 的 person 就不能 使用 该 card 了。

接下来， 创建 一个 Card 对象 和 一个 Person 对象 分别 映射 tb\_ card 和 tb\_ peson 表。![](/assets/创建card对象和person对象.png)再 接下来 是 XML 映射 文件。![](/assets/cardmapperxml.png)PersonMapper:

![](/assets/personmapperxml.png)

在 PersonMapper. xml 中 定义 了 一个 ＜ select.../ ＞， 其 根据 id 查询 Peson 信息， 由于 Peson 类 除了 简单 的 属性 id、 name、 sex 和 age 之外， 还有 一个 关联 对象 card， 所以 返回 的 是一 个 名为 personMapper 的 resultMap。 personMapper 中 使用 了 ＜ association.../ ＞ 元素 映射 一对一 的 关联 关系， select 属性 表示 会 使用 column 属性 的 card\_ id 值 作为 参数 执行 CardMapper 中 定义 的 selectCardById 查询 对应 的 Card 数据， 查询 出 的 数据 将被 封装 到 property 表示 的 Card 对象 当中。

之前 的 测试 都是 使用 SqlSession 对象 调用 insert、 update、 delete 和 select 方法 进行 测试。 实际上， Mybatis 官方 手册 建议 通过 mapper 接口 的 代理 对象 访问 Mybatis， 该 对象 关联 了 SqlSession 对象， 开发者 可以 通过 该 对象 直接 调用 方法 操作 数据库。 下面 定义 一个 mapper 接口 对象， 需要 注意 的 是， mapper 接口 对象 的 类 名 必须 和 之前 的 XML 文件 中的 mapper 的 namespace 一致， 而 方 法名 和 参数 也 必须 和 XML 文件 中的 ＜ select.../ ＞ 元素 的 id 属性 和 parameterType 属性 一致。

PersonMapper.java

![](/assets/personmapperjava.png)

最后， 完成 测试 类。

OneToOneTest.java

![](/assets/onetoonetest.png)

