# 枚举和结构体

使用`enum`来创建一个枚举。像类和所有其他命名类型一样，枚举可以具有与它们关联的方法。

```
enum Rank: Int {
    case ace = 1
    case two, three, four, five, six, seven, eight, nine, ten
    case jack, queen, king
    func simpleDescription() -> String {
        switch self {
        case .ace:
            return "ace"
        case .jack:
            return "jack"
        case .queen:
            return "queen"
        case .king:
            return "king"
        default:
            return String(self.rawValue)
        }
    }
}
let ace = Rank.ace
let aceRawValue = ace.rawValue
```

默认情况下，Swift分配原始值从零开始，每次递增1，但是您可以通过显式指定值来更改此行为。在上面的例子中，Ace被明确地赋予1的原始值，剩下的原始值被按顺序赋值。您也可以使用字符串或浮点数作为枚举的原始类型。使用rawValue属性来访问枚举case的原始值（rawValue）。

使用`init?(rawValue:)`初始化器从一个原始值创建一个枚举实例。事实上，在没有有意义的原始值的情况下，您不必提供一个。

```
enum Suit {
    case spades, hearts, diamonds, clubs
    func simpleDescription() -> String {
        switch self {
        case .spades:
            return "spades"
        case .hearts:
            return "hearts"
        case .diamonds:
            return "diamonds"
        case .clubs:
            return "clubs"
        }
    }
}
let hearts = Suit.hearts
let heartsDescription = hearts.simpleDescription()
```

注意上面提到的枚举的hearts的两种方式：当给hearts的值赋予一个常数时，枚举案例Suit.hearts由其全名引用，因为该常量没有指定明确的类型。

```
enum ServerResponse {
    case result(String, String)
    case failure(String)
}
 
let success = ServerResponse.result("6:00 am", "8:09 pm")
let failure = ServerResponse.failure("Out of cheese.")
 
switch success {
case let .result(sunrise, sunset):
    print("Sunrise is at \(sunrise) and sunset is at \(sunset).")
case let .failure(message):
    print("Failure...  \(message)")
}
```

注意日出和日落时间是如何从`ServerResponse`值中提取的，作为将值与switch case进行匹配的一部分。

使用`struct`来创建一个结构体。结构体支持许多与类相同的行为，包括方法和初始化器。结构和类之间最重要的差异之一是结构在代码中传递时总是被复制，但类通过引用传递。

```
struct Card {
    var rank: Rank
    var suit: Suit
    func simpleDescription() -> String {
        return "The \(rank.simpleDescription()) of \(suit.simpleDescription())"
    }
}
let threeOfSpades = Card(rank: .three, suit: .spades)
let threeOfSpadesDescription = threeOfSpades.simpleDescription()
```



  


  


  




