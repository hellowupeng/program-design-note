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
3. 配置 Spring MVC 的 Controller
4. Controller 类的实现

   > 提示  
   > Spring MVC 建议把所有的视图页面存放在 WEB-INF 文件夹下，这样可以保护视图页面，避免直接向视图页面发送请求。

5. View 页面

6. 测试应用

### 示例：基于注解的控制器

所有步骤和”第一个 Spring MVC 应用“示例一样，只是修改两个地方。

1. **Controller 类的实现**  
   org.springframework.stereotype.Controller 注释类型用于指示 Spring 类的实例是一个控制器。org.springframework.web.bind.annotation.RequestMapping 注释类型用来映射一个请求和请求的方法。value="/hello" 表示请求由 hello 方法进行处理。方法返回一个包含视图名或视图名和模型的 ModelAndView 对象。

2. **修改 Spring MVC 的配置文件**![](/assets/修改 Spring MVC 的配置文件.png)

   由于使用了注解类型，因此不需要再在配置文件中使用 XML 描述 Bean。Spring 使用扫描机制查找应用程序中所有基于注解的控制器类。&lt;context:component-scan base-package="org.fkit.controller" /&gt; 指定需要 Spring 扫描 org.fkit.controller 包及其子包下面的所有 Java 文件。

   此处还配置了一个 annotation 类型的处理器映射器 RequestMappingHandlerMapping，他根据请求查找映射；同时配置了 annotation 类型的处理器适配器 RequestMappingHandlerAdapter，来完成对 HelloController 类的 @RequestMapping 标注方法的调用；最后配置了视图解析器 InternalResourceViewResolver 来解析视图，将 View 呈现给用户。

3. **测试**



