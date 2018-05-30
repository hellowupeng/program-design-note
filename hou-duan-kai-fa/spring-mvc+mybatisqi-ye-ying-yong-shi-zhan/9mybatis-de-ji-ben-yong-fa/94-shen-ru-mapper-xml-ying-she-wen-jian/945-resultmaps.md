# 9.4.5 ResultMaps

resultMap 元素 是 MyBatis 中最 重要 最强 大的 元素。 它的 作用 是 告诉 MyBatis 将从 结果 集中 取出 的 数据 转换 成 开发者 所需 要的 对象。

是最 简单 的 映射 语句 示例：![](/assets/resultmap映射示例.png)selectUser 的 ＜ select.../ ＞ 元素 执行 一条 查询 语句， 查询 TB\_ USER 表 的 所有 数据。 resultType=＂ map＂ 表示 返回 的 数据 是 一个 Map 集合（ 使用 列名 作为 key， 列 值 作为 value）。

**＜ resultMap.../ ＞常用 属性 如下：**

* **id**。 resultMap 的 唯一 标识符。
* **type**。 resultMap 实际 返回 的 类型。

**＜ resultMap.../ ＞ 的 两个 子 元素 id 和 result：**

* **result**。 表示 数据库 表 的 普通 列， 其中， column 属性 表示 数据库 表 的 列名， property 表示 数据库 列 映射 到 返回 类型 的 属性。
* **result**。 表示 数据库 表 的 普通 列， 其中， column 属性 表示 数据库 表 的 列名， property 表示 数据库 列 映射 到 返回 类型 的 属性。



复杂映射：![](/assets/复杂映射.png)代码解释：

1. 首先 执行 id 为 selectStudent 的 ＜ select.../ ＞ 元素， 查询 所有 的 学生 数据， 此时 返回 的 不是 简单 的 Student 对象， 因为 Student 对象 中 还 包含 了 Clazz 对象， 所以 使用 resultMap 去 映射 返回 类型。
2. id 为 studentResultMap 的 ＜ resultMap.../ ＞ 元素 返回 类型 为 org. fkit. domain. Student， 其中， id、 name、 sex 和 age 都是 简单 的 属性 映射， 而 查询 的 班级 id 列 clazz\_ id 则 使用 了 关联 映射 ＜ association.../ ＞。
   **＜ association.../ ＞ 元素 的 解释 如下：**
   **column**。 表示 数据库 表 的 列名。
   **property**。 表示 返回 类型 Student 的 属性 名 clazz。
   **javaType**。 表示 该 属性 对应 的 类型 名称， 本 示例 是 一个 Clazz 类型。
   **select**。 表示 执行 一条 查询 语句， 将 查询 到 的 数据 封装 到 property 所 代表 的 类型 对象 当中。 上面 的 **selectClazzWithId** 执行 一条 SQL 语句， 将 学生 的 clazz\_ id 作为 参数 查询 对应 的 班级 信息。

![](/assets/collectionmapper.png)上面 的 映射 和 查询 学生 关联 班级 类似， 具体 解释 如下：

1. 首先 执行 id 为 selectClazz 的 ＜ select.../ ＞ 元素， 查询 所有 的 班级 数据， 此时 返回 的 不是 简单 的 Clazz 对象， 因为 Clazz 对象 中 还 包含 了 学生 的 集合 对象， 所以 使用 resultMap 去 映射 返回 类型。
2. id 为 clazzResultMap 的 ＜ resultMap.../ ＞ 元素 返回 类型 为 org. fkit. domain. Clazz， 其中， id 和 code 都是 简单 的 属性 映射， 而 查询 班级 所有 学 生时 则 使用 了 集合 映射 ＜ collection.../ ＞。
   **＜ collection.../ ＞ 元素 的 解释 如下：**
   **property**。 表示 返回 类型 Clazz 的 属性 名 students。
   **javaType**。 表示 该 属性 对应 的 类型 名称， 本 示例 中 是 一个 ArrayList 集合。
   **ofType**。 表示 集合 当中 的 类型， 本 示例 中 是 Student 类型。

   **column**。 表示 使用 id 作为 参数 进行 之后 的 select 语句 查询。

   **select**。 表示 执行 一条 查询 语句， 将 查询 到 的 数据 封装 到 property 所 代表 的 类型 对象 当中。 上面 的 **selectStudentWithId** 执行 一条 SQL 语句， 将 班级 的 id 作为 参数 查询 班级 对应 的 所有 学生 信息。



