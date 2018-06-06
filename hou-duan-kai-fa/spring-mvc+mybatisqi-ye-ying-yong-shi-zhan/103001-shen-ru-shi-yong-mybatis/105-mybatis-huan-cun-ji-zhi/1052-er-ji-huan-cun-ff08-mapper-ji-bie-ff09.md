# 10.5.2 二级缓存（mapper级别）

二级 缓存 是 mapper 级别 的 缓存。 使用 二级 缓存 时， 多个 SqlSession 使用 同一个 mapper 的 SQL 语句 去 操作 数据库， 得到 的 数据 会 存在 二级 缓存 区域， 它 同样是 使用 HashMap 进行 数据 存储 的。 相比 一级 缓存 SqlSession， 二级 缓存 的 范围 更大， 多个SqlSession 可以 共享 二级 缓存 中的 数据， 二级 缓存 是 跨 SqlSession 的。

二级 缓存 是 多个 SqlSession 共享 的， 其 作用域 是 mapper 的 同一个 namespace。 不同 的 SqlSession 两次 执行 相同 的 namespace 下 的 SQL 语句， 且 向 SQL 中 传递 的 参数 也 相同， 即 最终 执行 相同 的 SQL 语句， 当 第一个 SqlSession 调用 close（） 方法 关闭 一级 缓存 时， 第一次 从 数据库 中 查询 到 的 数据 会被 保存 到 二级 缓存， 第二次 查询 时会 从 二级 缓存 中 获取 数据， 不再 去 底层 数据库 查询， 从而 提高 查询 效率。

MyBatis 默认 没有 开启 二级 缓存， 需要 在 setting 全局 参数 中 配置 开启 二级 缓存。

src/mybatis-config.xml

```
<settings>
    <!-- 开启二级缓存 --!>
    <setting name="cacheEnabled" value="true"/>
</settings>
```

cacheEnabled 的 value 为 true 时 表示 在此 配置文件 下 开启 二级 缓存， 该 属性 默认 为 false。 MyBatis 的 二级 缓存 是 和 命名 空间 绑 定的， 即 二级 缓存 需要 配置 在 Mapper. xml 映射 文件 或者 Mapper 接口 中。 在 映射 文件 中， 命名 空间 就是 XML 根 节点 mapper 的 namespace 属性。 在 Mapper 接口 中， 命名 空间 就是 接口 的 全 限定 名称。

开启 默认 的 二级 缓存 代码 如下：&lt;cache /&gt;

默认 的 二级 缓存 会有 如下 作用：

映射 语句 文件 中的 所有 SELECT 语句 将会 被 缓存。 映射 语句 文件 中的 所有 INSERT、 UPDATE、 DELETE 语句 会 刷新 缓存。 缓存 会 使用 Least Recently Used（ LRU 最近 最少 使用） 策略 来 收回。 根据 时间表（ 如 no Flush Interval， 没有 刷新 间隔）， 缓存 不会 以 任何 时间 顺序 来 刷新。 缓存 会 存储 集合 或 对象（ 无论 查询 方法 返回 什么 类型 的 值） 的 1024 个 引用。 缓存 会被 视为 read/ write（ 可读/ 可 写） 的， 这 意味着 对象 检索 不是 共享 的， 而且 可以 安全 地 被 调用 者 修改， 而 不干 扰 其他 调 用者 或 线程 所做 的 潜在 修改。

＜ cache/ ＞ 元素 中 所有这些 行为 都可以 通过 cache 元素 的 属性 来 进行 修改。

src/org/fkit/mapper/UserMapper.xml

```
<!-- 开启当前 mapper 的 namespace 下的二级缓存 --!>
<cache eviction="LRU" flushInterval="6000" size="512" readonly="true"/>
```

以上 配置 创建 了 一个 LRU 缓存， 并 每隔 60s 刷新， 最大 存储 512 个 对象， 而且 返回 的 对象 被认为 是 只读 的。

cache 元素 用来 开启 当前 mapper 的 namespace 下 的 二级 缓存。 该 元素 的 属性:

* flushInterval。刷新 间隔。 可以 被 设置 为 任意 的 正 整数， 而且 它们 代表 一个 合理 的 毫秒 形式 的 时间 段。 默认 情况下 是 不 设置， 也就是 没有 刷新 间隔， 缓存 仅仅 调用 语句 时 刷新。
* size。缓存 数目。 可以 被 设置 为 任意 正 整数， 要 记住 你 缓存 的 对象 数目 和你 运行 环境 的 可用 内存 资源 数目。 默认值 是 1024。
* readonly。只读。 该 属性 可以 被 设置 为 true 或 false。 只读 的 缓存 会 给 所有 调用 者 返回 缓存 对象 的 相同 实例， 因此 这些 对象 不能 被 修改。 这 提供 了 很重 要的 性能 优势。 可读 写的 缓存 会 返回 缓存 对象 的 拷贝（ 通过 序列 化）。 这种 方式 会 慢 一些， 但是 安全， 因此 默认 是 false。
* eviction。收回 策略， 默认 为 LRU。 有如 下 几种：
  * LRU。最近 最少 使用 的 策略， 移 除 最长 时间 不被 使用 的 对象。
  * FIFO。先进 先出 策略， 按 对象 进入 缓存 的 顺序 来 移 除 它们。
  * SOFT。软 引用 策略， 移 除 基于 垃圾 回收 器 状态 和 软 引用 规则 的 对象。
  * WEAK。弱 引用 策略， 更 积 极地 移 除 基于 垃圾 收集 器 状态 和 弱 引用 规则 的 对象。

> 使用 二级 缓存 时， 与 查询 结果 映射 的 Java 对象 必须 实现 java. io. Serializable 接口 的 序列 化 和 反 序列 化 操作， 如果 存在 父 类， 其 成员 都 需要 实现 序列 化 接口。 实现 序列 化 接口 是 为了 对 缓存 数据 进行 序列 化 和 反 序列 化 操作， 因为 二级 缓存 数据 存储 介质 多种多样， 不一定 在 内存， 有可能 是 硬盘 或者 远程 服务器。





