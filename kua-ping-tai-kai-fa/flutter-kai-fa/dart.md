# Dart

### 重要概念

* 你可以放在变量中的所有东西都是一个对象，每个对象都是一个类的实例。数字，函数和 `null` 都是对象。所有对象都从 `Object` 类继承。
* 虽然 Dart 是强类型的，但类型注释是可选的，因为 Dart 可以推断类型。当你想明确地表示没有类型时，使用特殊类型 dynamic。

* Dart 支持泛型类型，如  `List<int>`（整数列表）或 `List<dynamic>`（任何类型的对象列表）。

* Dart 支持顶层函数（如 `main()`），以及与类或对象绑定的函数（分别为 static 和 instance 方法）。你也可以在函数中创建函数（嵌套函数或局部函数）。

* 同样，Dart 支持顶级变量以及绑定到类或对象（静态变量和实例变量）的变量。实例变量有时称为字段或属性。

* 与 Java 不同，Dart 没有关键字 public，protected 和 private。如果标识符以下划线（\_）开头，则它在库中是私有的。

* 标识符可以以字母或下划线（\_）开头，然后是这些字符和数字的任意组合。

* Dart 工具可以报告两种问题：警告和错误。

### 变量

```
var name = 'Bob';
```

变量存储引用。名为 name 的变量包含对值为“Bob”的 String 对象的引用。

如果对象不限于单一类型，可以指定 `Object` 或 `dynamic` 类型:

```
dynamic name = 'Bob';
```

```
String name = 'Bob';
```

### 默认值

未初始化的变量的初始值为 `null`。即使数字类型的变量最初也为 `null`，因为 Dart 中的所有数据都是对象。

```
int lineCount;
assert(lineCount == null);
```

### Final 和 const

如果你从不打算改变一个变量，使用 `final` 或 `const`。final 变量只能设置一次；const 变量是一个编译时常量。Const 变量为隐式 final。

> 注意：实例变量可以是 `final`，但不能是 `const`。

```
final name = 'Bob'; // Without a type annotation
// name = 'Alice'; // Uncommenting this causes an error
final String nickname = 'Bobby';
```

将 `const` 用于想要成为编译时常量的变量。如果const变量处于类级别，则将其标记为 `static const`。

```
const bar = 1000000; // Unit of pressure (dynes/cm2)
const double atm = 1.01325 * bar; // Standard atomsphere
```

`const` 关键字不仅用于声明常量。也可以使用它来创建常量值，以及声明创建常量值的构造函数。

```
// Note: [] creates an empty list.
// const [] creates an empty, immutable list (EIL).
var foo = const []; // foo is currently an EIL.
final bar = const []; // bar will always be an EIL.
const baz = const []; // baz is a compile-time constant EIL.

// You can change the value of a non-final, non-const variable,
// even if it used to have a const value.
foo = [];

// You can't change the value of a final or const variable.
// bar = []; // Unhandled exception.
// baz = []; // Unhandled exception.
```



