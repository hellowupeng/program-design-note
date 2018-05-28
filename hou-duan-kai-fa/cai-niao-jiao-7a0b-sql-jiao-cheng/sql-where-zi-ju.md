# SQL WHERE 子句

WHERE 子句用于过滤记录。

WHERE 子句用于提取那些满足指定标准的记录。

### SQL WHERE 语法

```
SELECT column_name,column_name
FROM table_name
WHERE column_name operator value;
```

### 演示数据库

Websites 表：

```
+----+--------------+---------------------------+-------+---------+
| id | name         | url                       | alexa | country |
+----+--------------+---------------------------+-------+---------+
| 1  | Google       | https://www.google.cm/    | 1     | USA     |
| 2  | 淘宝          | https://www.taobao.com/   | 13    | CN      |
| 3  | 菜鸟教程      | http://www.runoob.com/    | 4689  | CN      |
| 4  | 微博          | http://weibo.com/         | 20    | CN      |
| 5  | Facebook     | https://www.facebook.com/ | 3     | USA     |
+----+--------------+---------------------------+-------+---------+
```

实例：

```
SELECT * FROM Websites WHERE country='CN';
```

### 输出结果：![](/assets/WHERE 子句实例.png)文本字段 vs 数值字段

SQL 使用单引号来环绕文本值（大部分数据库系统也接受双引号）。

如果是数值字段，请不要使用引号。

实例：

```
SELECT * FROM Websites WHERE id=1;
```

### 输出结果：![](/assets/文本字段 vs. 数值字段.png)WHERE 子句中的运算符

### ![](/assets/WHERE 子句中的运算符.png)



