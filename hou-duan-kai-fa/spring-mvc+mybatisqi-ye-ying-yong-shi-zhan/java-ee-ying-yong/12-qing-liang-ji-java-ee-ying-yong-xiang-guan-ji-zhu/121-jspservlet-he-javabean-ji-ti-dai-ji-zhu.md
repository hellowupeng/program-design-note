# 1.2.1 JSP、Servlet 和 JavaBean 及替代技术

JSP：最早的 Java EE 规范之一，最经典 Java EE 技术之一。 充当 Java EE 应用表现层角色。具有简单、易用特点。

Servlet 和 JSP 底层运行原理完全一样。JSP 被 Web 服务器编译成 Servlet，在 Web 服务器内运行的是 Servlet。JSP 相当于一个“草稿”文件，Web 服务器根据它生成 Servlet，Servlet 提供真正 HTTP 服务。广义的 Servlet 包含 JSP 和 Servlet。

Servlet：开发成本高，充当表现层导致表现层页面难维护，不利于美工人员参与开发。

Servlet3.X 规范：提供异步请求、注解、增强的 Servlet API、非阻塞 IO 功能，简化了 Java Web 开发。

Java EE 应用使用 JavaBean 传输数据。在严格的 Java EE 应用中，中间层的组件会将应用底层的状态封装成 JavaBean 集，这些 JavaBean 也被称为 DTO（Data Transfer Object，数据传输对象），将这些 DTO 集传到 JSP 页面，从而让 JSP 可以显示应用的底层状态。

Java EE 表现层技术：JSP、FreeMarker、Velocity



