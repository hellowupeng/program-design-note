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



