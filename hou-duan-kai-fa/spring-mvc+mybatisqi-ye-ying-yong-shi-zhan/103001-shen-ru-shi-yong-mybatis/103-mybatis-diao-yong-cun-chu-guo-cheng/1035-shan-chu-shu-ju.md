# 10.3.5 删除数据

创建 一个 删除 tb\_ user 表 数据 的 存储 过程。

```
# 删除数据的存储过程
DROP PROCEDURE IF EXISTS delete_user_by_id;
DELIMITER //
CREATE PROCEDURE delete_user_by_id(IN v_id INTEGER)
BEGIN
DELETE FROM tb_user WHERE id = v_id;
END
//
```

org.fkit.mapper.UserMapper.xml

```
<delete id="removeUser" parameterType="org.fkit.domain.User" statementType="CALLABLE">
    {call delete_user_by_id(#{id,mode=IN})}
</delete>
```

＜ delete…/ ＞ 元素 调用 名为“ delete\_ user\_ by\_ id” 的 存储 过程 根据 id 删除 User 数据。





