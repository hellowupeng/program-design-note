# SQL DELETE 语句

DELETE 语句用于删除表中的记录。

### SQL DELETE 语法

```
DELETE FROM table_name
WHERE some_column=some_value;
```

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
|  6 | 百度         | https://www.baidu.com/    |     4 | CN      |
|  7 | stackoverflow | http://stackoverflow.com/ |     0 | IND     |
+----+---------------+---------------------------+-------+---------+
```

### SQL DELETE 实例

```
DELETE FROM Websites
WHERE name='百度' AND country='CN';
```

输出结果：![](/assets/SQL delete实例.png)

### 删除所有数据

可以在不删除表的情况下，删除表中所有的行。这意味着表结构、属性、索引将保持不变：

```
DELETE FROM table_name;

或

DELETE * FROM table_name;
```





