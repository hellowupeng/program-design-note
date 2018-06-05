# 10.2.5 foreach

动态 SQL 另外 一个 常用 的 操作 就是 需要 对 一个 集合 进行 遍历， 通常 发生 在 构建 IN 条件 语句 时。

EmployeeMapper.xml

```
<select id="selectEmployeeIn" resultType="org.fkit.domain.Employee">
    SELECT *
    FROM tb_employee
    WHERE ID in
    <foreach item="item" index="index" collection="list"
        open="(" separator="," close=")">
        #{item}
    </foreach>
</select>
```





