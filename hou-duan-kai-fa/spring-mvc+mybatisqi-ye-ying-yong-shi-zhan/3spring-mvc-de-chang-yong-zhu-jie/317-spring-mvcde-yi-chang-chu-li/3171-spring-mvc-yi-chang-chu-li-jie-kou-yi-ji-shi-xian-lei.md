# 3.17.1 Spring MVC 异常处理接口以及实现类

HandlerExceptionResolver 是 Spring 3. 0 之后 新增 的 一个 重要 接口， 负责 Spring MVC 的 异常 处理。

SimpleMappingExceptionResolver 继承 自 AbstractHandlerExceptionResolver 抽象 类， 是一 个 根据 配置 来 解析 异常 的 类， 包括 异常 类型、 默认 的 错误 视图、 默认 的 响应 码 及 异常 映射 等 配置 属性。

因此， 在 Spring MVC 中进 行 异常 处理， 如果 选择 XML 配置， 则 使用 SimpleMapping- ExceptionResolver 类； 如果 使用@ ExceptionHandler 注解 和@ ControllerAdvice 注解， 则由 ExceptionHandlerExceptionResolver 类 进行 处理。

