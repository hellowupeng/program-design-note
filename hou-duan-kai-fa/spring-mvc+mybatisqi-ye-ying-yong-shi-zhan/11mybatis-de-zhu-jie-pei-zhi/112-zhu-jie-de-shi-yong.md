# 11.2 注解的使用

@select、@ insert、@ update 和@ delete 可以 完成 常见 的 CRUD（ 增 删改 查） SQL 语句 映射。

```
@Insert("INSERT INTO TB_USER(name,sex,age) VALUES(#{name},#{sex},#{age})")
@Options(useGeneratedKeys=true,keyProperty="id")
int saveUser(User user);
```



