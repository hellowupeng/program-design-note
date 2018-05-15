# 操作符

### 条件表达式

```
condition ? expr1 : expr2
```

如果条件为真，则评估 expr1（并返回其值）;否则，评估并返回 expr2 的值。

```
expr1 ?? expr2
```

如果 expr1 非空，则返回其值;否则，评估并返回 expr2 的值。

### 级联符号（..）

Cascades（..）允许您在同一个对象上进行一系列操作。除了函数调用之外，还可以访问同一对象上的字段。这通常会为您节省创建临时变量的步骤，并允许您编写更流畅的代码。

```
querySelector('#confirm') // Get an object.
  ..text = 'Confirm' // Use its members.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'));
```

等价于：

```
var button = querySelector('#confirm');
button.text = 'Confirm';
button.classes.add('important');
button.onClick.listen((e) => window.alert('Confirmed!'));
```

你也可以嵌套 cascades：

```
final addressBook = (new AddressBookBuilder()
      ..name = 'jenny'
      ..email = 'jenny@example.com'
      ..phone = (new PhoneNumberBuilder()
            ..number = '415-555-0100'
            ..label = 'home')
          .build())
    .build();
```

在一个返回实际对象的函数上构造你的级联：

```
var sb = new StringBuffer();
sb.write('foo')
  ..write('bar'); // Error: method 'write' isn't defined for 'void'.
```





