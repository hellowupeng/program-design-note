# 10.2.3 where

EmployeeMapper.xml

```
<select id="selectEmployeeLike"
    resultType="org.fkit.domain.Employee">
    SELECT * FROM tb_employee
    <where>
        <if test="state != null">
            state = #{state}
        </if>
        <if test="id != null">
            and id = #{id}
        </if>
        <if test="loginname != null and password != null">
            and loginname = #{loginname} and password = #{password}
        </if>
    </where>
</select>
```

where 元素 知道 只有 在 一个 以上 的 if 条件 有 值 的 情况下 才会 插入 WHERE 子句。 而且， 若 最后 的 内容 是“ AND” 或“ OR” 开头， 则 where 元素 也 知道 如何 将它 们 去除。



