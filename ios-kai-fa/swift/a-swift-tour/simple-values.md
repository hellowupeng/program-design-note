# Simple Values

使用 let 来创建一个常量和 var 来创建一个变量。在编译时不需要知道常量的值，但必须为其赋值一次。

```Swift
var myVariable = 42
myVariable = 50
let myConstant = 42
```

在创建常量或变量时提供值可让编译器推断其类型。

如果初始值没有提供足够的信息（或者没有初始值），请在变量之后写入，并用冒号分隔来指定类型。

```
let implicitInteger = 70
let implicitDouble = 70.0
let explicitDouble: Double = 70
```

值永远不会被隐式转换为另一种类型。如果需要将值转换为其他类型，显式创建所需类型的实例。

```
let label = "The width is "
let width = 94
let widthLabel = label + String(width)
```

有一种更简单的方法来将值包含在字符串中: 将值写入括号中，并在括号之前写一个反斜杠（\）。

```
let apples = 3
let oranges = 5
let appleSummary = "I have \(apples) apples."
let fruitSummary = "I have \(apples + oranges) pieces of fruit."
```

对于占用多行的字符串使用三个双引号（"""）。

```
let quotation = """
I said "I have \(apples) apples."
And then I said "I have \(apples + oranges) pieces of fruit."
"""
```

使用方括号（\[\]）创建数组和字典，并通过将括号中的索引或键写入它们来访问它们的元素。在最后一个元素之后允许逗号。

```
var shoppingList = ["catfish", "water", "tulips", "blue paint"]
shoppingList[1] = "bottle of water"

var occupations = [
    "Malcolm": "Captain",
    "Kaylee": "Mechanic",
]
occupations["Jayne"] = "Public Relations"
```

要创建一个空数组或字典，使用初始化程序语法。

```
let emptyArray = [String]()
let emptyDictionary = [String: Float]()
```

如果可以推断出类型信息，则可以将一个空数组写为\[\]，将一个空字典写为\[：\]

```
shoppingList = []
occupations = [:]
```



