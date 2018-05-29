# 3.9 @RequestAttribute 注解

org. springframework. web. bind. annotation. RequestAttribute 注解 用于 访问 由 请求 处理 方法、 过滤器 或 拦截 器 创建 的、 预先 存 在于 request 作用域 中的 属性， 将 该 属性 转换 到 目标 方法 的 参数。

@RequestAttribute注解支持的属性：![](/assets/@RequestAttribute注解支持的属性.png)@RequestAttribute 注解 示例：

```java
@RequestMapping(value="/attributeTest")
public void attributeTest(
        @RequestAttribute(value="username") String username)
```

以上 代码 会 自动 将 request 作用域 中 名为 username 的 属 性的 值 设置 到 username 参数上。



