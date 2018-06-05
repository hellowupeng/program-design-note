# 10.3.3 根据 id 查询数据返回对象

创建 一个 根据 id 查询 tb\_ user 表 对应 数据 的 存储 过程。

```
# 根据 id 查询数据的存储过程
DROP PROCEDURE IF EXISTS select_user_by_id;
DELIMITER //
CREATE PROCEDURE select_user_by_id(IN v_id INTEGER)
BEGIN
SELECT id,NAME,sex,age FROM tb_user WHERE id = v_id;
END
//
```

```
<select id="selectUserById" parameterType="int" resultType="org.fkit.domain.User" statementType="CALLABLE">
    {call select_user_by_id(#{id,mode=IN})}
</select>
```

＜ select…/ ＞ 元素 调用 名为“ select\_ user\_ by\_ id” 的 存储 过程 根据 id 查询 对应 的 数据 并 返回 User 对象。



