# 10.3.4 修改数据

创建 一个 修改 tb\_ user 表 数据 的 存储 过程。

```
# 修改数据的存储过程
DROP PROCEDURE IF EXISTS update_user;
DELIMITER //
CREATE PROCEDURE update_user
(IN v_id INTEGER,IN v_name VARCHAR(18),IN v_sex VARCHAR(19),IN v_age INTEGER)
BEGIN
UPDATE tb_user SET NAME = v_name,sex = v_sex,age = v_age
WHERE id = v_id;
END
//
```

org.fkit.mapper.UserMapper.xml

```
<update id="modifyUser" parameterType="org.fkit.domain.User" statementType="CALLABLE">
    {call update_user(
    #{id,mode=IN,#{name,mode=IN},#{sex,mode=IN},#{age,mode=IN}}
    )}
</update>
```

＜ update…/ ＞ 元素 调用 名为“ update\_ user” 的 存储 过程 修改 User 数据。



