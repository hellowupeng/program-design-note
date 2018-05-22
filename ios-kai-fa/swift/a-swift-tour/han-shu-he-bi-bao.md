# 函数和闭包

使用 `func` 来声明一个函数。使用 `->` 分隔函数的参数和返回类型。

```
func greet(person: String, day: String) -> String {
    return "Hello \(person), today is \(day)."
}
greet(person: "Bob", day: "Tuesday")
```

默认情况下，函数使用它们的参数名称作为其参数的标签。在参数名称之前写入自定义参数标签，或者写入\_以使用不带参数标签。

```
func greet(_ person: String, on day: String) -> String {
    return "Hello \(person), today is \(day)."
}
greet("John", on: "Wednesday")
```

使用一个元组来创建一个复合值。元组的元素可以通过名字或数字来引用。

```
func calculateStatistics(scores: [Int]) -> (min: Int, max: Int, sum: Int) {
    var min = scores[0]
    var max = scores[0]
    var sum = 0

    for score in scores {
        if score > max {
            max = score
        } else if score < min {
            min = score
        }
        sum += score
    }

    return (min, max, sum)
}
let statistics = calculateStatistics(scores: [5, 3, 100, 3, 9])
print(statistics.sum)
print(statistics.2)
```

函数可以嵌套。嵌套函数可以访问在外部函数中声明的变量。可以使用嵌套函数来组织长的或复杂的函数中的代码。

```
func returnFifteen() -> Int {
    var y = 10
    func add() {
        y += 5
    }
    add()
    return y
}
returnFifteen()
```

函数是 first-class 的类型。这意味着一个函数可以返回另一个函数作为它的值。

```
func makeIncrementer() -> ((Int) -> Int) {
    func addOne(number: Int) -> Int {
        return 1 + number
    }
    return addOne
}
var increment = makeIncrementer()
increment(7)
```

函数可以将另一个函数作为其参数之一。

```
func hasAnyMatches(list: [Int], condition: (Int) -> Bool) -> Bool {
    for item in list {
        if condition(item) {
            return true
        }
    }
    return false
}

func lessThanTen(number: Int) -> Bool {
    return number < 10
}

var numbers = [20, 19, 7, 12]
hasAnyMatches(list: numbers, condition: lessThanTen)
```

函数实际上是闭包的一个特例：可以稍后调用的代码块。闭包中的代码可以访问在创建闭包的范围中可用的变量和函数等内容，即使闭包在执行时处于不同的范围，你已经看到了一个嵌套函数的例子。您可以使用花括号（{}）编写一个没有名称的闭包。用于从主体（body）中分离参数和返回类型。

```
numbers.map({ (number: Int) -> Int in
    let result = 3 * number
    return result
})
```

您可以更简洁地编写闭包的几个选项。当闭包的类型已知时，例如代理的回调，可以省略其参数的类型，其返回类型或两者。单语句闭包隐式返回其唯一语句的值。

```
let mappedNumbers = numbers.map({ number in 3 * number })
print(mappedNumbers)
```

您可以通过数字而不是名称来引用参数 - 这种方法在非常短的闭包中特别有用。作为函数的最后一个参数传递的闭包可以在括号之后立即出现。当闭包是函数的唯一参数时，可以完全省略括号。

```
let sortedNumbers = numbers.sorted { $0 > $1 }
print(sortedNumbers)
```



