# 内置类型

Dart语言对以下类型有特别的支持：

* numbers
* strings
* booleans
* lists \(也被称为 _arrays_\)
* maps
* runes \(用于在字符串中表示 Unicode 字符\)
* symbols

### Numbers

###### int

整数值不大于64位，具体取决于平台

###### double

64位（双精度）浮点数。

`int` 和 `double` 都是 `num` 的子类型。

整数字面量：

```
int x = 1;
int hex = 0xDEADBEEF;
```

double 字面量：

```
double y = 1.1;
double exponents = 1.42e5;
```

字符串和数字转换：

```
// String -> int
var one = int.parse('1');
assert(one == 1);

// String -> double
var onePointOne = double.parse('1.1');
assert(onePointOne == 1.1);

// int -> String
String oneAsString = 1.toString();
assert(oneAsString == '1');

// double -> String
String piAsString = 3.14159.toStringAsFixed(2);
assert(piAsString == '3.14');
```

int类型指定传统的移位（&lt;&lt;, &gt;&gt;），与（＆）和或（\|）运算符。

```
assert((3 << 1) == 6); // 0011 << 1 == 0110
assert((3 >> 1) == 1); // 0011 >> 1 == 0001
assert((3 | 4) == 7); // 0011 | 0100 == 0111
```

数字字面量是编译时常量。许多算数表达式也是编译时常量。

```
const msPerSecond = 1000;
const secondUntilRetry = 5;
const msUntilRetry = scondUntilRetry * msPerSecond;
```

### Strings



