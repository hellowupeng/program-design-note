# SQL 别名

通过使用 SQL，可以为表名称或列名称指定别名。

创建别名是为了让列名称的可读性更强。

### 列的 SQL 别名语法

```
SELECT column_name AS alias_name
FROM table_name;
```

### 表的 SQL 别名语法

```
SELECT column_name(s)
FROM table_name AS alias_name;
```

### 实例

```
// 实例1：指定两个别名，一个是 name 列的别名，一个是 country 列的别名。提示：如果列名称包含空格，要求使用双引号或方括号：
SELECT name AS n, country AS c
FROM Websites;

// 实例2：把三个列（url、alexa 和 country）结合在一起，并创建一个名为 "site_info" 的别名：
SELECT name, CONCAT(url, ', ', alexa, ', ', country) AS site_info
FROM Websites;

// 实例3：使用 "Websites" 和 "access_log" 表，并分别为它们指定表别名 "w" 和 "a"（通过使用别名让 SQL 更简短）
SELECT w.name, w.url, a.count, a.date 
FROM Websites AS w, access_log AS a 
WHERE a.site_id=w.id and w.name="菜鸟教程";
// 不带别名的 SQL
SELECT Websites.name, Websites.url, access_log.count, access_log.date 
FROM Websites, access_log 
WHERE Websites.id=access_log.site_id and Websites.name="菜鸟教程";
```

在下面的情况下，使用别名很有用：

* 在查询中涉及超过一个表
* 在查询中使用了函数
* 列名称很长或者可读性差
* 需要把两个列或者多个列结合在一起





