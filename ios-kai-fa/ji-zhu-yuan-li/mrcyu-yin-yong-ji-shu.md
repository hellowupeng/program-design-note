# MRC与引用计数

OC对象中都有一个retainCount属性来记录引用计数，NSObject alloc方法的内部实现：

```
struct obj_layout{
    NSUInteger retainded;
};

+(id)alloc{
    int size = sizeof(struct obj_layout) + 对象所占内存大小;
    struct obj_layout *p = (struct obj_layout *)calloc(1, size);
    return (id)(p+1);
}
```

alloc内部总共做了两部分的工作,一个是先计算出头部obj\_layout以及自身所占有多少空间,然后在内存之中通过calloc函数开辟一个大小为size的连续空间.alloc返回的id值为对象本身的指针\(非obj\_layout的指针\).整体如下图所示：

![](/assets/alloc返回对象内存图2.png)

###### 参考资料

1. [&lt;iOS 与OS X多线程和内存管理&gt;笔记:MRC与引用计数](https://mp.weixin.qq.com/s/Hu1QJd5-BGRTLFTVw5jaRw)



