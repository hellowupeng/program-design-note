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







