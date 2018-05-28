# 3.3 @RequestParam注解

org. springframework. web. bind. annotation. RequestParam 注解 用于 将 指定 的 请求 参数 赋值 给 方法 中的 形 参。

@RequestParam 注解属性：![](/assets/@RequestParam 注解属性.png)请求 处理 方法 参数 的 可选 类型 为 Java 基本 数据 类型 和 String。![](/assets/@RequestParam 注解示例.png)![](/assets/@RequestParam注解假设请求.png)

以上 代码 会 将 请求 中的 loginname 参数 的 值“ jack” 赋 给 loginname 变量， password 参数 的 值“ 123456” 赋 给 password 变量。

