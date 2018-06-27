# 事务管理

Spring 支持两种类型的事务管理：

* [Programmatic transaction management](https://www.tutorialspoint.com/spring/programmatic_management.htm)：这意味着你必须在编程的帮助下管理事务。这给你极大的灵活性，但很难维护。

* [Declarative transaction management](https://www.tutorialspoint.com/spring/declarative_management.htm)：这意味着您将事务管理与业务代码分开。您只能使用注释或基于XML的配置来管理事务。

声明式事务管理优于程序式事务管理，虽然它没有程序式事务管理灵活，程序式事务管理允许您通过代码控制事务。但作为一种横切关注点，声明式事务管理可以用 AOP 方法模块化。Spring 通过 Spring AOP 框架支持声明式事务管理。





