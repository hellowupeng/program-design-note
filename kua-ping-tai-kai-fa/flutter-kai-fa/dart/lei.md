# 类

Dart 是一种面向对象的语言，具有类和基于混合的继承。每个对象都是一个类的实例，所有的类都从 Object 继承而来。基于混合的继承意味着虽然每个类（Object 除外）都只有一个超类，但是一个类体可以在多个类层次中重用。

要创建一个对象，你可以使用 new 关键字和一个类的构造函数。

构造函数名称可以是 ClassName 或 ClassName.identifier。

```
var jsonData = jsonDecode('{"x":1, "y":2}');

// Create a Point using Point().
var p1 = new Point(2, 2);

// Create a Point using Point.fromJson().
var p2 = new Point.fromJson(jsonData);
```

> Dart 2：你可以在构造函数之前省略 new。例如：`p1 = Point(2, 2)`

对象具有由函数和数据（分别为方法和实例变量）组成的成员。当你调用一个方法时，你可以在一个对象上调用它：该方法可以访问该对象的函数和数据。

使用点（.）来引用实例变量或方法。

使用 `?.` 来避免左侧的操作数为空（null）时发生异常：

```
// If p is non-null, set its y value to 4.
p?.y = 4;
```

一些类提供了常量构造函数。要使用常量构造函数创建编译时常量，使用 const 而不是 new：

```
var p = const ImmutablePoint(2, 2);
```

要在运行时获取对象的类型，可以使用 Object 的 runtimeType 属性，该属性返回一个 Type 对象。

```
print('The type of a is ${a.runtimeType}');
```

### 实例变量

```
class Point {
  num x; // Declare instance variable x, initially null.
  num y; // Declare y, initially null.
  num z = 0; // Declare z, initially 0.
}
```

所有未初始化的实例变量都具有值 null。

所有的实例变量都会生成一个隐式的 getter 方法。非 final 实例变量还会生成一个隐式 setter 方法。

```
class Point {
  num x;
  num y;
}

void main() {
  var point = new Point();
  point.x = 4; // Use the setter method for x.
  assert(point.x == 4); // Use the getter method for x.
  assert(point.y == null); // Values default to null.
}
```

### 构造器函数

通过创建一个与其类相同名称的函数声明一个构造函数。构造函数的最常见形式：

```
class Point {
  num x, y;

  Point(num x, num y) {
    // There's a better way to do this, stay tuned.
    this.x = x;
    this.y = y;
  }
}
```

this 关键字引用当前实例。

> 注意：只有在名称冲突时才使用 this。否则，Dart风格省略了 this。

为实例变量分配构造函数参数的模式非常常见，Dart使用语法糖来简化它：

```
class Point {
  num x, y;

  // Syntactic sugar for setting x and y
  // before the constructor body runs.
  Point(this.x, this.y);
}
```

### 默认构造函数

如果不声明构造函数，则会提供一个默认构造函数。默认构造函数没有参数，并调用超类中的无参构造函数。

### 构造函数不会被继承

子类不从其父类继承构造函数。

### 命名构造函数

使用命名构造函数为类实现多个构造函数或提供额外的清晰度：

```
class Point {
  num x, y;

  Point(this.x, this.y);

  // Named constructor
  Point.origin() {
    x = 0;
    y = 0;
  }
}
```

请记住，构造函数不是继承的，这意味着超类的命名构造函数不会被子类继承。如果你想用超类中定义的命名构造函数创建子类，则必须在子类中实现该构造函数。

### 调用一个非默认的父类构造函数

默认情况下，子类中的构造函数调用父类的无名无参构造函数。父类的构造函数在构造函数体的开头被调用。如果还使用初始化程序列表，则会在调用父类之前执行。

执行顺序如下：

1. 初始化器列表

2. 超类的无参数构造函数

3. 主类的无参数构造函数

如果父类没有一个无名的无参构造函数，那么你必须手动调用父类中的一个构造函数。

### 初始化程序列表

除了调用超类构造函数之外，还可以在构造函数体运行之前初始化实例变量。用逗号分隔。

```
// Initializer list sets instance variables before
// the constructor body runs.
Point.fromJson(Map<String, num> json)
    : x = json['x'],
      y = json['y'] {
  print('In Point.fromJson(): ($x, $y)');
}
```

初始化器列表在设置 final 字段时非常方便。

### 重定向构造函数

有时构造函数的唯一目的是重定向到同一个类中的另一个构造函数。重定向构造函数的主体是空的，构造函数调用在冒号（:\)后出现。

```
class Point {
  num x, y;

  // The main constructor for this class.
  Point(this.x, this.y);

  // Delegates to the main constructor.
  Point.alongXAxis(num x) : this(x, 0);
}
```

### 常量构造函数

如果你的类产生永不改变的对象，你可以使这些对象成为编译时常量。定义一个 const 构造函数并确保所有实例变量都是 final。

```
class ImmutablePoint {
  static final ImmutablePoint origin =
      const ImmutablePoint(0, 0);

  final num x, y;

  const ImmutablePoint(this.x, this.y);
}
```

### 工厂构造函数

在实现不创建其类的新实例的构造函数时使用 factory 关键字。工厂构造函数可能会从缓存中返回一个实例，或者它可能会返回一个子类型的实例。

以下示例演示了从缓存中返回对象的工厂构造函数：

```
class Logger {
  final String name;
  bool mute = false;

  // _cache is library-private, thanks to
  // the _ in front of its name.
  static final Map<String, Logger> _cache =
      <String, Logger>{};

  factory Logger(String name) {
    if (_cache.containsKey(name)) {
      return _cache[name];
    } else {
      final logger = new Logger._internal(name);
      _cache[name] = logger;
      return logger;
    }
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) print(msg);
  }
}
```

> 注：工厂构造函数无法访问 this。

使用 new 关键字调用工厂构造函数：

```
var logger = new Logger('UI');
logger.log('Button clicked');
```



