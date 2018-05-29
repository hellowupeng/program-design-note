# 3.17.5 @RestControllerAdvice注解

org. springframework. web. bind. annotation. RestController 注解 本身 使用@ ControllerAdvice 和@ ResponseBody 注解。 使用 了@ RestControllerAdvice 注解 的 类 会被 看作 一个 ControllerAdvice， 而 该类 中 所有 使用@ ExceptionHandler 注解 的 方法 都 默认 使用@ ResponseBody 注解。



