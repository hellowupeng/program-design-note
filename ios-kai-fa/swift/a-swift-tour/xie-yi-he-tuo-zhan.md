# 协议和拓展

使用`protocol`来声明协议。

```
protocol ExampleProtocol {
    var simpleDescription: String { get }
    mutating func adjust()
}
```

类，枚举和结构都可以采用协议。

```
class SimpleClass: ExampleProtocol {
    var simpleDescription: String = "A very simple class."
    
    var anotherProperty: Int = 69105
    
    func adjust() {
        simpleDescription += "  Now 100% adjusted."
    }
}

var a = SimpleClass()
a.adjust()
let aDescription = a.simpleDescription
 
struct SimpleStructure: ExampleProtocol {
    var simpleDescription: String = "A simple structure"
    
    mutating func adjust() {
        simpleDescription += " (adjusted)"
    }
}

var b = SimpleStructure()
b.adjust()
let bDescription = b.simpleDescription
```

注意在`SimpleStructure`的声明中使用`mutating`关键字来标记修改结构的方法。`SimpleClass`的声明不需要标记为`mutating`的任何方法，因为类上的方法总是可以修改类。

使用`extension`为现有类型添加功能，例如新方法和计算属性。您可以使用扩展来将协议一致性添加到其他地方声明的类型，甚至可以添加到从库或框架导入的类型。

```
extension Int: ExampleProtocol {
    var simpleDescription: String {
        return "The number \(self)"
    }
    
    mutating func adjust() {
        self += 42
    }
}

print(7.simpleDescription)
```

您可以像使用任何其他命名类型一样使用协议名称，例如，创建具有不同类型的对象集合，但都符合单个协议。当您使用类型为协议类型的值时，协议定义之外的方法不可用。

```
let protocolValue: ExampleProtocol = a
print(protocolValue.simpleDescription)
// print(protocolValue.anotherProperty)  // Uncomment to see the error
```

即使变量`protocolValue`的运行时类型为`SimpleClass`，编译器也会将其视为给定类型的`ExampleProtocol`。这意味着除了协议一致性之外，您不能意外地访问类实现的方法或属性。

  
  
  


