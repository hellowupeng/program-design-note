# Functions

Dart 是一种真正的面向对象的语言，所以即使是函数也是对象，并且有一个类型 Function。这意味着函数可以分配给变量或作为参数传递给其他函数。你也可以调用一个 Dart 类的实例，就像它是一个函数一样。

```
bool isNoble(int atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

省略类型（不推荐）:

```
isNoble(atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

只包含一个表达式的函数，可以使用简写语法：

```
bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;
```

=&gt; expr 语法是 { return expr;  } 缩写。 =&gt; 符号有时被称为胖箭头语法。

函数可以有两种类型的参数：必需的和可选的。首先列出所需参数，然后列出任何可选参数。

### 可选参数

可选参数可以是位置（positional）或命名（named）的，但不能同时包含两者。

##### 可选的命名参数

调用函数时，可以使用 paramName：value 指定命名参数。

```
enableFlags(bold: true, hidden: false);
```

定义函数时，使用 {param1，param2，...} 来指定命名参数：

```
/// Sets the [bold] and [hidden] flags ...
void enableFlags({bool bold, bool hidden}) {
  // ...
}
```

##### 可选的位置参数

在中括号（\[ \]）中包装一组函数参数将它们标记为可选的位置参数：

```
String say(String from, String msg, [String device]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  return result;
}
```

不带可选参数调用此函数：

```
assert(say('Bob', 'Howdy') == 'Bob says Howdy');
```

用第三个参数调用这个函数：

```
assert(say('Bob', 'Howdy', 'smoke signal') ==
    'Bob says Howdy with a smoke signal');
```

### 默认参数值

您的函数可以使用 = 来定义命名参数和位置参数的默认值。默认值必须是编译时常量。如果未提供默认值，则默认值为空（null）。

为命名参数设置默认值：

```
/// Sets the [bold] and [hidden] flags ...
void enableFlags({bool bold = false, bool hidden = false}) {
  // ...
}

// bold will be true; hidden will be false.
enableFlags(bold: true);
```

为位置参数设置默认值：

```
String say(String from, String msg,
    [String device = 'carrier pigeon', String mood]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  if (mood != null) {
    result = '$result (in a $mood mood)';
  }
  return result;
}

assert(say('Bob', 'Howdy') ==
    'Bob says Howdy with a carrier pigeon');
```

你可以将列表或地图作为默认值传递。

```
void doStuff(
    {List<int> list = const [1, 2, 3],
    Map<String, String> gifts = const {
      'first': 'paper',
      'second': 'cotton',
      'third': 'leather'
    }}) {
  print('list:  $list');
  print('gifts: $gifts');
}
```

### main（）函数

每个应用程序都必须具有顶层 main（）函数，该函数充当应用程序的入口点。main（）函数返回void并且有一个可选的List&lt;String&gt;参数。

```
void main() {
  querySelector('#sample_text_id')
    ..text = 'Click me!'
    ..onClick.listen(reverseText);
}
```

> 注：..语法称为级联。通过级联，可以对单个对象的成员执行多个操作。

### Functions 作为第一类对象

可以将一个函数作为参数传递给另一个函数。

```
void printElement(int element) {
  print(element);
}

var list = [1, 2, 3];

// Pass printElement as a parameter.
list.forEach(printElement);
```

也可以将一个函数分配给一个变量：

```
var loudify = (msg) => '!!! ${msg.toUpperCase()} !!!';
assert(loudify('hello') == '!!! HELLO !!!');
```

### 匿名函数

大多数函数都被命名，例如 main（）或 printElement（）。也可以创建一个匿名函数，也叫 lambda 或 closure。可以将一个匿名函数分配给一个变量，可以将其添加到集合中或从集合中删除它。

匿名函数看起来类似于命名的函数 - 零个或多个参数，在括号之间用逗号和可选类型注释分隔。

下面的代码块包含该函数的主体：

```
([[Type] param1[, …]]) { 
  codeBlock; 
}; 
```

以下示例使用无类型参数item定义匿名函数。为列表中的每个项目调用的函数将打印一个字符串，该字符串包含指定索引处的值。

```
var list = ['apples', 'bananas', 'oranges'];
list.forEach((item) {
  print('${list.indexOf(item)}: $item');
});
```

如果该函数只包含一条语句，则可以使用粗体箭头符号将其缩短。

```
list.forEach(
    (item) => print('${list.indexOf(item)}: $item'));
```

### 返回值

如果没有指定返回值，则语句返回 null; 隐式地附加到函数体。

```
foo() {}

assert(foo() == null);
```





