# 集合

### NSArray

##### 排序

* 逆序：array.reverseObjectEnumerator.allObjects
* 数组中是字符串对象排序首选sortedArrayUsingSelector:

```
NSArray *array = @[@"John Appleseed", @"Tim Cook", @"Hair Force One", @"Michael Jurewitz"];
NSArray *sortedArray = [array sortedArrayUsingSelector:@selector(localizedCaseInsensitiveCompare:)];
```

* 存储内容是数字

```
NSArray *numbers = @[@9, @5, @11, @3, @1];
NSArray *sortedNumbers = [numbers sortedArrayUsingSelector:@selector(compare:)];
```

* 使用函数指针 sortedArrayHint 排序

```
- (NSData *)sortedArrayHint;
- (NSArray *)sortedArrayUsingFunction:(NSInteger (*)(id, id, void *))comparator context:(void *)context;
- (NSArray *)sortedArrayUsingFunction:(NSInteger (*)(id, id, void *))comparator context:(void *)context hint:(NSData *)hint;
```

* 基于 block 的排序方法

```
- (NSArray *)sortedArrayUsingComparator:(NSComparator)cmptr;
- (NSArray *)sortedArrayWithOptions:(NSSortOptions)opts usingComparator:(NSComparator)cmptr;
```

* 性能比较 selector 最快

```
Sorting 1000000 elements. selector: 4947.90[ms] function: 5618.93[ms] block: 5082.98[ms].
```

* 更快的二分查找

```
typedef NS_OPTIONS(NSUInteger, NSBinarySearchingOptions) {
     NSBinarySearchingFirstEqual = (1UL << 8),
     NSBinarySearchingLastEqual = (1UL << 9),
     NSBinarySearchingInsertionIndex = (1UL << 10),
};

- (NSUInteger)indexOfObject:(id)obj
     inSortedRange:(NSRange)r
     options:(NSBinarySearchingOptions)opts
     usingComparator:(NSComparator)cmp;

//Time to search for 1000 entries within 1000000 objects. Linear: 54130.38[ms]. Binary: 7.62[ms]
```

##### 枚举

* 使用 indexesOfObjectsWithOptions:passingTest:

```
NSIndexSet *indexes = [randomArray indexesOfObjectsWithOptions:NSEnumerationConcurrent
     passingTest:^BOOL(id obj, NSUInteger idx, BOOL *stop) {
     return testObj(obj);
}];
NSArray *filteredArray = [randomArray objectsAtIndexes:indexes];
```

* 传统的枚举

```
NSMutableArray *mutableArray = [NSMutableArray array];
for (id obj in randomArray) {
    if (testObj(obj)) {
        [mutableArray addObject:obj];
    }
}
```

* block 方式枚举

```
NSMutableArray *mutableArray = [NSMutableArray array];
[randomArray enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
    if (testObj(obj)) {
        [mutableArray addObject:obj];
    }
}];
```

* 通过下标 objectAtIndex:

```
NSMutableArray *mutableArray = [NSMutableArray array];
for (NSUInteger idx = 0; idx < randomArray.count; idx++) {
    id obj = randomArray[idx];
    if (testObj(obj)) {
        [mutableArray addObject:obj];
    }
}
```

* 使用 NSEnumerator

```
NSMutableArray *mutableArray = [NSMutableArray array];
NSEnumerator *enumerator = [randomArray objectEnumerator];
id obj = nil;
while ((obj = [enumerator nextObject]) != nil) {
    if (testObj(obj)) {
        [mutableArray addObject:obj];
    }
}
```

### NSDictionary





