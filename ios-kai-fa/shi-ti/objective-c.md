# Objective-C

##### 1、为什么说 Objective-C 是一门动态的语言？

这里的动态和静态是相对的,动态的意思是不需要在编译时确定所有的东西,在运行时也可以动态添加变量,属性,方法和类. Objective-C 可以通过Runtime这个运行时机制,在运行时动态的添加变量,方法和类等,所以说Objective-C是一门动态的语言.

Objective-C的动态性，让程序在运行时判断其该有的行为，而不是像c等静态语言在编译构建时就确定下来。它的动态性主要体现在3个方面：

1）动态类型：如id类型。实际上静态类型因为其固定性和可预知性而使用的特别广泛。静态类型是强类型，动态类型是弱类型，运行时决定接收者。

2）动态绑定：让代码在运行时判断需要调用什么方法，而不是在编译时。与其他面向对象语言一样，方法调用和代码并没有在编译时连接在一起，而是在消息发送时才进行连接。运行时决定调用哪个方法。

3）动态载入。让程序在运行时添加代码模块以及其他资源。用户可以根据需要执行一些可执行代码和资源，而不是在启动时就加载所有组件。可执行代码中可以含有和程序运行时整合的新类。

##### 2、属性的实质是什么？包括哪几个部分？属性默认的关键字都有哪些？@dynamic 关键字和 @synthesize 关键字用来做什么的？

属性是一个类中用来描述对象的抽象概念。

属性包括的部分有setter和getter方法

```
// 原子性
atomic: 
nonatomic:

// 合成读写方法名
getter=getterName
setter=setterName

// 读写权限
readwrite
readonly

// 内存管理
assign
retain
copy
strong

@synthesize
@dynamic
```

@synthesize  
如果没有实现setter和getter方法，编译器将会自动在生产setter和getter方法。

@dynamic  
表示变量对应的属性访问器方法 , 是动态实现的 , 你需要在 NSObject 中继承而来的 +\(BOOL\) resolveInstanceMethod:\(SEL\) sel 方法中指定动态实现的方法或者函数。  
属性修饰其他关键字：

##### 3、属性的默认关键字是什么？

默认关键字，基本数据： atomic,readwrite,assign

普通的 OC 对象: atomic,readwrite,strong

##### 4、NSString 为什么要用 copy 关键字，如果用 strong 会有什么问题？

```
// 深复制
Person *xiaoMing = [[Person alloc] init];
NSMutableString * name = [[NSMutableStringalloc] initWithString:@"xiaoming"];
//name.string = @"xiaoming";
xiaoMing.name = name;
NSLog(@"%@", xiaoMing.name);
[name appendString:@"hah"];

//此时名字这个属性被修改了
NSLog(@"%@", xiaoMing.name);
```

如果用Copy来修饰name这个属性不会改变，

如果使用Strong，当name这个字符串改变的时候，name这个属性也会随着改变。

补充：这其实也是看需求，看被赋值的字符串是否需要随着赋值字符串的变化而变化，而大多数情况下我们不希望被赋值的字符串如某个对象的某个字符串类型的属性会随着赋值字符串的变化而变化。 反之，如果我们希望被赋值的字符串随着赋值字符串的变化而变化，那么我们也可以使用strong来修饰字符串

##### 5、如何令自己所写的对象具有拷贝功能？

需实现 NSCopying 协议。如果自定义的对象分为可变版本与不可变版本，那么就要同时实现 NSCopying与 NSMutableCopying协议。

具体步骤：

需声明该类遵从 NSCopying 协议实现 NSCopying 协议。该协议只有一个方法:

`- (id)copyWithZone:(NSZone *)zone;`

注意：一提到让自己的类用 copy 修饰符，我们总是想覆写copy方法，其实真正需要实现的却是 “copyWithZone” 方法。至于如何重写带 copy 关键字的 setter这个问题，如果抛开本例来回答的话，如下：

```
- (void)setName:(NSString *)name {
    //[_name release];
    _name = [name copy];
}
```

##### 6、可变集合类和不可变集合类的 copy  和 mutableCopy 有什么区别？如果集合是内容复制的话，集合里面的元素也是内容复制吗？

使用copy时 可变集合的指针地址以及内存地址都不相同 深复制 不可变集合的指针地址不一样但是内存地址一样 属于浅复制

使用mutableCopy的时候无论是可变集合还是不可变集合的指针地址和内存地址都不同 都属于深复制

```
- (void)testCopy {
    NSMutableArray *mutableArray = [NSMutableArray arrayWithObject:@"mutableArray"];
    NSArray *array = [NSArray arrayWithObject:@"array"];
    id copy_mutableArray = mutableArray.copy;
    id copy_array = array.copy;
    id mutableCopy_mutableArray = mutableArray.mutableCopy;
    id mutableCopy_array = array.mutableCopy;
    NSLog(@"mutableArray:%@ ,内存地址%p -- 指针地址%p",mutableArray,mutableArray,&mutableArray);
    NSLog(@"array:%@ ,内存地址%p -- 指针地址%p",array,array,&array);
    NSLog(@"copy_mutableArray:%@ ,内存地址%p -- 指针地址%p",copy_mutableArray,copy_mutableArray,&copy_mutableArray);
    NSLog(@"copy_array:%@ ,内存地址%p -- 指针地址%p",copy_array,copy_array,&copy_array);
    NSLog(@"mutableCopy_mutableArray:%@ ,内存地址%p -- 指针地址%p",mutableCopy_mutableArray,mutableCopy_mutableArray,&mutableCopy_mutableArray);
    NSLog(@"mutableCopy_array:%@ ,内存地址%p -- 指针地址%p",mutableCopy_array,mutableCopy_array,&mutableCopy_array);

}
```

输出：

```
2018-02-28 08:12:02.322754+0800 XWBlogsDemos[6646:275862] mutableArray:(
    mutableArray
) ,内存地址0x60400025ec90 -- 指针地址0x7ffee2eb0b48
2018-02-28 08:12:02.322913+0800 XWBlogsDemos[6646:275862] array:(
    array
) ,内存地址0x604000019010 -- 指针地址0x7ffee2eb0b40
2018-02-28 08:12:02.323038+0800 XWBlogsDemos[6646:275862] copy_mutableArray:(
    mutableArray
) ,内存地址0x604000019090 -- 指针地址0x7ffee2eb0b38
2018-02-28 08:12:02.323140+0800 XWBlogsDemos[6646:275862] copy_array:(
    array
) ,内存地址0x604000019010 -- 指针地址0x7ffee2eb0b30
2018-02-28 08:12:02.323236+0800 XWBlogsDemos[6646:275862] mutableCopy_mutableArray:(
    mutableArray
) ,内存地址0x60400025ef90 -- 指针地址0x7ffee2eb0b28
2018-02-28 08:12:02.323333+0800 XWBlogsDemos[6646:275862] mutableCopy_array:(
    array
) ,内存地址0x60400025ee70 -- 指针地址0x7ffee2eb0b20
```

##### 7、为什么 IBOutlet 修饰的 UIView 也适用 weak 关键字？

在xib或者Sb拖控件时，其实控件就加载到了父控件的subviews数组里面，进行了强引用，使用weak，避免循环引用





