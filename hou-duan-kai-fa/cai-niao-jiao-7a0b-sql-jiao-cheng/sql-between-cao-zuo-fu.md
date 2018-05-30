# SQL BETWEEN 操作符

BETWEEN 操作符选取介于两个值之间的数据范围内的值。这些值可以是数值、文本或者日期。

### SQL BETWEEN 语法

```
SELECT column_name(s)
FROM table_name
WHERE column_name BETWEEN value1 AND value2;
```

### 实例

```
// 实例1：选取 alexa 介于 1 和 20 之间的所有网站：
SELECT * FROM Websites
WHERE alexa BETWEEN 1 AND 20;

// 实例2：不在上面实例范围内的网站，使用 NOT BETWEEN
SELECT * FROM Websites
WHERE alexa NOT BETWEEN 1 AND 20;

// 实例3：选取alexa介于 1 和 20 之间但 country 不为 USA 和 IND 的所有网站
SELECT * FROM Websites
WHERE (alexa BETWEEN 1 AND 20)
AND NOT country IN ('USA', 'IND');

// 实例4：选取 name 以介于 'A' 和 'H' 之间字母开始的所有网站：
SELECT * FROM Websites
WHERE name BETWEEN 'A' AND 'H';

// 实例5：选取 name 不介于 'A' 和 'H' 之间字母开始的所有网站：
SELECT * FROM Websites
WHERE name NOT BETWEEN 'A' AND 'H';

// 实例6：选取 date 介于 '2016-05-10' 和 '2016-05-14' 之间的所有访问记录：
SELECT * FROM access_log
WHERE date BETWEEN '2016-05-10' AND '2016-05-14';
```





