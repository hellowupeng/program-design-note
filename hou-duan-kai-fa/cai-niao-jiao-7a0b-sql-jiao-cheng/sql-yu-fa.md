# SQL 语法

### 数据库表

数据库通常包含一个或多个表。每个表由一个名字表示，表包含带有数据的记录（行）。

```bash
mysql> use RUNOOB;
Database changed

mysql> set names utf8;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM Websites;
+----+--------------+---------------------------+-------+---------+
| id | name         | url                       | alexa | country |
+----+--------------+---------------------------+-------+---------+
| 1  | Google       | https://www.google.cm/    | 1     | USA     |
| 2  | 淘宝          | https://www.taobao.com/   | 13    | CN      |
| 3  | 菜鸟教程      | http://www.runoob.com/    | 4689  | CN      |
| 4  | 微博          | http://weibo.com/         | 20    | CN      |
| 5  | Facebook     | https://www.facebook.com/ | 3     | USA     |
+----+--------------+---------------------------+-------+---------+
5 rows in set (0.01 sec)
```

##### 解析

* `use RUNOOB;` 命令用于选择数据库。
* `set names utf8;` 命令设置使用的字符集。
* `SELECT * FROM Websites;` 读取数据表的信息。
* 上面的表包含五条记录（每一条对应一个网站信息）和5个列（id、name、url、alexa 和 country）。

> 注意: SQL 对大小写不敏感：SELECT 与 select 是相同的。

### 分号

### 重要的 SQL 命令

* **SELECT - **从数据库提取数据

* **UPDATE - **更新数据库数据

* **DELETE - **从数据库删除数据

* **INSERT INTO - **向数据库插入新数据

* **CREATE DATABASE - **创建新数据库

* **ALTER DATABASE - **修改数据库

* **CREATE TABLE - **创建新表

* **ALTER TABLE - **变更数据库表

* **DROP TABLE - **删除表

* **CREATE INDEX - **创建索引（搜索键）

* **DROP INDEX - **删除索引



