# 3.12 @ModelAttribute注解

org. springframework. web. bind. annotation. ModelAttribute 注解 用于 将 请求 参数 绑 定 到 对象。

@ModelAttribute 注解 只 支持 一个 属性 value， 类型 为 String， 表示 绑 定的 属性 名称。

> 提示
>
> 被@ ModelAttribute 注释 的 方法 会在 Controller 每个 方法 执行 前 被 执行， 因此 在 一个 Controller 被 映射 到 多个 URL 时， 要 谨慎 使用。



