# SQL 连接（JOIN）

SQL join 用于把来自两个或多个表的行结合起来。

SQL INNER JOIN 从多个表中返回满足 JOIN 条件的所有行。

### 实例

```
// 实例1：
SELECT Websites.id, Websites.name, access_log.count, access_log.date
FROM Websites
INNER JOIN access_log
ON Websites.id=access_log.site_id;
```

### 不同的 SQL JOIN

* **INNER JOIN: **如果表中有至少一个匹配，则返回行

* **LEFT JOIN: **即使右表中没有匹配，也从左表返回所有的行

* **RIGHT JOIN: **即使左表中没有匹配，也从右表返回所有的行

* **FULL JOIN: **只要其中一个表中存在匹配，则返回行



