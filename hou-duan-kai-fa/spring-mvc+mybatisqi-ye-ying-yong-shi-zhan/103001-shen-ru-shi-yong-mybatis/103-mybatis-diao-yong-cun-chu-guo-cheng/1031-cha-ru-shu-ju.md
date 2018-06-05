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

1. DELIMITER 是 MySQL 解释器， 该 段 命令 是否 已经 结束 了， MySQL 是否 可以可以 执行 了， 因为 在 MySQL 当中， 默认 情况 下一 遇到 分号， 它 就要 自动 执行。
2. 在 存储 过程中 使用 参数 时， 除了 参数 名 和 参数 类型 之外， 还 建议 指定 参数 的 mode（ 模式）， 可选 值 为 IN、 OUT 和 INOUT 三种。 入 参 使用 IN， 出 参（ 返回） 使用 OUT， 输入 输出 参数 使用 INOUT.

3. （3） LAST\_ INSERT\_ ID（） 是 MySQL 的 函数， 和 AUTO\_ INCREMENT 属性 一起 使用， 当 往 带有 AUTO\_ INCREMENT 属性 字段 的 表中 新增 数据 时， LAST\_ INSERT\_ ID（） 函数 返回 该 字段 的 值。



