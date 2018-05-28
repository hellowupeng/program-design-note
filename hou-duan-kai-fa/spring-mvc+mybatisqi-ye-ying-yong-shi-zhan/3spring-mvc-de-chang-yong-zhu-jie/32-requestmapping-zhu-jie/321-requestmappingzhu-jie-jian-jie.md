# 3.2.1 @RequestMapping注解简介

开发者 需要 在 控制器 内部 为 每一个 请求 动作 开发 相应 的 处理 方法。 org. springframework. web. bind. annotation. RequestMapping 注解 指示 Spring 用 哪一个 类 或 方法 来处 理 请求 动作， 该 注解 可 用于 类 或 方法。

> 提示
>
> @RequestMapping 注解 虽然 也 包含 在 org. springframework. web. bind. annotation 里面， 但是 严格 来说， 它 并不 属于 参数 绑 定 注解。

@RequestMapping 注解 可以 用来 注释 一个 控制器 类， 在 这种 情况下， 所有 方法 都将 映射 为 相对于 类 级别 的 请求， 表示 该 控制器 处理 的 所有 请求 都被 映射 到 value 属性 所 指示 的 路径 下。

![](/assets/@RequestMapping修饰控制器类.png)

此时 方法 被 映射 到 如下 请求 URL：

![](/assets/@RequestMapping修饰控制器方法映射URL.png)

@RequestMapping注解支持的属性：![](/assets/@RequestMapping支持的属性.png)

