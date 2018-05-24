# 1.1.2 Java EE 应用的组件

Java EE 应用大致包括如下几类组件：

* **表现层组件。**主要负责用户输入数据，或者向客户显示系统状态。表现层技术：JSP、Velocity、FreeMarker、Tapestry 等技术，或者使用普通的应用程序充当表现层组件，甚至可以是小型智能设备。
* **控制器组件。**Java EE MVC 框架，提供一个前端核心控制器：负责拦截用户请求，并将请求转发给用户实现的控制器组件。用户实现的控制器组件负责调用业务逻辑方法、处理用户请求。
* **业务逻辑组件。**系统核心组件，实现系统的业务逻辑。通常一个一个业务逻辑方法对应一次用户操作。一个业务逻辑方法应该是一个整体，因此要求对业务逻辑方法增加事务性。业务逻辑方法仅仅负责实现业务逻辑，不应该进行数据库访问。业务逻辑组件中不应该出现原始的 MyBatis、Hibernate 和 JDBC 等 API。

  > 注意  
  > 保证业务逻辑组件之中不出现 MyBatis、Hibernate 和 JDBC 等 API，有一个更重要的原因：为保证业务逻辑方法的实现，与具体的持久层访问技术分离。当系统需要在不同持久层技术直接切换时，系统的业务逻辑组件无须任何改变。
  >
  > 最理想的情况是，业务逻辑组件中都不应该出现持久层 API。

* **DAO 组件。**这个类型的对象比较缺乏变化，每个 DAO 组件都提供 Domain Object 对象基本的创建、查询、更新和删除等操作，这些操作对应数据库的 CRUD（创建、查询、更新、删除）等原子操作。采用不同持久层访问技术，DAO 组件的实现会完全不同。为了业务逻辑组件的实现与 DAO 组件的实现分离，程序应该为每个 DAO 组件都提供接口，业务逻辑组件面向 DAO 接口编程，提供更好的解耦。

* **领域对象组件。**领域对象（Domain Object）抽象了系统的对象模型。通常，领域对象的状态都必须保存在数据库里。每个领域对象通常对应一个或多个数据表，领域对象通常需要提供对数据记录的访问方式。


