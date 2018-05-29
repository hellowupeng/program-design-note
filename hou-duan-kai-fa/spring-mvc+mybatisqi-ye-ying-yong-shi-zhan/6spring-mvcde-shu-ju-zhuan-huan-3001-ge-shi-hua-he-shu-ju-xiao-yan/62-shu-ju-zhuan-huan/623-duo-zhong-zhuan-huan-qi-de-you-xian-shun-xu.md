# 6.2.3 多种转换器的优先顺序

对于 同一个 类型 的 对象 来说， 如果 既在 ConversionService 中 装配 了 自定义 的 转换器， 又 通过 WebBindingInitializer 接口 装配 了 全局 的 自定义 编辑器， 同时 还在 控制器 中 通过@ InitBinder 装配 了 自定义 的 编辑器， 此时 Spring MVC 将 按照 以下 的 优先 顺序 查找 对应 的 编辑器：

1. 查询 通过@ InitBinder 装配 的 自定义 编辑器。
2. 查询 通过 ConversionService 装配 的 自定义 转换器。
3. 查询 通过 WebBindingInitializer 接口 装配 的 全局 自定义 编辑器。



