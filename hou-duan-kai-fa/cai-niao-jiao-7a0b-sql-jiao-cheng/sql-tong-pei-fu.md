# SQL 通配符

通配符可用于替代字符串中的任何其他字符。

在 SQL 中，通配符与 SQL LIKE 操作符一起使用。SQL 通配符用于搜索表中的数据。

在 SQL 中，可使用以下通配符：

* %：替代 0 个或多个字符
* -: 替代一个字符
* \[charlist\]: 字符列中的任何单一字符
* \[^charlist\] 或 \[!charlist\]: 不在字符列中的任何单一字符

实例：

```
// 实例1：选取 url 以字母 "https" 开始的所有网站：
SELECT * FROM Websites
WHERE url LIKE 'https%';

// 实例2：取 url 包含模式 "oo" 的所有网站：
SELECT * FROM Websites
WHERE url LIKE '%oo%';

// 实例3：取 name 以一个任意字符开始，然后是 "oogle" 的所有客户：
SELECT * FROM Websites
WHERE name LIKE '_oogle';

// 实例4：选取 name 以 "G" 开始，然后是一个任意字符，然后是 "o"，然后是一个任意字符，然后是 "le" 的所有网站：
SELECT * FROM Websites
WHERE name LIKE 'G_o_le';

// 实例5：选取 name 以 "G"、"F" 或 "s" 开始的所有网站：
SELECT * FROM Websites
WHERE name REGEXP '^[GFs]';

// 实例6：选取 name 以 A 到 H 字母开头的网站：
SELECT * FROM Websites
WHERE name REGEXP '^[A-H]';

// 实例7：选取 name 不以 A 到 H 字母开头的网站：

SELECT * FROM Websites
WHERE name REGEXP '^[^A-H]';
```

MySQL 中使用 **REGEXP **或 **NOT REGEXP **运算符 \(或 RLIKE 和 NOT RLIKE\) 来操作正则表达式。

