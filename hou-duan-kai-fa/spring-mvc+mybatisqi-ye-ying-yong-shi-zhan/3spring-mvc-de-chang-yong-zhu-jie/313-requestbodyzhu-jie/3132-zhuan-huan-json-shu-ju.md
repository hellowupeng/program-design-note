# 3.13.2 转换 JSON 数据

当前台页面使用 GET / POST 方式提交数据时，数据编码格式由请求头的 ContentType 指定，分为以下情况：

* application/x-www-form-urlencoded。这种 情况 的 数据@ RequestParam、@ ModelAttribute 也可以 处理， 并且 很 方便， 当然@ RequestBody 也能 处理。
* multipart/form-data。@RequestBody 不能 处理 这种 格式 的 数据。
* application/x-www-form-urlencoded。必须 使用@ RequestBody 来 处理。



> 提示
>
> 在 引入 静态 文件， 例如 js 时， 需要 加入 ＜ mvc： default- servlet- handler/ ＞ 从而 使用 默认 的 Servlet 来 响应 静态 文件。 如果 没有 加入 该 配置， 则 执行 时 页面 会 报 404 错误， 而 控制台 会 提出 警告：
>
> No mapping found for HTTP request with URI \[/ResponseBodyTest/ js/ jquery- 1. 11. 0. min. js\] in DispatcherServlet with name' springmvc'





