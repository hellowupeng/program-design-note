# Swift 高级语法

### 难点

get,set,willSet,didSet

* get

计算属性，主要用来计算返回值

基本语法:

```
struct Point {
    var y: Int?
    var x: Int?
    var location: (Int?, Int?) { return (x, y) }
}
```

实现 `readonly`

Swift中的属性没有对应的实例变量，必须自己创建。

```
struct Point {
    private var _y: Int?
    var: Int { return _y }
}
```

* set

存储属性，主要存储值。

设置存储属性时，必须实现计算属性。

```
struct Point {
     var y: Int?
     var x: Int?
     var location:(Int?, Int?) {
        get { return (x, y) }
        set {
           x = newValue.0
           y = newValue.1
       }
     }
  }
```

* willSet、didSet 是观察器

`willSet` 在新值设置之前调用，传入默认参数 `newValue`。

`didSet` 在新值设置之后调用，传入默认参数 `newValue`。

观察器观察的不止是对象本身，对象的某个属性的值改变也会触发观察器。

* 协议，optional

Swift协议不能实现可选。只能通过 `@objc` 实现可选。

但是Swift协议可扩展也可以继承。

Swift的协议实现一般通过扩展实现，通过协议继承和类的扩展来实现。

协议的命名遵循 Swift 的标准库, 即协议名以 "Type"， "-able"， "-ible" 结尾。-type 定义行为, -able、-ible 定义元素怎样做某事。

### 函数

* 默认参数

  方法可以通过设置默认参数，实现多参数时参数为空的情况，也可以简写方法，让方法更灵活。

  ```
  func abc(a: Int, b: Int? = nil, c: Int?) -> Int {
       return a + (b ?? 0) + (c ?? 0)
  }
  abc(a: 1, c: 2)
  abc(a: 1, b: 1, c: nil)
  ```

* 传出参数

  使用 `inout` 实现传出参数，但是作为传出参数时，不能使用Optional值。

  ```
  func exchange<T>(a: inout T, b: inout T){
       let c = a
       a = b
       b = c
  }

  var aa = 10
  var bb = 20

  exchange(a: &aa, b: &bb)

  aa   // 20 
  bb   // 10
  ```

* 省略参数（可变参数？）

  ```
  func sum(data: Int...) -> Int{
       var result = 0

       for item in data {
           result += item
       }

       return result
  }

  sum(data: 1, 2, 3, 4)
  ```

* 泛型方法

  ```
  // 自定义带索引的for_in
  func forin<T>(_ items: [T], closure: (T, Int) -> Void) {
       var index = 0

       for item in items {
            closure(item, index)
            index += 1
       }
  }

  forin([1,3,4]) { item, index in
       print("\(item)....\(index)")
  }
  ```

### Curring

柯里化：《Advanced Swift》中提出的一种通过写方法模板来快速实现方法的机制。让我们的方法更加动态化，可以整合系统方法。

### 关键字

* typealias
  这是一个非常好用的关键字，用来定义别名。系统使用了大量别名。

  ```
  typealias Index = Int
  typealias Add = (Int) -> Void

  funk printNumber() -> Add {
        return { a in print("\(a)") }
  }

  printNumber()(2)
  ```

* mutating

  结构体和枚举是值类型，值类型的属性不能在方法中更改，如果需要在方法中修改需要在方向名前面加mutating。

  ```
  struct Point {
      var x = 1.0
      mutating func lalala() {
            x += 2
      }
  }
  ```

* defer

  延迟执行，附带一个代码块，在方法执行结束前执行该代码块，即时方法执行过程中抛出错误也会执行该代码。多个defer时，执行顺序从后往前执行。

  ```
  func testDefer() throws {
           print("1")
           defer { print("2") }
           defer { print("3") }
           throw PrinterError.noFile
           defer { print("4") }
  }
  //打印1.3.2
  ```

* required

  用于初始化，required放在init前面。如果子类需要自定义初始化时，必须实现required的初始化。

  ```
  class SuperClass {
            required init() {
            }
  }

  class SubClass: SuperClass {
             required init() {
             }
  }
  ```

* lazy

  lazy关键字的作用是在第一次使用属性的时候才去生成，而不是在一开始就初始化好，按需使用。

  当计算属性的值比较耗时，或者需要外部值的依赖时，用lazy比较合适。

  lazy必须配合var使用，因为let需要有个初始值。

  lazy也是线程不安全的。

* 访问权限

  1. **private**：只能在当前类里访问

  2. **fileprivate**: 当前文件里访问

  3. **internal**: 默认访问级别，当前模块或框架可以访问，相当于Target级别

  4. **public**: 可以被任何人访问

  5. **open**: 可以被任何人访问，包括**override**和继承

  ```
  // 访问权限排序
  open > public > internal > fileprivate > private
  ```

* escaping

  在一个函数式的方法中，有一个闭包式的参数，如果这个闭包式的参数在函数中被返回出去了就是逃逸闭包，没有被返回就是非逃逸闭包。（在其他地方执行？）

  ```
  // 逃逸闭包 
  func closure(input: @escaping () -> ()) -> () -> () {
       return input
  }
  // 非逃逸闭包
  func closure(input: () -> ()) {
     
  }
  ```

### 闭包

* 定义
  闭包是一个引用类型的代码块，可以用作函数的参数或者返回值，可以捕获上下文的任何常量和变量。

* 表现形式
  全局函数：都是有命名的闭包，但是不能捕获任何值。

  嵌套函数：都是有命名的闭包，并且能够捕获当前上下文的值。

  闭包表达式：闭包表达式都是无名闭包，可以捕获上下文的值。

* 捕获上下文
  默认情况下，闭包会捕获附近作用域中的常量和变量，并使用强引用指向它们。你可以通过一个捕获列表来显式指定它的捕获行为。

  捕获列表在参数列表之前，由中括号括起来，里面是由逗号分隔的一系列表达式。一旦使用了捕获列表，就必须使用 in 关键字，即使省略了参数名、参数类型和返回类型。

  ```
  //值捕获
    var a = 0 
    var b = 0 
    let closure = { [a] in     
       print(a, b)
    } 
    a = 10 
    b = 10 
    closure() // 打印 “0 10”
    //引用捕获
    class SimpleClass {    
         var value: Int = 0
    }
    var x = SimpleClass() 
    var y = SimpleClass() 
    let closure = { [x] in    
         print(x.value, y.value)
    } 
    x.value = 10 
    y.value = 10 
    closure() // 打印 “10 10”
  ```

* 循环引用

  如果捕获列表中的值是引用类型，你可以使用 weak 或者 unowned 来修饰它，闭包会分别用弱引用和无主引用来捕获该值。

  ```
  myFunction { print(self.title) }                   // 以强引用捕获
  myFunction { [weak self] in print(self!.title) }   // 以弱引用捕获
  myFunction { [unowned self] in print(self.title) } // 以无主引用捕获
  ```

  在捕获列表中，也可以将任意表达式的值绑定到一个常量上。该表达式会在闭包被创建时进行求值，闭包会按照指定的引用类型来捕获表达式的值。例如：

  ```
  // 以弱引用捕获 self.parent 并赋值给 parent
  myFunction { [weak parent = self.parent] in print(parent!.title) }
  ```

### 运算符重载

### 下标编程

### 设计模式

### Runtime

1. 扩展属性
   ```
   extension UIView {
       private struct AssociatedKeys{
          static var loadingViewKey:UIView?
       }

       var loadingView: UIView? {
           get {
              return objc_getAssociatedObject(self, &AssociatedKeys.loadingViewKey) as? UIView
           }
           set {
                if let newValue = newValue {
                    newValue.backgroundColor = .red
                    objc_setAssociatedObject(self, &AssociatedKeys.loadingViewKey, newValue,objc_AssociationPolicy.OBJC_ASSOCIATION_RETAIN_NONATOMIC)
                }
           }
       }
   }
   ```
2. 交换方法

   ```
   class TestSwizzling : NSObject {
          dynamic func methodOne() -> Int {
              return 1
          }
   }

   extension TestSwizzling {
         //在 Objective-C 中,我们在 load() 方法进行 swizzling。但Swift不允许使用这个方法。
         override class func initialize() {
              let originalSelector = #selector(TestSwizzling.methodOne);
              let swizzledSelector = #selector(TestSwizzling.methodTwo);
 
              let originalMethod = class_getInstanceMethod(self, originalSelector);
              let swizzledMethod = class_getInstanceMethod(self, swizzledSelector);
 
              method_exchangeImplementations(originalMethod, swizzledMethod);
              }

        func methodTwo() -> Int{
               // swizzling 后, 该方法就不会递归调用
               return methodTwo() + 1
        }
   }

   var c = TestSwizzling()
   print(c.methodOne())  //2
   print(c.methodTwo())  //1
   ```

3. 获取属性和方法名

   ```
   fun allPropertyNamesAndValues(cls:AnyClass) ->[String: AnyObject] {
       var count: UInt32 = 0
       let properties = class_copyPropertyList(cls, &count)

       var resultDict: [String: AnyObject] = [:]
       for var i = 0; i < Int(count); ++i {
              let property = properties[i]
 
              // 取得属性名
             let name = property_getName(property)
             if let propertyName = String.fromCString(name) {
             // 取得属性值
                if let propertyValue = self.valueForKey(propertyName) {
                     resultDict[propertyName] = propertyValue
                }
            }
       }
      return resultDict
   }

   func allMethods() {
        var count: UInt32 = 0
        let methods = class_copyMethodList(Person.self, &count)

        for var i = 0; i < Int(count); ++i {
            let method = methods[i]
            let sel = method_getName(method)
            let methodName = sel_getName(sel)
            let argument = method_getNumberOfArguments(method)
 
           print("name: (methodName), arguemtns: (argument)")
        }
   }
   ```

### 第三方库

* SnapKit：自动布局约束库

* SwiftyJson：将对象转化为Json处理

* Alamofire：网络库

* RxSwift

* ObjectMapper：同SwiftyJson

* Quick：行为驱动开发框架

* Eureka：快速构建 iOS 各种复杂表单的库

* Spring：封装的系统动画库

* Kingfisher：图片缓存库

* CoreStore：Core Data管理类库

###### 参考资料

1. [Swift 高级语法](https://www.jianshu.com/p/06541d566042)



