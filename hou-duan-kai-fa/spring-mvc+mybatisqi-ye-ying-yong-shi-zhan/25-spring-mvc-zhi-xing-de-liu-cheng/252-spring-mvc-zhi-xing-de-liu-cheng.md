# 2.5.2 Spring MVC 执行的流程

一次 请求 → 响应 的 完整 流程:

![](/assets/请求->响应流程.png)

Spring MVC 请求 → 响应 的 完整 工作 流程 如下：

1. 用户 向 服务器 发送 请求， 请求 被 Spring 的 前端 控制器 DispatcherServlet 截获。
2. DispatcherServlet 对 请求 URL（ 统一 资源 定位 符） 进行 解析， 得到 URI（ 请求 资源 标识符）。 然后 根据 该 URI， 调用 HandlerMapping 获得 该 Handler 配置 的 所有 相关 的 对象， 包括 Handler 对象 以及 Handler 对象 对应 的 拦截 器， 这些 对象 会被 封装 到 一个 HandlerExecutionChain 对象 当中 返回。
3. DispatcherServlet 根据 获得 的 Handler， 选择 一个 合适 的 HandlerAdapter。
   HandlerAdapter 的 设计 符合 面向 对象 中的 单一 职责 原则， 代码 架构 清晰， 便于 维护， 最重要的 是， 代码 可 复 用性 高。 HandlerAdapter 会被 用于 处理 多种 Handler， 调用 Handler 实际 处理 请求 的 方法， 例如 hello 方法。
4. 提取 请求 中的 模型 数据， 开始 执行 Handler（ Controller）。 在 填充 Handler 的 入 参 过程中， 根据 配置， Spring 将 帮你 做 一些 额外 的 工作。
   **消息转换**。将 请求 消息（ 如 JSON、 XML 等 数据） 转换 成 一个 对象， 将对 象 转换 为 指定 的 响应 信息。
   **数据转换**。对 请求 消息 进行 数据 转换， 如 String 转换 成 Integer、 Double 等。
   **数据格式化**。对 请求 消息 进行 数据 格式化， 如 将 字符串 转换 成 格式化 数字 或 格式化格式化 日期 等。
   **数据验证**。验证 数据 的 有效性（ 长度、 格式 等）， 验证 结果 存储 到 BindingResult 或 Error 中。
5. Handler 执行 完成 后， 向 DispatcherServlet 返回 一个 ModelAndView 对象， ModelAndView 对象 中 应该 包含 视图 名 或 视图 名 和 模型。
6. 根据 返回 的 ModelAndView 对象， 选择 一个 合适 的 ViewResolver（ 视图 解析 器） 返回 给 DispatcherServlet。
7. ViewResolver 结合 Model 和 View 来 渲染 视图。
8. 将 视图 渲染 结果 返回 给 客户 端。



