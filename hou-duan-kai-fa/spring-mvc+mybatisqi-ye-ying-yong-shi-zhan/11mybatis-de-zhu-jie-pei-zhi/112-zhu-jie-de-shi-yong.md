# 11.2 注解的使用

@select、@ insert、@ update 和@ delete 可以 完成 常见 的 CRUD（ 增 删改 查） SQL 语句 映射。

```
@Insert("INSERT INTO TB_USER(name,sex,age) VALUES(#{name},#{sex},#{age})")
@Options(useGeneratedKeys=true,keyProperty="id")
int saveUser(User user);
```

调用 saveUser 方法， 会 执行@ insert 注解 中的 SQL 语句。 需要 注意 的 是， saveUser 方法 还 使用 了@ Options 注解， 属性 useGeneratedKeys= true 表示 使用 数据库 自动 增 长的 主 键， 该 操作 需要 底层 数据库 的 支持。 keyProperty=＂ id＂ 表示 将 插入 数据 生成 的 主 键 设置 到 user 对象 的 id 当中。



