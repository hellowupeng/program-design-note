# NSDictionary

### 性能

同样数目的值，NSDictionary比NSArray要花费多得多的内存空间

### 排序

使用NSArray的排序方法将键数组排序为新的对象

```
- (NSArray *)keysSortedByValueUsingSelector:(SEL)comparator;
- (NSArray *)keysSortedByValueUsingComparator:(NSComparator)cmptr;
- (NSArray *)keysSortedByValueWithOptions:(NSSortOptions)opts
     usingComparator:(NSComparator)cmptr;
```

### 枚举

* keysOfEntriesWithOptions:passingTest:，可并行

```
NSSet *matchingKeys = [randomDict keysOfEntriesWithOptions:NSEnumerationConcurrent
passingTest:^BOOL(id key, id obj, BOOL *stop)
{
     return testObj(obj);
}];
NSArray *keys = matchingKeys.allObjects;
NSArray *values = [randomDict objectsForKeys:keys notFoundMarker:NSNull.null];
__unused NSDictionary *filteredDictionary = [NSDictionary dictionaryWithObjects:values
     forKeys:keys];
```

* getObjects:andKeys: 枚举，基于c数组

```
NSMutableDictionary *mutableDictionary = [NSMutableDictionary dictionary];
id __unsafe_unretained objects[numberOfEntries];
id __unsafe_unretained keys[numberOfEntries];
[randomDict getObjects:objects andKeys:keys];
for (int i = 0; i < numberOfEntries; i++) {
     id obj = objects[i];
     id key = keys[i];
     if (testObj(obj)) {
          mutableDictionary[key] = obj;
     }
}
```

* block 枚举

```
NSMutableDictionary *mutableDictionary = [NSMutableDictionary dictionary];
[randomDict enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop) {
     if (testObj(obj)) {
          mutableDictionary[key] = obj;
     }
}];
```

* NSFastEnumeration

```
NSMutableDictionary *mutableDictionary = [NSMutableDictionary dictionary];
for (id key in randomDict) {
     id obj = randomDict[key];
     if (testObj(obj)) {
          mutableDictionary[key] = obj;
     }
}
```

* NSEnumeration

```
NSMutableDictionary *mutableDictionary = [NSMutableDictionary dictionary];
NSEnumerator *enumerator = [randomDict keyEnumerator];
id key = nil;
while ((key = [enumerator nextObject]) != nil) {
     id obj = randomDict[key];
     if (testObj(obj)) {
          mutableDictionary[key] = obj;
     }
}
```

* 各个方法枚举时间参考

![](/assets/nsdictionary各方法枚举时间.png)



