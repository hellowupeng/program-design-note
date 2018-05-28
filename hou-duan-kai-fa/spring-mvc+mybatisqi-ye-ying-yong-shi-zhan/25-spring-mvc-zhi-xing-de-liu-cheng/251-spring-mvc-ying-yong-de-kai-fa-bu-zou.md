# 2.5.1 Spring MVC 应用的开发步骤

1. 在 web. xml 文件 中 定义 前端 控制器 DispatcherServlet 来 拦截 用户 请求。
   开发 Spring MVC 应 用的 第 1 步 就是 在 web. xml 文件 中 增加 如下 配置 片段：![](/assets/定义 Spring MVC 的前端控制器.png)

2. 如果 需要 以 POST 方式 提交 请求， 则 定义 包含 表单 数据 的 JSP 页面。 如果 仅仅 只是 以 GET 方式 发送 请求， 则 无须 经过 这 一步。

3. 定义 处理 用户 请求 的 Handle 类， 可以 实现 Controller 接口 或使 用@ Controller 注解。 这一 步 也是 所有 MVC 框架 中 必不可少 的， 因为 这个 DispatcherServlet 就是 MVC 中的 C， 也就是 前端 控制器， 该 控制器 负责 接收 请求， 并将 请求 分发 给 对应 的 Handle， 即 实现 Controller 接口 的 Java 类， 而 该 Java 类 负责 调用 后台 业务 逻辑 代码 来处 理 请求。

   > 提示
   >
   > MVC 框架 的 底层 机制 是： 前端 Servlet 接收 到 用户 请求 后， 通常 会对 用户 请求 进行 简单 预处理， 例如 解析、 封装 参数 等， 然后 通过 反射 来 创建 Controller 实例， 并 调用 Controller 的 指定 方法（ 实现 Controller 接口 的 是 handleRequest 方法， 而使 用 基于 注解 的 控制器 可以 是 任意 方法） 来处 理 用户 请求。
   >
   > 当 Servlet 拦截 用户 请求 后， 它 如何 知道 创建 哪个 Controller 接口 的 实例 呢？
   >
   > 1.利用 xml 配置文件： 例如 在 xml 配置文件 中 描述 hello 请求 对应 使用 HelloController 类。 这就 可以 让 MVC 框架 知道 创建 哪个 Controller 接口 的 实例。
   >
   > 2.利用 xml 配置文件： 例如 在 xml 配置文件 中 描述 hello 请求 对应 使用 HelloController 类。 这就 可以 让 MVC 框架 知道 创建 哪个 Controller 接口 的 实例。

   在 Spring MVC 框架 中， 控制器 实际上 由 两个 部分 组成， 即 拦截 所有 用户 请求 和 处理 请求 的 通用 代码 都由 前端 控制器 DispatcherServlet 完成， 而 实际 的 业务 控制（ 诸如 调用 后台 业务 逻辑 代码， 返回 处理 结果 等） 则由 Controller 处理。

4. 配置 Handle。 Java 领域 的 绝大部分 MVC 框架 都 非常 喜欢 使用 xml 文件 来 进行 配置 管理， 这 在 以前 是一 种 思维 定 势。 即 配置 哪个 请求 对应 哪个 Controller， 从而 让 前端 控制器 根据 该 配置 来 创建 合适 的 Controller 实例， 并 调用 该 Controller 的 业务 控制 方法。

   采用 如下 代码 片段 来 配置 Handle：![](/assets/配置handle，映射/hello请求.png)在 Spring 2. 5 之后， 推荐 使用 注解 来 配置 Handle：

   ![](/assets/使用注解来配置 Handle.png)

   上面 的 配置 片段 指定 如果 用户 请求 URL 为 hello， 则 使用 org. fkit. controller. HelloController 来 处理。 现在 几乎 所有 的 MVC 框架 都 使用“ 约定 优于 配置” 的 思想， 也就是 采用 约定 方式 来 规定 用户 请求 地址 和 Handle 之间 的 对应 关系。

5. 编写 视图 资源。 当 Handle 处理 用户 请求 结束 后， 通常 会 返回 一个 ModelAndView 对象， 该 对象 中 应该 包含 返回 的 视图 名 或 视图 名 和 模型， 这个 视图 名 就 代表 需要 显示 的 物理 视图 资源。 如果 Handle 需要 把 一些 数据 传给 视图 资源， 则 可以 通过 模型 对象。



