# 6.1 数据绑定流程

Spring MVC 通过 反射 机制 对 目标 处理 方法 的 签名 进行 分析， 并将 请求 消息 绑 定 到处 理 方法 的 参数 上。数据 绑 定的 核心 部件 是 DataBinder， 其 运行机制 如图所示：

![](/assets/数据绑定运行机制.png)

Spring MVC 框架 将 ServletRequest 对象 及 处理 方法 的 参数 对象 实例 传递 给 DataBinder， DataBinder 调用 装配 在 Spring Web 上下文 中的 ConversionService 组件 进行 数据 类型 转换、 数据 格式化 工作， 并将 ServletRequest 中的 消息 填充 到 参数 对象 中。 然后 再 调用 Validator 组件 对 已经 绑 定了 请求 消息 数据 的 参数 对象 进行 数据 合法性 校验， 并 最终 生成 数据 绑 定结 果 BindingResult 对象。 BindingResult 包含 已完成 数据 绑 定的 参数 对象， 还 包含 相应 的 校验 错误 对象， Spring MVC 抽取 BindingResult 中的 参数 对象 及 校验 错误 对象， 将 它们 赋 给 处理 方法 的 相应 参数。





