# 3.14 @ResponseBody 注解

org. springframework. web. bind. annotation. ResponseBody 注解 用于 将 Controller 的 请求 处理 方法 返回 的 对象， 通过 适当 的 HttpMessageConverter 转换 为 指定 格式 后， 写入 到 Response 对象 的 body 数据区。 当 返回 的 数据 不是 HTML 标签 的 页面， 而是 其他 某种 格式 的 数据 时（ 如 JSON、 XML 等） 使用 它。![](/assets/@ResponseBody 返回 JSON格式数据实例.png)

