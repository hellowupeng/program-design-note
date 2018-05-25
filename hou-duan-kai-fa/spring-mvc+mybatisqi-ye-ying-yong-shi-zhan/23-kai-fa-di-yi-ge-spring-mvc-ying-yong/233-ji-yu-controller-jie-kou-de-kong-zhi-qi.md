# 2.3.3 基于 Controller 接口的控制器

DispatcherServlet 在 Spring 中充当前端控制器角色，核心功能是分发请求。请求会被分发给对应处理的 Java 类，Spring MVC 中成为 Handle。

Spring2.5版本之前：开发一个 Handle 的唯一方法是实现 org.springframework.web.servlet.mvc.Controller 接口。Controller 接口必须实现方法：

```java
ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception
```

Controller 接口的实现类可以通过 handleRequest 方法传递的参数访问对应请求的 HTTPServletRequest 和 HttpServletResponse 对象，处理完业务请求后，必须返回一个包含模型对象和视图路径的 ModelAndView 对象。

> 注意
>
> Controller 接口的实现类只能处理一个单一请求动作，基于注解的控制器（Spring2.5后）可以支持同时处理多个请求动作，且无须实现任何接口，更加灵活。

### 示例：第一个 Spring MVC 应用

1. 增加 Spring 的支持
2. 配置前端控制器



