# 2.4 详解 DispatcherServlet

DispatcherServlet 源代码：![](/assets/org/springframework/web/servlet/DispatcherServlet.png)org.springframework.web.servlet 路径下的 DispatcherServlet.properties 配置文件，指定了 DispatcherServlet 所使用的默认组件：![](/assets/dispatcherServlet.properties.png)DispatcherServlet 装配每种组件的细节：

**本地化解析器。**只允许一个实例。

* 查找名为 localeResolver、类型为.LocaleResolver 的 Bean 作为该类型组件。
* 如果没有找到，则使用默认的实现类 AcceptHeaderLocaleResolver 作为该类型组件。

主题解析器。只允许一个实例

* 查找名为 themeResolver、类型为 ThemeResolver 的 Bean 作为该类型组件。
* 如果没有找到，则使用默认的实现类 FixedThemeResolver 作为该类型组件。

处理器映射器。允许多个实例。

* 如果 detectAllHandlerMappings 的属性为 true（默认为 true），则根据类型匹配机制查找上下文以及Spring容器中所有类型为 HandlerMapping 的 Bean，将它们作为该类型组件。
* 如果 detectAllHandlerMappings 的属性为 false，则查找名为 handlerMapping、类型为 HandlerMapping 的 Bean 作为该类型组件。
* 如果通过以上两种方式都没有找到，则使用 BeanNameUrlHandlerMapping 实现类创建该类型的组件。

处理器适配器。允许多个实例

* 如果detectAllHandlerAdapters 的属性为true（默认为true），则根据类型匹配机制查找上下文以及Spring容器中所有类型为HandlerAdapter的Bean，将它们作为该类型的组件。
* 如果detectAllHandlerAdapters的属性为false，则查找名为handlerAdapter、类型为HandlerAdapter的Bean作为该类型组件。
* 如果通过以上两种方式都没有找到，则使用DispatcherServlet.Properties 配置文件中指定的三个实现类分别创建一个适配器，并将其添加到适配器列表中。

处理器异常解析器。允许多个实例

* 如果detectAllHandlerExceptionResolver的属性为true（默认为true），则根据类型匹配机制查找上下文以及Spring容器中所有类型为HandlerExceptionResolver的Bean，将它们作为该类型组件。
* 如果detectAllHandlerExceptionResolver的属性为false，则查找名为handlerException-Resolver、类型为HandlerExceptionResolver的Bean作为该类型组件。
* 如果通过以上两种方式都没有找到，则查找DispatcherServlet.properties配置文件中定义的默认实现类。如果该文件中没有对应处理器异常解析器的默认实现类，用户可以自定义处理器异常解析器的实现类，将之添加到DispatcherServlet.properties配置文件当中。

视图名称解析器。只允许一个实例

* 查找名为viewNameTranslator、类型为RequestToViewNameTranslator的Bean作为该类型组件。
* 如果没有找到，则使用默认的实现类DefaultRequestToViewNameTranslator作为该类型的组件。

视图解析器。允许多个实例

* 如果detectAllViewResolver的属性为true（默认为true），则根据类型匹配机制查找上下文以及Spring容器中所有类型为ViewResolver的Bean，将它们作为该类型组件。
* 如果 detectAllViewResolvers 的 属性 为 false， 则 查找 名为 viewResolvers、 类型 为 ViewResolver 的 Bean 作为 该类 型 组件。
* 如果 detectAllViewResolvers 的 属性 为 false， 则 查找 名为 viewResolvers、 类型 为 ViewResolver 的 Bean 作为 该类 型 组件。

文件上传解析器。只允许一个实例

* 查找 名为 muliipartResolver、 类型 为 MuliipartResolver 的 Bean 作为 该类 型 组件。
* 如果 用户 没有 在上 下 文中 显 式 定义 MuliipartResolver 类型 的 组件， 则 DispatcherServlet 将不 会 加载 该 类型 的 组件。

FlashMap映射管理器。

* 查找 名为 FlashMapManager、 类型 为 SessionFlashMapManager 的 Bean 作为 该类 型 组件， 用于 管理 FlashMap， 即 数据 默认 存储 在 HttpSession 中。

同一 类型 的 组件 存在 多个，因为 这些 组件 都 实现 了 org. springframework. core. Ordered 接口， 所以 可以 通过 Order 属性 确定 优先级 的 顺序， 值 越小 的 优先级 越高。



