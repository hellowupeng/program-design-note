# 控制流

使用 if 和 switch 来创建条件，并使用 for-in，while 和 repeat-while 来创建循环。

条件或循环变量的括号是可选的。

```
let individualScores = [75, 43, 103, 87, 12]
var teamScore = 0
for score in individualScores {
    if score > 50 {
        teamScore += 3
    } else {
        teamScore += 1
    }
}
print(teamScore)
```

在 `if` 语句中，条件必须是布尔表达式 - 这意味着如 `if score {...}` 这样的代码是一个错误，而不是隐式的比较为零。

在值的类型后面写一个问号（？），将该值标记为可选。

```
var optionalString: String? = "Hello"
print(optionalString == nil)
 
var optionalName: String? = "John Appleseed"
var greeting = "Hello!"
if let name = optionalName {
    greeting = "Hello, \(name)"
}
```

如果可选值为 nil，则条件为 false，并且花括号中的代码被跳过。否则，在 let 之后，可选值将被解包并分配给该常量，这会使代码块中的解包值可用。

处理可选值的另一种方式是使用 `??` 提供默认值。如果可选值缺失，则使用默认值。

```
let nickName: String? = nil
let fullName: String = "John Appleseed"
let informalGreeting = "Hi \(nickName ?? fullName)"
```

Switch 支持任何种类的数据和各种比较操作 - 它们不限于整数和相等性测试。

```
let vegetable = "red pepper"
switch vegetable {
case "celery":
    print("Add some raisins and make ants on a log.")
case "cucumber", "watercress":
    print("That would make a good tea sandwich.")
case let x where x.hasSuffix("pepper"):
    print("Is it a spicy \(x)?")
default:
    print("Everything tastes good in soup.")
}
```

使用 `for-in` 来遍历字典中的项目。

```
let interestingNumbers = [
    "Prime": [2, 3, 5, 7, 11, 13],
    "Fibonacci": [1, 1, 2, 3, 5, 8],
    "Square": [1, 4, 9, 16, 25],
]
var largest = 0
for (kind, numbers) in interestingNumbers {
    for number in numbers {
        if number > largest {
            largest = number
        }
    }
}
print(largest)
```

使用 while 来重复一段代码，直到条件改变。

```
var n = 2
while n < 100 {
    n *= 2
}
print(n)
 
var m = 2
repeat {
    m *= 2
} while m < 100
print(m)
```

使用 `..<` 得到一个索引范围。

```
var total = 0
for i in 0..<4 {
    total += i
}
print(total)
```

使用 `..<` 制作一个省略其上限值的范围，并使用 `...` 来制作一个包含两个值的范围。



