# 9.2 深入 MyBatis 的配置文件

SqlSessionFactoryBuilder 根据 传入 的 输入 流 生成 Configuration 对象， 然后 根据 Configuration 对象 创建 默认 的 SqlSessionFactory 实例。

MyBatis 初始化基本流程：

![](/assets/SqlSessionFactory初始化过程.png)

MyBatis 初始化 要 经过 以下 几步：

1. 调用 SqlSessionFactoryBuilder 对象 的 build（ inputStream） 方法。
2. SqlSessionFactoryBuilder 会 根据 输入 流 inputStream 等 信息 创建 XMLConfigBuilder 对象。
3. SqlSessionFactoryBuilder 调用 XMLConfigBuilder 对象 的 parse（） 方法。
4. XMLConfigBuilder 对象 解析 XML 配置文件 返回 Configuration 对象。
5. SqlSessionFactoryBuilder 根据 Configuration 对象 创建 一个 DefaultSessionFactory 对象。
6. SqlSessionFactoryBuilder 返回 DefaultSessionFactory 对象 给 客户 端， 供 客户 端 使用。

SqlSessionFactory 是 根据 MyBatis 的 配置文件 mybatis- config. xml 创建 的。



