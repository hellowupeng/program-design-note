# SQL INSERT INTO SELECT 语句

通过 SQL，您可以从一个表复制信息到另一个表。

INSERT INTO SELECT 语句从一个表复制数据，然后把数据插入到一个已存在的表中。

### 语法

我们可以从一个表中复制所有的列插入到另一个已存在的表中：

```
INSERT INTO table2
SELECT * FROM table1;
```

或者我们可以只复制希望的列插入到另一个已存在的表中：

```
INSERT INTO table2
(column_name(s))
SELECT column_name(s)
FROM table1;
```

### 演示数据库

"Websites" 表：

```
+----+--------------+---------------------------+-------+---------+
| id | name         | url                       | alexa | country |
+----+--------------+---------------------------+-------+---------+
| 1  | Google       | https://www.google.cm/    | 1     | USA     |
| 2  | 淘宝          | https://www.taobao.com/   | 13    | CN      |
| 3  | 菜鸟教程      | http://www.runoob.com/    | 4689  | CN      |
| 4  | 微博          | http://weibo.com/         | 20    | CN      |
| 5  | Facebook     | https://www.facebook.com/ | 3     | USA     |
| 7  | stackoverflow | http://stackoverflow.com/ |   0 | IND     |
+----+---------------+---------------------------+-------+---------+
```

"apps" APP表：

```
mysql> SELECT * FROM apps;
+----+------------+-------------------------+---------+
| id | app_name   | url                     | country |
+----+------------+-------------------------+---------+
|  1 | QQ APP     | http://im.qq.com/       | CN      |
|  2 | 微博 APP | http://weibo.com/       | CN      |
|  3 | 淘宝 APP | https://www.taobao.com/ | CN      |
+----+------------+-------------------------+---------+
3 rows in set (0.00 sec)
```

### 实例

复制 "apps" 中的数据插入到 "Websites" 中：

```
INSERT INTO Websites (name, country)
SELECT app_name, country FROM apps;
```

只复 QQ 的 APP 到 "Websites" 中：

```
INSERT INTO Websites (name, country)
SELECT app_name, country FROM apps
WHERE id=1;
```



