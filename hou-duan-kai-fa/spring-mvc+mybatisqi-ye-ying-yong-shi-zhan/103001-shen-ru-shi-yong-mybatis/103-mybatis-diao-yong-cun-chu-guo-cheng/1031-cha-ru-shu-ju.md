# 10.3.1 插入数据

SQL 脚本：

```
# 插入数据的存储过程
DROP PROCEDURE IF EXISTS insert_user;
DELIMITER //
CREATE PROCEDURE insert_user
(OUT v_id INTEGER,IN v_name VARCHAR(18),IN v_sex VARCHAR(19),IN v_age INTEGER)
BEGIN
# 执行 insert 操作，values 中的值是传入的参数
INSERT INTO tb_user(NAME,sex,age) VALUES(v_name,v_sex,v_age);
# 调用 LAST_INSERT_ID 函数将插入成功自动生成的主键 id 值设置到变量 v_id 中
SET v_id = LAST_INSERT_ID();
END
//
```



