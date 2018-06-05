# UPDATE 语句

UPDATE 语句用于更新表中的记录。

### 语法

```
UPDATE table_name
SET column1=value1,column2=value2,...
WHERE some_column=some_value;
```

"Websites" 表:

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

### 实例

```
UPDATE Websites 
SET alexa='5000', country='USA' 
WHERE name='菜鸟教程';
```

输出结果：![](/assets/sql update 实例.png)

