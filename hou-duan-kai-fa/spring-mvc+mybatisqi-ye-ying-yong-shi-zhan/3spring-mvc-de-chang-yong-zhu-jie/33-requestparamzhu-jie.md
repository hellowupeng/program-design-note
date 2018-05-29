# 3.3 @RequestParam注解

org. springframework. web. bind. annotation. RequestParam 注解 用于 将 指定 的 请求 参数 赋值 给 方法 中的 形 参。

@RequestParam 注解属性：![](/assets/@RequestParam 注解属性.png)请求 处理 方法 参数 的 可选 类型 为 Java 基本 数据 类型 和 String。![](/assets/@RequestParam 注解示例.png)![](/assets/@RequestParam注解假设请求.png)

以上 代码 会 将 请求 中的 loginname 参数 的 值“ jack” 赋 给 loginname 变量， password 参数 的 值“ 123456” 赋 给 password 变量。

UserController 类：

1. UserController 类 使用 了@ Controller 注解， 是 一个 控制器 类。
2. 在 UserController 类 上面 使用 了@ RequestMapping（ value=＂/ user＂） 注解， 表示 该 控制器 处理 的 所有 请求 都被 映射 到 user 路径 下。
3. registerForm 方法 使用 了@ GetMapping（ value=＂/ register＂） 注解， 表示 该 方法 映射 的 请求 为 http：// localhost： 8080/ RequestMappingTest/ user/ register， 并且 只 支持 GET 请求。 该 方法 返回 字符串“ registerForm”， 参考 springmvc- config. xml 中的 配置 信息， 可以 知道 该 方法 只是 跳 转到 registerForm. jsp 注册 页面。
4. （5） register 方法 使用 了@ PostMapping（ value=＂/ register＂） 注解， 表示 该 方法 映射 的 请求 为 http：// localhost： 8080/ RequestMappingTest/ user/ register， 并且 只 支持 POST 请求。 该 方法 使用@ RequestParam 注解 将 指定 的 请求 参数 赋值 给 方法 中的 形 参， 之后 创建 了 一个 User 对象 保存 用户 传递 的 注册 信息， 最后 将 User 对象 存储 到 userList 集合 中， 然后 登录 页面 就可以 到 userList 集合 中进 行 用户 登录 业务 逻辑 的 判断。 该 方法 返回 字符串 loginForm， 并 跳 转到 loginForm. jsp 登录 页面。



