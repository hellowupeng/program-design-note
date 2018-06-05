# 数据安全及各种加密算法对比

常用加密方式：

* Base64编码（基础）
* 单项散列函数 MD5、SHA1、SHA256、SHA512等

* 消息认证码 HMAC-MD5、HMAC-SHA1

* 对称加密 DES\|3DES\|AES（高级加密标准）

* 非对称加密 RSA

* 数字签名

* 证书

### Base64编码

base64编码：

```
// 要编码的字符串
NSString *str = @"haha";

// 转换成二进制文件
NSData *data = [str dataUsingEncoding:NSUTF8StringEncoding];

// 进行base64编码
NSString *dataStr = [data base64EncodedStringWithOptions:kNilOptions];

NSLog(@"%@", dataStr);
```

base64解码：

```
// 先对数据进行解码
NSData *encData = [[NSData alloc]initWithBase64EncodedString:dataStr options:kNilOptions];

// 将二进制数据转换成字符串
NSString *encStr = [[NSString alloc]initWithData:encData encoding:NSUTF8StringEncoding];

NSLog(@"%@", encStr);
```

原理：

1. 将所有字符串转换成ASCII码。

2. 将ASCII码转换成8位二进制。

3. 将二进制三位归成一组（不足三位在后边补0），再按每组6位，拆成若干组。

4. 统一在6位二进制后不足8位的补0。

5. 将补0后的二进制转换成十进制。

6. 从Base64编码表取出十进制对应的Base64编码。

若原数据长度不是3的倍数时且剩下1个输入数据，则在编码结果后加2个=；若剩下2个输入数据，则在编码结果后加1个=

特点：

1. 可以将任意的二进制数据进行Base64编码。

2. 所有的数据都能被编码为并只用65个字符就能表示的文本文件。

3. 编码后的65个字符包括AZ,az,0~9,+,/,=。

4. 对文件或字符串进行Base64编码后将比原始大小增加33%。

5. 能够逆运算。

6. 不够安全，但却被很多加密算法作为编码方式。

### 单项散列函数

单向散列函数也称为消息摘要函数、哈希函数或者杂凑函数。

单向散列函数输出的散列值又称为消息摘要或者指纹

特点：

1. 对任意长度的消息散列得到散列值是定长的。

2. 散列计算速度快，非常高效。

3. 消息不同，则散列值一定不同。

4. 消息相同，则散列值一定相同。

5. 具备单向性，无法逆推计算。

经典算法：

MD4、MD5、SHA1、SHA256、SHA512等。

安全性：

* md5解密网站：[http://www.cmd5.com](http://www.cmd5.com/)

* MD5的强抗碰撞性已经被证实攻破，即对于重要数据不应该再继续使用MD5加密

**疑问一：单项散列函数为什么不可逆？？**

原来好多同学知识知道md5加密是不可逆的，却不知道是为什么，其实散列函数可以将任意长度的输入经过变化得到不同的输出，如果存在两个不同的输入得到了相同的散列值，我们称之为这是一个碰撞，因为使用的hash算法，在计算过程中原文的部分信息是丢失了的，一个MD5理论上可以对应多个原文，因为MD5是有限多个，而原文是无限多个的。

一个形象的例子：2 + 5 = 7，但是根据 7 的结果，却并不能推算出是由 2 + 5计算得来的。

**疑问二：为什么有些网站可以解密MD5后的数据？？**

MD5解密网站，并不是对加密后的数据进行解密，而是数据库中存在大量的加密后的数据，对用户输入的数据进行匹配（也叫暴力碰撞），匹配到与之对应的数据就会输出，并没有对应的解密算法。

##### MD5改进

由以上信息可以知道，MD5加密后的数据也并不是特别安全的，其实并没有绝对的安全策略，我们可以对MD5进行改进，加大破解的难度，典型的加大解密难度的方式有一下几种：

1. 加盐（Salt）：在明文的固定位置插入随机串，然后再进行MD5

2. 先加密，后乱序：先对明文进行MD5，然后对加密得到的MD5串的字符进行乱序

3. 先乱序，后加密：先对明文字符串进行乱序处理，然后对得到的串进行加密

4. 先乱序，再加盐，再MD5等

5. HMac消息认证码

也可以进行多次的md5运算，总之就是要加大破解的难度。

**Hmac消息认证码（对MD5的改进）**

原理：

1. 消息的发送者和接收者有一个共享密钥

2. 发送者使用共享密钥对消息加密计算得到MAC值（消息认证码）

3. 消息接收者使用共享密钥对消息加密计算得到MAC值

4. 比较两个MAC值是否一致

使用：

1. 客户端需要在发送的时候把（消息）+（消息·HMAC）一起发送给服务器

2. 服务器接收到数据后，对拿到的消息用共享的KEY进行HMAC，比较是否一致，如果一致则信任

### **对称加密算法**

对称加密的特点：

* 加密/解密使用相同的密钥

* 是可逆的

经典算法：

* DES 数据加密标准

* 3DES 使用3个密钥，对消息进行（密钥1·加密）+（密钥2·解密）+（密钥3·加密）

* AES 高级加密标准

密码算法可以分为分组密码和流密码两种：

* 分组密码：每次只能处理特定长度的一zu数据的一类密码算法。一个分组的比特数量就称之为分组长度。

* 流密码：对数据流进行连续处理的一类算法。流密码中一般以1比特、8比特或者是32比特等作为单位俩进行加密和解密。

分组模式：主要有两种

* ECB模式\(又称电子密码本模式\)

  * 使用ECB模式加密的时候，相同的明文分组会被转换为相同的密文分组。

  * 类似于一个巨大的明文分组 -&gt; 密文分组的对照表。

  **某一块分组被修改，不影响后面的加密结果**

* CBC模式\(又称电子密码链条\)

  * 在CBC模式中，首先将明文分组与前一个密文分组进行XOR\(异或\)运算，然后再进行加密。

  * 每一个分组的加密结果依赖需要与前一个进行异或运算，由于第一个分组没有前一个分组，所以需要提供一个初始向量iv

  **某一块分组被修改，影响后面的加密结果**

### **非对称加密**

鉴于对称加密存在的风险，非对称加密应运而生

特点：

* 使用公钥加密，使用私钥解密

* 公钥是公开的，私钥保密

* 加密处理安全，但是性能极差

非对称密码体制的特点：算法强度复杂、安全性依赖于算法与密钥，但是由于其算法复杂，而使得加密解密速度没有对称加密解密的速度快

经典算法：

* RSA

**非对称加密存在的安全问题**

原理上看非对称加密非常安全，客户端用公钥进行加密，服务端用私钥进行解密，数据传输的只是公钥，原则上看，就算公钥被人截获，也没有什么用，因为公钥只是用来加密的，那还存在什么问题呢？？那就是经典的

**中间人攻击**

### 数字证书

数字证书包含：

* 公钥

* 认证机构的数字签名（权威机构CA）

###### 参考资料

1. [数据安全及各种加密算法对比](https://mp.weixin.qq.com/s/liznGP7EP914faAe1_Fs9Q)


