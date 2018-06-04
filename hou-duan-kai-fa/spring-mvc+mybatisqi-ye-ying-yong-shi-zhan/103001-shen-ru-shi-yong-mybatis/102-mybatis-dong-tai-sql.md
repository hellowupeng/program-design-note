# 10.2 MyBatis 动态 SQL

MySQL 采用基于 OGNL 的表达式来完成动态 SQL。OGNL 的表达式可以用在任意的 SQL 映射语句中。

常用动态 SQL 元素：

```
if
choose(when, otherwise)
where
set
foreach
bind
```



