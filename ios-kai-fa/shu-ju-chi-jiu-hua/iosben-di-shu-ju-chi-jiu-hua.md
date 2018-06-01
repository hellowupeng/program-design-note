# iOS本地数据持久化

### 一.iOS本地数据持久化的类型:

1. NSUserDefaults

2. plist

3. Keychain\(钥匙串\)

4. 归档

5. 沙盒写入

6. 数据库

### 二.应用场景

##### 1.NSUserDefaults

用于存储用户的偏好设置和用户信息,如用户名,是否自动登录,字体大小等.

数据自动保存在沙盒的Libarary/Preferences目录下.

NSUserDefaults将输入的数据储存在.plist格式的文件下,这种存储方式就决定了它的安全性几乎为0,所以不建议存储一些敏感信息如:用户密码,token,加密私钥等!

它能存储的数据类型为:NSNumber（NSInteger、float、double），NSString，NSDate，NSArray，NSDictionary，BOOL.

不支持自定义对象的存储.

```
NSUserDefaults *userDefault = [NSUserDefaults standardUserDefaults];
[userDefault setInteger:1 forKey:@"integer"];
[userDefault setBool:YES forKey:@"BOOl"];
[userDefault setFloat:6.5 forKey:@"float"];
[userDefault setObject:@"123" forKey:@"numberString"];
NSString *numberString = [userDefault objectForKey:@"numberString"];
BOOL myBool = [userDefault boolForKey:@"BOOl"];
[userDefault removeObjectForKey:@"float"];
[userDefault synchronize];
```

> 注意
>
> 1.NSUserDefaults存储的数据都是不可变的,想将可变数据存入需要先转为不可变才可以存储.
> 2.NSUserDefaults是定时把缓存中的数据写入磁盘的，而不是即时写入，为了防止在写完NSUserDefaults后程序退出导致的数据丢失，可以在写入数据后使用synchronize强制立即将数据写入磁盘.

### 2.plist

即属性列表文件，全名是Property List，这种文件的扩展名为.plist，因此，通常被叫做plist文件。它是一种用来存储串行化后的对象的文件，用于存储程序中经常用到且数据量小而不经常改动的数据。

可以存储的类型:NSNumber，NSString，NSDate，NSData ,NSArray，NSDictionary，BOOL.

不支持自定义对象的存储.

1.command + n 创建:

```
// 读取
NSString *plistPath = [[NSBundle mainBundle]pathForResource:@"myTest" ofType:@"plist"];
NSMutableDictionary *dataDic = [NSMutableDictionary dictionaryWithContentsOfFile:plistPath];
/**如果为数组时*/
//    NSMutableArray *dataArray = [NSMutableArray arrayWithContentsOfFile:plistPath];
NSLog(@"%@",dataDic);

// 修改
[dataDic setObject:@"男" forKey:@"sex"];
[dataDic setObject:@15 forKey:@"age"];
[dataDic writeToFile:plistPath atomically:YES];
NSLog(@"----%@",dataDic);
```

2.纯代码创建

```
// 创建
NSArray *sandBoxPath = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
NSString *documentsPath = [sandBoxPath objectAtIndex:0];
NSString *plistPath = [documentsPath stringByAppendingPathComponent:@"myTestPlist.plist"];
NSLog(@"%@",plistPath);
    
// 写入
NSMutableDictionary *dic = [NSMutableDictionary dictionary];
[dic setObject:@"18" forKey:@"age"];
[dic setObject:@"胡杨" forKey:@"name"];
[dic writeToFile:plistPath atomically:YES];

// 读取
NSArray *sandBoxPath = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
NSString *documentsPath = [sandBoxPath objectAtIndex:0];
NSString *plistPath = [documentsPath stringByAppendingPathComponent:@"myTestPlist.plist"];
NSLog(@"%@",plistPath);
NSMutableDictionary *dataDic = [NSMutableDictionary dictionaryWithContentsOfFile:plistPath];
NSLog(@"-----%@",dataDic);
```

> 注意：如果需要存储自定义类型的数据需要先进行序列化!

##### 3.Keychain

用于本地重要数据的存储,将数据加密后存储在本地更安全.如:密码,秘钥,序列号等.当你删除APP后Keychain存储的数据不会删除，所以在重装App后，Keychain里的数据还能使用。从ios 3.0开始，跨程序分享keychain变得可行而NSUserDefaults存储的数据会随着APP而删掉.

使用keychain时苹果官方已经为我们封装好了文件KeychainItemWrapper,引入即可使用.当然也可是使用其他优秀的第三方的封装,比如ssKeychain,使用方法如下:

[使用方法](https://link.jianshu.com/?t=https%3A%2F%2Fwww.cnblogs.com%2Fm4abcd%2Fp%2F5242254.html)

##### 4.归档\(NSKeyedArchiver\)

归档是iOS开发中数据存储常用的技巧，归档可以直接将对象储存成文件，把文件读取成对象。

相对于plist或者userdefault形式，归档可以存储的数据类型更加多样，并且可以存取自定义对象。对象归档的文件是保密的，在磁盘上无法查看文件中的内容，更加安全。

遵守NSCoding协议，并实现该协议中的两个方法。如果是继承，则子类一定要重写那两个方法。因为子类在存取的时候，会去子类中去找调用的方法，没找到那么它就去父类中找，所以最后保存和读取的时候新增加的属性会被忽略。需要先调用父类的方法，先初始化父类的，再初始化子类的。

保存数据的文件的后缀名可以随意命名。[demo](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Fhuyangwork%2FNSKeyedArchiverDemo.git)

##### 5.沙盒写入

持久化在Document目录下，一般存储非机密数据。当App中涉及到电子书阅读、听音乐、看视频、刷图片列表等时，推荐使用沙盒存储。因为这可以极大的节约用户流量，而且也增强了app的体验效果.

Application：存放程序源文件，上架前经过数字签名，上架后不可修改。

Documents: 保存应用运行时生成的需要持久化的数据,iTunes同步设备时会备份该目录。例如,游戏应用可将游戏存档保存在该目录。

tmp: 保存应运行时所需的临时数据,使完毕后再将相应的文件从该目录删除。应用 没有运行时,系统也可能会清除该目录下的文件。iTunes同步设备时不会备份该目录。

Library/Caches: 保存应用运行时成的需要持久化的数据,iTunes同步设备时不会备份 该目录。一般存储体积大、不需要备份的非重要数据，比如网络数据缓存存储到Caches下

Library/Preference: 保存应用的所有偏好设置，如iOS的Settings\(设置\) 应会在该目录中查找应的设置信息。iTunes同步设备时会备份该目录。

```
// 获取程序的Home目录       
NSString  *path = NSHomeDirectory();     
// 获取Document目录       
NSString *path = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject;  
// 获取Cache目录       
NSString *path = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES).firstObject;   
// 获取Library目录       
NSString *path = NSSearchPathForDirectoriesInDomains(NSLibraryDirectory, NSUserDomainMask, YES).firstObject;
// 获取Tmp目录       
NSString *path = NSTemporaryDirectory();
```

写入：

```
NSString *path = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES).firstObject;
NSString *filePath = [path stringByAppendingPathComponent:@"test.txt"];
NSLog(@"%@",filePath);
NSArray *array = @[@"1",@"2",@"3"];
BOOL isSuccess = [array writeToFile:filePath atomically:YES];
if (isSuccess) {
    NSLog(@"成功");
} else {
    NSLog(@"失败");
}
```

读取：

```
NSString *path = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES).firstObject;
NSString *filePath = [path stringByAppendingPathComponent:@"test.txt"];
NSLog(@"%@",filePath);
NSArray *tmpArray = [NSArray arrayWithContentsOfFile:filePath];
NSLog(@"%@",tmpArray);
```

##### 6.数据库

适合储存数据量较大的数据,FMDB、CoreData、realm。

###### FMDB:

FMDB是iOS平台的SQLite数据库框架，FMDB以OC的方式封装了SQLite的C语言API，使用起来更加面向对象，省去了很多麻烦、冗余的C语言代码，对比苹果自带的Core Data框架，更加轻量级和灵活，提供了多线程安全的数据库操作方法，有效地防止数据混乱。

###### CoreData:

ore Data是iOS5之后才出现的一个框架，它提供了对象-关系映射\(ORM\)的功能，即能够将OC对象转化成数据，保存在SQLite数据库文件中，也能够将保存在数据库中的数据还原成OC对象。在此数据操作期间，我们不需要编写任何SQL语句.但是直接操作CoreData显的不是那么容易,所以我多数的时候会使用MagicRecord来实现.MagicRecord是对CoreData的二次封装,使用起来简单操作方便.

数据迁移:

如果使用到数据库那就不得不提数据迁移的问题,不管是MagicRecord还是FMDB如果要更新数据库都要进行数据迁移.

FMDB的数据迁移:

判断表中有没有这个字段,如果没有使用sq语句插入.

```
/**添加字段/数据迁移*/
- (void)dataMigrationWithTableName:(NSString *)tableName newAdded:(NSString *)newAdded block:(FMDBblock)block
{
//tableName:表名
//newAdded:新加字段名
    [_queue inDatabase:^(FMDatabase * _Nonnull db) {
        if (![db columnExists:newAdded inTableWithName:tableName]){
            NSString *sql = [NSString stringWithFormat:@"ALTER TABLE %@ ADD %@ INTEGER",tableName,newAdded];
            BOOL success = [db executeUpdate:sql];
            if (success) {
                block(YES,@"字段添加成功");
            } else {
                block(NO,@"字段添加失败");
            }
        }
    }];
}
```

###### 参考资料

1. [iOS本地数据持久化](https://mp.weixin.qq.com/s/0dAgmankSYsnmrpOUCOk6Q)



