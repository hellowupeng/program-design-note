# 10.3.2 查询数据返回集合

创建 一个 查询 tb\_ user 表 所有 数据 的 存储 过程。

```
# 查询所有数据的存储过程
DROP PROCEDURE IF EXISTS select_user;
DELIMITER //
CREATE PROCEDURE select_user()
BEGIN
SELECT id,NAME,sex,age FROM tb_user;
END
//
```

org.fkit.mapper.UserMapper.xml

```
<select id="selectUser" resultType="org.fkit.domain.User" statementType="CALLABLE">
    {call select_user()}
</select>
```

＜ select…/ ＞ 元素 调用 名为“ select\_ user” 的 存储 过程 查询 所有 User 数据 并 返回 List， 查询 到 的 每一 条数 据 会被 封装 到 User 对象。



疯狂软件. Spring+MyBatis企业应用实战（第2版） \(Kindle Locations 3801-3802\). 电子工业出版社. Kindle Edition. 



