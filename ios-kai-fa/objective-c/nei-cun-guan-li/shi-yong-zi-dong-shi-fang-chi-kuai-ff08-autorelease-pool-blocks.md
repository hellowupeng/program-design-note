# 使用自动释放池块（Autorelease Pool Blocks）

自动释放池块提供了一种机制，您可以放弃对象的所有权，但是避免对象被立即释放（例如当你从一个方法返回一个对象时）。通常情况下，您不需要创建自己的自动释放池块，但是在某些情况下，这样做是有益的。

### 关于自动释放池块

使用 `@autoreleasepool` 标记自动释放池块，如以下示例所示：

```
@autoreleasepool {
    // Code that creates autoreleased objects.
}
```

在自动释放池块的末尾，在块内接收到 autorelease 消息的对象将发送 release 消息，对象每次在块内发送 autorelease 消息时都会收到 release 消息。

像任何其他代码块一样，autorelease 池块可以嵌套：

```
@autoreleasepool {
    // . . .
    @autoreleasepool {
        // . . .
    }
    . . .
}
```

对于给定的 autorelease 消息，相应的 release 消息在发送 autorelease 消息的自动释放池块的末尾处发送。

Cocoa 总是希望代码在自动释放池块中执行，否则自动释放的对象不会被释放，并且您的应用程序会泄漏内存。

有三种情况你可能会使用自己的自动释放池块：

* 如果你正在编写不基于UI框架的程序，例如命令行工具。

* 如果你编写一个创建许多临时对象的循环。

您可以在循环中使用自动释放池块来在下一次迭代之前处理这些对象。在循环中使用自动释放池块有助于减少应用程序的最大内存占用量。

* 如果你使用了一个辅助线程。

一旦线程开始执行，您必须创建自己的自动释放池块;否则，你的应用程序会泄漏对象。

### 使用本地自动释放池块来降低峰值内存占用

许多程序创建自动释放的临时对象。这些对象添加到程序的内存占用空间直到块结束。在许多情况下，允许临时对象累积直到当前事件循环迭代结束时不会导致过多的开销;但是，在某些情况下，您可能会创建大量临时对象，这些临时对象会大大增加内存占用量，并且希望更快处理。在后面这些情况下，您可以创建自己的自动释放池块。在块的末尾，临时对象被释放，这通常会导致它们的释放，从而减少程序的内存占用。

以下示例显示了如何在for循环中使用本地自动释放池块。

```
NSArray *urls = <# An array of file URLs #>;
for (NSURL *url in urls) {
 
    @autoreleasepool {
        NSError *error;
        NSString *fileContents = [NSString stringWithContentsOfURL:url
                                         encoding:NSUTF8StringEncoding error:&error];
        /* Process the string, creating and autoreleasing more objects. */
    }
}
```

for 循环一次处理一个文件。任何在 autorelease 池块内发送 autorelease 消息的对象（如 fileContents）都会在块的末尾释放。

在 autorelease 池块之后，您应该将在块内自动释放的任何对象视为“已处理”。不要向该对象发送消息或将其返回给方法的调用者。如果您必须在自动释放池块之外使用临时对象，则可以通过向块内的对象发送 retain 消息，然后在该块之后将其自动释放发送给对象，如下例所示：

```
– (id)findMatchingObject:(id)anObject {
 
    id match;
    while (match == nil) {
        @autoreleasepool {
 
            /* Do a search that creates a lot of temporary objects. */
            match = [self expensiveSearchForObject:anObject];
 
            if (match != nil) {
                [match retain]; /* Keep match around. */
            }
        }
    }
 
    return [match autorelease];   /* Let match go and return it. */
}
```

### 自动释放池块和线程

Cocoa 应用程序中的每个线程都维护自己的自动释放池块。如果你正在编写一个 Foundation-only 的程序，或者你分离了一个线程，你需要创建你自己的自动释放池块。

如果您的应用程序或线程很长并且可能会生成大量自动释放对象，则应该使用自动释放池块。



