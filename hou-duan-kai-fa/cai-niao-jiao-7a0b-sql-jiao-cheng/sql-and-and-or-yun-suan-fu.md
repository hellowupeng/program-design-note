# AND & OR 运算符

AND & OR 运算符用于基于一个以上的条件对记录进行过滤。

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

##### AND 运算符实例

```
SELECT * FROM Websites
WHERE country='CN'
AND alexa > 50;
```

输出结果：![](/assets/and运算符实例.png)

##### OR 运算符实例

```
SELECT * FROM Websites
WHERE country='USA'
OR country='CN';
```

##### 输出结果：![](/assets/or 运算符实例.png)结合 AND & OR

```
SELECT * FROM Websites
WHERE alexa > 15
AND (country='CN' OR country='USA');
```

输出结果：![](/assets/结合 and or实例.png)

