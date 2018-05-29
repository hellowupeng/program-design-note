# 3.8 @CookieValue 注解

org. springframework. web. bind. annotation. CookieValue 注解 用于 将 请求 的 Cookie 数据 映射 到 功能 处理 方法 的 参数 上。

@CookieValue注解支持的属性：![](/assets/@CookieValue注解支持的属性.png)

@CookieValue 注解 示例：

```java
@RequestMapping(value="/cookieValueTest")
public void cookieValueTest(
        @CookieValue(vlaue="JSESSIONID", defaultValue="") String sessionId)
```

以上 配置 会 自动 将 Cookie 中 JSESSIONID 的 值 设置 到 sessionId 参数 上，defaultValue 表示 Cookie 中 没有 JSESSIONID 时 默 认为 空。



