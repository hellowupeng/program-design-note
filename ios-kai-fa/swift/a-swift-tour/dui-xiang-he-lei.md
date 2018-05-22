# 对象和类

使用`class`跟随类的名称创建一个类。类中的属性声明与常量或变量声明的写法相同，只不过它在类的上下文中。同样，方法和函数声明的写法也是一样的。

```
class Shape {
    var numberOfSides = 0
    
    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides."
    }
}
```

通过在类名后加括号来创建一个类的实例。使用点语法来访问实例的属性和方法。

```
var shape = Shape()
shape.numberOfSides = 7
var shapeDescription = shape.simpleDescription()
```

`Shape`类的这个版本缺少一些重要的东西：初始化器在创建实例时设置类。使用`init`来创建一个实例。

```
class NamedShape {
    var numberOfSides: Int = 0
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides."
    }
}
```

如果需要在释放对象之前执行一些清理，请使用`deinit`创建一个`deinitializer`。

子类在其类名后面包含它们的父类名称，并用冒号分隔。不要求类为任何标准根类继承，所以您可以根据需要包含或省略超类。

覆盖父类的实现的子类上的方法被标记为`override`方法，没有`override`，编译器会检测为错误。编译器还检测具有`override`的方法，但实际上并未覆盖超类中的任何方法。

```
class Square: NamedShape {
    var sideLength: Double
    
    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 4
    }
    
    func area() -> Double {
        return sideLength * sideLength
    }
    
    override func simpleDescription() -> String {
        return "A square with sides of length \(sideLength)."
    }
}

let test = Square(sideLength: 5.2, name: "my test square")
test.area()
test.simpleDescription()
```

除了存储的简单属性外，属性还可以有一个getter和一个setter。

```
class EquilateralTriangle: NamedShape {
    var sideLength: Double = 0.0
    
    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 3
    }
    
    var perimeter: Double {
        get {
            return 3.0 * sideLength
        }
        set {
            sideLength = newValue / 3.0
        }
    }
    
    override func simpleDescription() -> String {
        return "An equilateral triangle with sides of length \(sideLength)."
    }
}

var triangle = EquilateralTriangle(sideLength: 3.1, name: "a triangle")
print(triangle.perimeter)
triangle.perimeter = 9.9
print(triangle.sideLength)
```

在`perimeter`设置器中，新值具有隐式名称`newValue`。在`set`后，您可以在圆括号中提供明确的名称。

请注意，`EquilateralTriangle`类的初始化程序有三个不同的步骤：

1. 设置子类声明的属性的值。

2. 调用父类的初始化器。

3. 更改由父类定义的属性的值。使用方法，获取器或设置器的任何其他设置工作也可以在此处完成。

如果您不需要计算属性，但仍需要提供在设置新值之前和之后运行的代码，请使用`willSet`和`didSet`。只要在初始化器外改变值提供的代码就会运行。例如，下面的类确保其三角形的边长总是与其正方形的边长相同。

```
class TriangleAndSquare {
    var triangle: EquilateralTriangle {
        willSet {
            square.sideLength = newValue.sideLength
        }
    }
    
    var square: Square {
        willSet {
            triangle.sideLength = newValue.sideLength
        }
    }
    
    init(size: Double, name: String) {
        square = Square(sideLength: size, name: name)
        triangle = EquilateralTriangle(sideLength: size, name: name)
    }
}

var triangleAndSquare = TriangleAndSquare(size: 10, name: "another test shape")
print(triangleAndSquare.square.sideLength)
print(triangleAndSquare.triangle.sideLength)
triangleAndSquare.square = Square(sideLength: 50, name: "larger square")
print(triangleAndSquare.triangle.sideLength)
```

使用可选值时，你可以在像方法，属性和下标之类的操作之前写`?`。如果在`?`之前的值是`nil`，那么`?`后的一切都会被忽略并且整个表达式的值为`nil`。否则，可选值将被解包，并且之后的所有内容都会被解包。作用于未包装的值。在这两种情况下，整个表达式的值都是一个可选值。

```
let optionalSquare: Square? = Square(sideLength: 2.5, name: "optional square")
let sideLength = optionalSquare?.sideLength
```



