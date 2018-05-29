# 3.6 @CrossOrigin 注解

org. springframework. web. bind. annotation. CrossOrigin 注解 用于 在 Spring MVC 中 处理 跨 域 请求。

@CrossOrigin支持的属性：![](/assets/@CrossOrigin支持的属性.png)@CrossOrigin 注解 示例：

表示 CrossOriginController 控制器 的 所有 方法 可以 处理 所有 域 上 的 请求：

```java
@CrossOrigin(maxAge=3600)
@Controller
public class CrossOriginController
```

表示 CrossOriginController 控制器 的 所有 方法 可以 处理 http：// www. fkit. org 域 上 的 请求：

```java
@CrossOrigin(origins="http://www.fkit.org",maxAge=3600)
@Controller
public class CrossOriginController
```

CrossOriginController 类 上有@ CrossOrigin 注解， login 方法 上 也有@ CrossOrigin 注解， Spring MVC 会合 并 两个 注解 的 属性 一起 使用。

```java
@CrossOrigin(maxAge=3600)
@Controller
public class CrossOriginController{
    @CrossOrigin(origins="http://www.fkit.org")
    @GetMapping(value="/login")
    public String login() {
        // ...
    }
}
```



