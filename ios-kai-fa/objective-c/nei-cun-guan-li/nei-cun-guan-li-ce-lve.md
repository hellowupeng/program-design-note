# 内存管理策略

在引用计数环境中用于内存管理的基本模型由 NSObject 协议中定义的方法和标准方法命名约定的组合提供。NSObject 类还定义了一个方法 `dealloc`，它在释放对象时自动调用。

### 基本内存管理规则

内存管理模型基于对象所有权。任何对象都可能有一个或多个所有者。只要一个对象至少有一个所有者，它就会继续存在。如果一个对象没有所有者，则运行时系统将自动销毁它。

Cocoa 设置了以下策略：

* **你拥有你创建的任何对象**

  你可以使用名称以 “alloc”，“new”，“copy” 或 “mutableCopy” 开头的方法创建对象。

* **你可以使用 retain 取得对象的所有权**

  接收到的对象通常保证在接收到的方法内保持有效，并且该方法还可以安全地将该对象返回给其调用者。在两种情况下使用 retain：（1）在访问器方法或 init 方法的实现中，将要存储的对象的所有权作为属性值;（2）防止对象作为其他操作的副作用而失效。

* **当你不再需要它时，你必须放弃你拥有的对象的所有权**

你通过向对象发送 release 消息或 autorelease 消息来放弃对象的所有权。在 Cocoa 术语中，放弃对象的所有权通常被称为“释放”对象。

* **你不能释放你不拥有的对象**

##### 一个简单的例子

```
{
    Person *aPerson = [[Person alloc] init];
    // ...
    NSString *name = aPerson.fullName;
    // ...
    [aPerson release];
}
```

Person 对象是使用 alloc 方法创建的，因此随后会在不再需要时发送释放消息。person 的 name 不是使用任何拥有方法获取的，因此不会发送释放消息。但请注意，该示例使用 release 而不是 autorelease。

##### 使用 autorelease 延迟释放

当需要发延迟释放消息时，使用 autorelease，通常在从方法返回对象时。

```
- (NSString *)fullName {
    NSString *string = [[[NSString alloc] initWithFormat:@"%@ %@",
                                          self.firstName, self.lastName] autorelease];
    return string;
}
```

你拥有由 alloc 返回的字符串。要遵守内存管理规则，必须先放弃字符串的所有权，然后再丢失对该字符串的引用。如果使用 release，字符串将在返回之前释放（该方法会返回一个无效的对象）。使用 autorelease，表示你想放弃所有权，但是你允许方法的调用方在释放之前使用返回的字符串。

你也可以像这样实现 fullName 方法：

```
- (NSString *)fullName {
    NSString *string = [NSString stringWithFormat:@"%@ %@",
                                 self.firstName, self.lastName];
    return string;
}
```

遵循基本规则，您不拥有stringWithFormat返回的字符串，因此您可以安全地从方法返回字符串。

相反，下面的实现是错误的：

```
- (NSString *)fullName {
    NSString *string = [[NSString alloc] initWithFormat:@"%@ %@",
                                         self.firstName, self.lastName];
    return string;
}
```

根据命名约定，没有什么可以表示 fullName 方法的调用者拥有返回的字符串。因此调用者没有理由释放返回的字符串，因此会被泄漏。

##### 你不拥有引用返回的对象

Cocoa 中的一些方法指定一个对象是通过引用返回的。一个常见的模式是使用包含错误信息的NSError对象（如果发生错误），如`initWithContentsOfURL:options:error:(NSData)` 和 `initWithContentsOfFile:encoding:error:(NSString)` 所示。

在这些情况下，适用的规则与已经描述的相同。当你调用任何这些方法时，你不会创建NSError对象，所以你不拥有它。因此不需要释放它，如下例所示：

```
NSString *fileName = <#Get a file name#>;
NSError *error;
NSString *string = [[NSString alloc] initWithContentsOfFile:fileName
                        encoding:NSUTF8StringEncoding error:&error];
if (string == nil) {
    // Deal with error...
}
// ...
[string release];
```

### 实现 dealloc 来放弃对象的所有权

NSObject 类定义了一个方法 dealloc，当对象没有所有者并且其内存被回收（被释放）时它会自动调用。dealloc方法的作用是释放对象自己的内存，并处理它拥有的任何资源，包括任何对象实例变量的所有权。

以下示例说明了如何为 Person 类实现 dealloc 方法：

```
@interface Person : NSObject
@property (retain) NSString *firstName;
@property (retain) NSString *lastName;
@property (assign, readonly) NSString *fullName;
@end

@implementation Person
// ...
- (void)dealloc
    [_firstName release];
    [_lastName release];
    [super dealloc];
}
@end
```

### Core Foundation 使用类似但不同的规则

Core Foundation 对象有类似的内存管理规则。然而，Cocoa 和 Core Foundation 的命名约定是不同的。特别是，Core Foundation的创建规则不适用于返回 Objective-C 对象的方法。例如，在下面的代码片段中，您不负责放弃 myInstance 的所有权：

```
MyClass *myInstance = [MyClass createInstance];
```



