# SQL ORDER BY 关键字

ORDER BY 关键字用于对结果集进行排序。

ORDER BY 关键字用于对结果集按照一个列或者多个列进行排序。

ORDER BY 关键字默认按照升序对记录进行排序。如果需要按照降序对记录进行排序，您可以使用 DESC 关键字。

### SQL ORDER BY 语法

```
SELECT column_name,column_name
FROM table_name
ORDER BY column_name,column_name ASC|DESC;
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

### ORDER BY 实例

```
SELECT  * FROM Websites
ORDER BY alexa;
```

### 输出结果：![](/assets/order by 输出结果.png)ORDER BY DESC 实例

降序排序

```
SELECT  * FROM Websites
ORDER BY alexa DESC;
```

### ORDER BY 多列

```
SELECT * FROM Websites
ORDER BY country,alexa;
```

输出结果：![](/assets/order by多列排序.png)



