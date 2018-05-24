# 2.1.1 传统 Model1 和 Model2

Java Web 应用的结构经历了 Model1 和 Model2 两个时代，从 Model1 发展到 Model2 既是技术发展的必然，也是无数程序员的结晶。

* Model1 模式。整个 Web 应用几乎全部由 JSP 页面组成，JSP 页面接收处理客户端请求，对请求处理后直接做出响应。用少量的 JavaBean 来处理数据库连接、数据库访问等操作。
  局限性：JSP 页面身兼 View 和 Controller 两种角色，将控制逻辑和表现逻辑混杂在一起，从而导致代码的重用性非常低，增加应用的拓展和维护难度。
* Model2 是基于 MVC 架构的设计模式。在 Model2 架构中，Servlet 作为前端控制器，负责接收客户端发送的请求。在 Servlet 中只包含控制逻辑和简单的前端处理；然后调用后端 JavaBean 来完成实际的逻辑处理；最后，将其转发到相应的 JSP 页面来处理显示逻辑。
  具体实现方式：
  ![](/assets/Model2 的流程.png)
  Model2 下 JSP 不再承担控制器责任，仅是表现层角色，仅用于将结果呈现给用户。JSP 页面的请求与 Servlet（控制器）交互，而 Servlet 负责与后台的 JavaBean 通信。在 Model2 模式下，模型（Model）由 JavaBean 充当，视图（View）由 JSP 页面充当，控制器（Controller）由 Servlet 充当。



