# 10.2.2 choose\(when, otherwise\)

```
<select id="selectEmployeeChoose"
    parameterType="hashmap"
    resultType="org.fkit.domain.Employee">
    <!-- 如果传入了 id，就根据 id 查询，没有传入 id 就根据 loginname 和 password 查询，否则查询 sex 等于男的数据 --!>    
    <choose>
        <when test="id != null">
            and id = #{id}
        </when>
        <when test="loginname != null and password != null">
            and loginname = #{loginname} and password = #{password}
        </when>
        <otherwise>
            and sex = '男'
        </otherwise>
    </choose>
</select>
```





