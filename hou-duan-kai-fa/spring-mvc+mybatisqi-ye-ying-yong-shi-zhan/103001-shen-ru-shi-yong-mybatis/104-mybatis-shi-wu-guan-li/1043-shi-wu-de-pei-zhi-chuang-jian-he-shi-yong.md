# 10.4.3 事务的配置创建和使用

### 1.事务的配置

mybatis-config.xml

```
<environment id="mysql">
    <!-- 指定事务管理类型，type="JDBC"指直接简单使用了 JDBC 的提交和回滚设置，type="MANAGED"指让容器实现对事务的管理 --!>
    <transactionManager type="JDBC"/>
    <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
    </dataSource>
</environment>
```

＜ environment ＞ 元素 定义 了 连接 某个 数据库 的 信息， 其 子 元素 ＜ transactionManager ＞ 的 type 决定 我们 用 什么 类型 的 事务管理 机制。

### 2.事务工厂的创建

MyBatis 事务 的 创建 是 交给 org. apache. ibatis. transaction. TransactionFactory 事务 工厂 来 完成 的。 如果 我们将 ＜ transactionManager ＞ 的 type 配置 为 JDBC， 那么， 在 MyBatis 初始化 解析 ＜ environment ＞ 节点 时， 会 根据 type=＂ JDBC＂ 创建 一个 JdbcTransactionFactory 工厂， 其 源 码 如下：

```java
// 解析<transactionManager>节点，创建对应的 TransactionFactory
private TransactionFactory transactionManagerElement(XNode context) throws Exception {
    /**
     在 Configuration 初始化的时候，会通过以下语句，注册 JDBC 和 MANAGED 对应的工厂类
     typeAliasRegistry.registryAlias("JDBC", JdbcTransactionFactory.class);
     typeAliasRegistry.registryAlias("MANAGED", ManagedTransactionFactory.class);
     */
     
     if (context != null) {
         String type = context.getStringAttribute("type");
         Properties props = context.getChildrenAsProperties();
         // 创建对应的工厂实例，返回的是 JdbcTransactionFactory 或者 ManagedTransactionFactory 其中之一
         ManagedTransactionFactory factory = (TransactionFactory)resolveClass(type).newInstance();
         factory.setProperties(props);
         return factory;
     }
     throw new BuilderException("Environment declaration requires a TransactionFactory.");
}
```

### 3.事务工厂 TransactionFactory

通过 事务 工厂 TransactionFactory 很容易 获取 到 Transaction 对象 实例。 我们 以 JdbcTransaction 为例， 看一下 JdbcTransactionFactory 是 怎样 生成 JdbcTransaction 的。 JdbcTransaction 源 代码 如下：

```java
public class JdbcTransactionFactory implements TransactionFactory {
    @Override
    public void setProperties(Properties props) {}
    
    // 根据给定的数据库连接 Connection 创建 Transaction
    @Override
    public Transaction newTransaction(Connection conn) {
        return new JdbcTransaction(conn);
    }
    
    // 根据 DataSource、隔离级别和是否自动提交创建 Transaction
    @Override
    public Transaction newTransaction(DataSource ds, TransactionIsolationLevel level, boolean autoCommit) {
        return new JdbcTransaction(ds, level, autoCommit);
    }
}
```

### 4.JdbcTransaction

JdbcTransaction 可直接 使用 JDBC 的 提交 和 回 滚 事务管理 机制。 它 依赖于 从 dataSource 中 取得 的 连接 connection 来 管理 transaction 的 作用域， connection 对象 的 获取 被 延迟 到 调用 getConnection（） 方法 时。 如果 将 autocommit 设置 为 on， 开启 状态 的 话， 则 它 会 忽略 commit 和 rollback。

也就是说， JdbcTransaction 是 使用 java. sql. Connection 上 的 commit 和 rollback 功能 来 完成 事务 操作 的， JdbcTransaction 只是 相当于 对 java. sql. Connection 事务处理 进行 了 再次 封装， Transaction 的 事务管理 都是 通过 java. sql. Connection 实现 的。

### 5.ManagedTransaction

ManagedTransaction 让 容器 来 管理 事务 Transaction 的 整个 生命 周期， 意思 就是说， 使用 ManagedTransaction 的 commit 和 rollback 功能 不会 对 事务 有 任何 的 影响， 它 什么 都 不会 做， 它将 事务管理 的 权力 移交 给了 容器。

从 ManagedTransaction 类 的 源 代码 可以 看出， 提交 和 回 滚 时 它 什么 都 没有 做， 所以， 当 使用 ManagedTransaction 时 MyBatis 的 事务 是 交给 容器 来 操作 管理 的。

