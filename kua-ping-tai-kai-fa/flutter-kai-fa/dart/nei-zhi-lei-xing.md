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

Dart 字符串是一系列 UTF-16 代码单元。您可以使用单引号或双引号来创建一个字符串：

```
var s1 = 'Single quotes work well for string literals.';
var s2 = "Double quotes work just as well.";
var s3 = 'It\'s easy to escape the string delimiter.';
var s4 = "It's even easier to use the other delimiter.";
```

您可以使用$ {expression}将表达式的值放入字符串中。如果表达式是标识符，则可以跳过{}。为了获得对应于对象的字符串，Dart调用对象的 `toString()` 方法。

```
var s = 'string interpolation';

assert('Dart has $s, which is very handy.' ==
    'Dart has string interpolation, ' +
        'which is very handy.');
assert('That deserves all caps. ' +
        '${s.toUpperCase()} is very handy!' ==
    'That deserves all caps. ' +
        'STRING INTERPOLATION is very handy!');
```

> 注意：==运算符测试两个对象是否相同。如果两个字符串包含相同的代码单元序列，则它们是等效的。

您可以使用相邻的字符串文字或+运算符连接字符串：

```
var s1 = 'String '
    'concatenation'
    " works even over line breaks.";
assert(s1 ==
    'String concatenation works even over '
    'line breaks.');

var s2 = 'The + operator ' + 'works, as well.';
assert(s2 == 'The + operator works, as well.');
```

另一种创建多行字符串的方法：使用单引号或双引号的三引号：

```
var s1 = '''
You can create
multi-line strings like this one.
''';

var s2 = """This is also a
multi-line string.""";
```

你可以创建一个“原始”字符串，前缀为r：

```
var s = r"In a raw string, even \n isn't special.";
```

字符串字面量是编译时常量，只要任何内插表达式其值为 null 或数字，字符串或布尔值，则是一个编译时常量。

```
// These work in a const string.
const aConstNum = 0;
const aConstBool = true;
const aConstString = 'a constant string';

// These do NOT work in a const string.
var aNum = 0;
var aBool = true;
var aString = 'a string';
const aConstList = const [1, 2, 3];

const validConstString = '$aConstNum $aConstBool $aConstString';
// const invalidConstString = '$aNum $aBool $aString $aConstList';
```

### Booleans

为了表示布尔值，Dart 有一个名为 `bool` 的类型。两个对象具有类型 bool：布尔文字 true 和 false，它们都是编译时常量。

Dart 的类型安全意味着您不能使用 `if (nonbooleanValue)`或 `assert(nonbooleanValue)`。

显式地检查值：

```
// Check for an empty string.
var fullName = '';
assert(fullName.isEmpty);

// Check for zero.
var hitPoints = 0;
assert(hitPoints <= 0);

// Check for null.
var unicorn;
assert(unicorn == null);

// Check for NaN.
var iMeantToDoThis = 0 / 0;
assert(iMeantToDoThis.isNaN);
```

### Lists

几乎所有编程语言中最常见的集合可能是数组或有序对象组。在 Dart 中，数组是 List 对象，因此大多数人只是将它们称为列表（lists）。

Dart 列表字面量看起来像 JavaScript 数组字面量:

```
var list = [1, 2, 3];
```

> 注意：分析器推断该 list 的类型为 List &lt;int&gt;。如果尝试将非整数对象添加到此列表中，分析器或运行时（runtime）会引发错误。

列表使用从零开始的索引，其中0是第一个元素的索引，而 list.length - 1 是最后一个元素的索引。

```
var list = [1, 2, 3];
assert(list.length == 3);
assert(list[1] == 2);

list[1] = 1;
assert(list[1] == 1);
```

要创建一个编译时常量的列表，在列表文字前添加 const：

```
var constantList = const [1, 2, 3];
// constantList[1] = 1; // Uncommenting this causes an error.
```

### Maps

map 是将键和值关联的对象。键和值都可以是任何类型的对象。每个键只出现一次，但你可以多次使用相同的值。

```
var gifts = {
  // Key:    Value
  'first': 'partridge',
  'second': 'turtledoves',
  'fifth': 'golden rings'
};

var nobleGases = {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};
```

> 注意：分析器推断礼物的类型为 Map&lt;String, String&gt; 并且 nobleGases 的类型为 Map&lt;int，String&gt;。如果您尝试将错误类型的值添加到 map 中，则分析器或运行时会产生错误。

你可以使用Map构造函数创建相同的对象：

```
var gifts = new Map();
gifts['first'] = 'partridge';
gifts['second'] = 'turtledoves';
gifts['fifth'] = 'golden rings';

var nobleGases = new Map();
nobleGases[2] = 'helium';
nobleGases[10] = 'neon';
nobleGases[18] = 'argon';
```

将新的键值对添加到现有 map:

```
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds'; // Add a key-value pair
```

从 map 中检索值:

```
var gifts = {'first': 'partridge'};
assert(gifts['first'] == 'partridge');
```

如果查找不在 map 中的键，则返回空值（null）：

```
var gifts = {'first': 'partridge'};
assert(gifts['fifth'] == null);
```

使用 .length 获取 map 中键值对的数量：

```
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds';
assert(gifts.length == 2);
```

要创建一个编译时常量的 map，在 map 字面量前添加 const：

```
final constantMap = const {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};

// constantMap[2] = 'Helium'; // Uncommenting this causes an error.
```

### Runes {#runes}

在Dart中，runes 是字符串的 UTF-32 代码点。

### Symbols {#symbols}

符号（Symbol）对象表示在 Dart 程序中声明的运算符或标识符。

要获得标识符的符号，使用符号字面量，该字面量只是＃，后面跟着标识符：

```
#radix
#bar
```

Symbol 字面量是编译时常量。

  


  




