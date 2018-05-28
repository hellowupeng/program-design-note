# 3.1 @Controller注解

org. springframework. stereotype. Controller 注解 用于 指示 Spring 类 的 实例 是 一个 控制器， 使用@ Controller 注解 的 类 不需要 继承 特 定的 父 类 或者 实现 特定 的 接口， 相对 之前 的 版本 实现 Controller 接口 变得 更加 简单。 而且 Controller 接口 的 实现 类 只能 处理 一个 单一 请求 动作， 而@ Controller 注解 的 控制器 可以 支持 同时 处理 多个 请求 动作， 更加 灵活。

@Controller 用于 标记 一个 类， 使用 它 标 记的 类 就是 一个 Spring MVC Controller 对象， 即 一个 控制器 类。 Spring 使用 扫描 机制 查找 应用 程序 中 所有 基于 注解 的 控制器 类。 分发 处理器 会 扫描 使 用了 该 注解 的 类 的 方法， 并 检测 该 方法 是否 使用 了@ RequestMapping 注解， 而使 用@ RequestMapping 注解 的 方法 才是 真正 处理 请求 的 处理器。 为了 保证 Spring 能 找到 控制器， 需要 完成 两件 事情：

1. 在 Spring MVC 的 配置文件 的 头 文件 中 引入 spring- context。
2. 使用 ＜ context： component- scan/ ＞ 元素， 该 元素 的 功能 为： 启动 包 扫描 功能， 以便 注册 带有@ Controller、@ Service、@ repository、@ Component 等 注解 的 类 成为 Spring 的 Bean。 base- package 属性 指定 了 需要 扫描 的 类 包， 类 包 及其 递归 子 包 中 所 有的 类 都会 被 处理。
   配置文件：
   ![](/assets/Spring MVC 配置文件.png)
   应该 将 所有 控制器 类 都 放在 基本 包 下， 并且 指定 扫描 该 包， 即 org. fkit. controller， 而 不应该 指定 扫描 org. fkit 包， 以免 Spring MVC 扫描 了 无关 的 包。

![](/assets/springmvc-config.png)springmvc- config. xml 文件 配置 信息:

1. 由于 使用 了@ Controller 注解， 因此 不需要 再 在 配置文件 中 使用 XML 描述 Bean。 ＜ context： component- scan base- package=＂ org. fkit. controller＂/ ＞ 指定 需要 Spring 扫描 org. fkit. controller 包 及其 子 包 下面 的 所有 java 文件。
2. ＜ mvc： annotation- driven ＞ 是一 种 简写 形式， 可以 让 初学者 快速 应用 默认 配置 方案。 ＜ mvc： annotation- driven ＞ 会 自 动 注 册 RequestMappingHandlerMapping 与 Request MappingHandlerAdapter 两个 Bean， 这是 Spring MVC 为@ Controllers 分发 请求 所 必需 的， 并 提供 了 数据 绑 定 支持、@ NumberFormatannotation 支持、@ DateTimeFormat 支持、@ Valid 支持、 读写 XML 的 支持（ JAXB） 和 读写 JSON 的 支持（ 默认 为 Jackson） 等 功能。 本例 处理 Ajax 请求 时， 就 使用 到了 对 JSON 的 支持 功能。
3. ＜ mvc： default- servlet- handler/ ＞ 是 Spring MVC 的 静态 资源 处理器， 在 web. xml 中， 如果 将 DispatcherServlet 请求 映射 配置 为“/”， 则 Spring MVC 将 捕获 Web 容器 所有 的 请求， 包括 静态 资源 的 请求， 而 引入 类似 ＜ script type=＂ text/ javascript＂ src=＂ js/ jquery- 1. 11. 0. min. js＂/ ＞ 这种 静态 资源 文件 的 时候， DispatcherServlet 会 将“/” 看成 请求请求 路径， 找 不到 它的 时候 会 导致 提示 404 错误。 当 在 springmvc- config. xml 中 配置 ＜ mvc： default- servlet- handler/ ＞ 后， 会在 Spring MVC 上下 文中 定义 一个 org. springframework. web. servlet. resource. DefaultServletHttpRequestHandler， 它 就 像 一个 检查员， 对 进入 DispatcherServlet 的 URL 进行 筛 查， 如果 发现 是 静态 资源 的 请求， 就 将 该 请求 转由 Web 应用 服务器 默认 的 Servlet 处理； 如果 不是 静态 资源 的 请求， 才 由 DispatcherServlet 继续 处理。
4. 最后 配置 了 视图 解析 器 InternalResourceViewResolver 来 解析 视图， 将 View 呈现 给用户。 视图 解析 器 中 配置 的 prefix 属性 表示 视图 的 前缀， suffix 表示 视图 的 后缀， 返回 的 视图 字符串 是“ helloWorld”， 经过 视图 解析 器 解析 之后， 视图 的 完整 路径 为：/ WEB- INF/ content/ helloWorld. jsp。 需要 注意 的 是， 此处 没有 配置 处理器 映射 器 和 处理器 适配器， 当 用户 没有 配置 这 两项 时， Spring 会使 用 默认 的 处理器 映射 器 和 处理器 适配器 处理 请求。

此外， 还需 要在 web. xml 文件 中 配置 Spring MVC 的 前端 控制器 DispatcherServlet。

* **处理请求参数和内容部分的注解**：注解：@ RequestMapping、@ RequestParam、@ GetMapping、@ PostMapping、@ PutMapping、@ DeleteMapping、@ PatchMapping、@ RequestBody、@ ResponseBody、@ RequestPart、@ RestController。
* **处理请求URL部分的注解**：@ PathVariable、@ MatrixVariable、@ CrossOrigin。
* **处理请求头部分的注解**：@ RequestHeader、@ CookieValue。
* **处理属性类型的注解**：@ RequestAttribute、@ SessionAttribute、@ SessionAttributes、@ ModelAttribute。
* **处理异常类型的注解**：@ ExceptionHandler、@ ControllerAdvice、@ RestControllerAdvice、@ ResponseStatus。
* **绑定表单数据的注解**：@ InitBinder。



