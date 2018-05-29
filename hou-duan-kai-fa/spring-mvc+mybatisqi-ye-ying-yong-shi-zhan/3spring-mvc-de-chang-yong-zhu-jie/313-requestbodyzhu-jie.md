# 3.13 @RequestBody注解

org. springframework. web. bind. annotation. RequestBody 注解 常用 来 处理 Content- Type： 不是 application/ x- www- form- urlencoded 编码 的 内容， 例如 application/ json、 application/ xml 等。等。@ RequestBody 注解 通过 使用 HandlerAdapter 配置 的 HttpMessageConverters 来 解析 JSON 或 XML 数据， 然后 绑 定 到 相应 的 Bean 上。





