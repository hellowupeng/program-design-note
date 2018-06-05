# SELECT DISTINCT 语句

SELECT DISTINCT 语句用于返回唯一不同的值。

### 语法

```
SELECT DISTINCT column_name,column_name
FROM table_name;
```

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
SELECT DISTINCT country FROM Websites;
```

输出结果：

![](/assets/SELECT DISTINCT 实例.png)

