# SQL IN 操作符

IN 操作符允许您在 WHERE 子句中规定多个值。

### SQL IN 语法

```
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1,value2,...);
```

### 实例：

```bash
// 实例1：选取 name 为 "Google" 或 "菜鸟教程" 的所有网站：
SELECT * FROM Websites
WHERE name IN ('Google','菜鸟教程');
```



