# 9.2.2 properties 属性

这些 属性 都是 可 外部 配置 且 可动 态 替换 的， 既可以 在 典型的 Java 属性 文件 中 配置， 亦可 通过 properties 元素 的 子 元素 来 传递。

可以 在 CLASSPATH 中 增加 一个 db. properties 的 Java 属性 文件。![](/assets/db.properties.png)在 配置文件 中 配置 ＜ properties.../ ＞ 属性：

```
＜ properties resource=＂ db. properties＂/ ＞
```

其中 的 属性 就可 以在 整个 配置文件 中 使用 来 替换 需要 动态 配置 的 属性 值。![](/assets/配置文件中使用来替换需要动态配置属性值.png)

