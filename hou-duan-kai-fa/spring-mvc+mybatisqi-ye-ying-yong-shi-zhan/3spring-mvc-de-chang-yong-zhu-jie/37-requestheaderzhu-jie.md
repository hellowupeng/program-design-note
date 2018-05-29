# 3.7 @RequestHeader注解

org. springframework. web. bind. annotation. RequestHeader 注解 用于 将 请求 的 头 信息 数据 映射 到 功能 处理 方法 的 参数 上。

@RequestHeader注解支持的属性：![](/assets/@RequestHeader支持的属性.png)@RequestHeader注解示例：

```java
public void requestHeaderTest(
        @RequestHeader("User-Agent") String userAgent,
        @RequestHeader(value="Accept") String[] accepts) {}
```

以上 配置 自动 将 请求 头“ User- Agent” 的 值 赋 到 userAgent 变量 上， 并将 请求 头“ Accept” 的 值 赋 到 accepts 变量 上。

