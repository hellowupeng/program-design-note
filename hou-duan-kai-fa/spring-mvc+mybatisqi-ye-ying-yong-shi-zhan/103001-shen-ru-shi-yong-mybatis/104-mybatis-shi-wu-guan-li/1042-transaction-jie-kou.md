# 10.4.2 Transaction 接口

MyBatis 事务 设计 的 重点 是 org. apache. ibatis. transaction. Transaction 接口， Transaction 接口 有两 个 实现 类， 分别 是 org. apache. ibatis. transaction. jdbc. JdbcTransaction 和 org. apache. ibatis. transaction. managed. ManagedTransaction。 同时 MyBatis 还 设计 了 org. apache. ibatis. transaction. TransactionFactory 接口 和 两个 实现 类 org. apache. ibatis. transaction. jdbc. JdbcTransactionFactory 和 org. apache. ibatis. transaction. managed. ManagedTransactionFactory 用来 获取 事务 的 实例 对象。

对 数据库 的 事务 而言， 应该 具有 以下 几个 动作： 创建（ create）、 提交（ commit）、 回 滚（ rollback）、 关闭（ close）。

```java
public interface Transaction {

    // 获取数据库连接
    Connection getConnection() throws SQLException;

    // 提交
    void commit() throws SQLException;

    // 回滚
    void rollback() throws SQLException;
    
    // 关闭数据库连接
    void close() throws SQLException;
}
```

MyBatis 的 事务管理 分为 两种 形式：

* **使用 JDBC 的 事务管理 机制**。 即 利用 java. sql. Connection 对象 完成 对 事务 的 提交（ commit（））、 回 滚（ rollback（）） 和 关闭（ close（）） 等 操作。
* **使用 MANAGED 的 事务管理 机制**。 对于 这种 机制， MyBatis 自身 不会 去 实现 事务管理， 而是 让 容器 如 WebLogic、 JBoss 等 来 实现 对 事务 的 管理。



