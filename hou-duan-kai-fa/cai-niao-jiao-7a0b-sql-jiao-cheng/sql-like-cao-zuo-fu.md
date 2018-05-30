# SQL LIKE 操作符

LIKE 操作符用于在 WHERE 子句中搜索列中的指定模式。

### SQL LIKE 语法

```
SELECT column_name(s)
FROM table_name
WHERE column_name LIKE pattern;
```

实例：

```
// 实例1：选取 name 以字母 "G" 开始的所有客户：
SELECT * FROM Websites
WHERE name LIKE 'G%';

// 实例2：选取 name 以字母 "k" 结尾的所有客户：
SELECT * FROM Websites
WHERE name LIKE '%k';

// 实例3：选取 name 包含模式 "oo" 的所有客户：
SELECT * FROM Websites
WHERE name LIKE '%oo%';

// 实例4:选取 name 不包含模式 "oo" 的所有客户：
SELECT * FROM Websites
WHERE name NOT LIKE '%oo%';
```

> 提示：
>
> "%" 符号用于在模式的前后定义通配符（缺省字母）。



