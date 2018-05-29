# 6.2 数据转换

在 Java 语言 中， 在 java. beans 包 中 提供 了 一个 ProperyEditor 接口 来 进行 数据 转换。 ProperyEditor 的 核心 功能 是将 一个 字符串 转换 为 一个 Java 对象， 以便 根据 界面 的 输入 或 配置文件 中的 配置 字符串 构造 出 一个 Java 对象。

但是 ProperyEditor 存在 以下 不足： 

* 只能 用于 字符串 和 Java 对象 的 转换， 不适 用于 任意 两个 Java 类型 直接 的 转换。 
* 对 源 对象 及 目标 对象 所在 的 上下文 信息（ 如 注解 等） 不 敏感， 在 类型 转换 时不 能 利用 这些 上下文 信息 实施 高级 转换 逻辑。

Spring 从 3. 0 开始， 添加 了 一个 通用 的 类型 转换 模块， 该类 型 转换 模块 位于 org. springframework. core. convert 包 中。 Spring 希望 用 这个 类型 转换 体系 替换 Java 标准 的 ProperyEditor 接口。



