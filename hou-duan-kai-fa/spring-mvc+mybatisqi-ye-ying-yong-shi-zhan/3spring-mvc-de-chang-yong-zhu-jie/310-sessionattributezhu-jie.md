# 3.10 @SessionAttribute注解

org. springframework. web. bind. annotation. SessionAttribute 注解 用于 访问 由 请求 处理 方法、 过滤器 或 拦截 器 创建 的、 预先 存 在于 session 作用域 中的 属性， 将 该 属性 转换 到 目标 方法 的 参数。

@SessionAttribute注解支持的属性：![](/assets/@SessionAttribute注解支持的属性.png)@SessionAttribute 注解示例：

```java
@RequestMapping(value="/attributeTest")
public void attributeTest(
        @SessionAttribute(value="username") String username)
```

以上 代码 会 自动 将 session 作用域 中 名为 username 的 属 性的 值 设置 到 username 参数 上。



