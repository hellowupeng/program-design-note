# 3.5 @MatrixVariable 注解

org. springframework. web. bind. annotation. MatrixVariable 注解 拓展 了 URL 请求 地址 的 功能。使用@ MatrixVariable 注解 时 多个 变量 可以 使用；（ 分号） 分隔， 该 注解 允许 开发者 进行 多条 件 组合 查询。

@MatrixVariable 注解支持的属性：![](/assets/@Matrix注解支持的属性.png)@MatrixVariable 注解示例：![](/assets/@MatrixVariable注解示例.png)是，@ MatrixVariable 注解 功能 在 Spring MVC 中 默认 是 不 启用 的， 启用 它 需要 设置 enable- matrix- variables=＂ true＂。

![](/assets/启用@MatrixVariable注解示例.png)

