# 错误处理

您使用任何采用`Error`协议的类型代表错误。

```
enum PrinterError: Error {
    case outOfPaper
    case noToner
    case onFire
}
```

使用`throw`抛出一个错误和`throws`标记可能抛出错误的函数。如果在函数中抛出错误，函数会立即返回，并调用函数的代码处理错误。

```
func send(job: Int, toPrinter printerName: String) throws -> String {
    if printerName == "Never Has Toner" {
        throw PrinterError.noToner
    }
    return "Job sent"
}
```

有几种方法可以处理错误。一种方法是使用`do-catch`。在`do`块内部，您可以通过在前面写入`try`来标记可能导致错误的代码。在`catch`块内部，错误会自动给出名称`error`，除非您给它一个不同的名称。

```
do {
    let printerResponse = try send(job: 1040, toPrinter: "Bi Sheng")
    print(printerResponse)
} catch {
    print(error)
}
```

您可以提供多个处理特定错误的`catch`块。

```
do {
    let printerResponse = try send(job: 1440, toPrinter: "Gutenberg")
    print(printerResponse)
} catch PrinterError.onFire {
    print("I'll just put this over here, with the rest of the fire.")
} catch let printerError as PrinterError {
    print("Printer error: \(printerError).")
} catch {
    print(error)
}
```

处理错误的另一种方法是使用`try？`将结果转换为可选。如果该函数抛出一个错误，则丢弃该特定错误并且结果为 `nil`。

否则，结果是包含函数返回值的可选项。

```
let printerSuccess = try? send(job: 1884, toPrinter: "Mergenthaler")
let printerFailure = try? send(job: 1885, toPrinter: "Never Has Toner")
```

在函数返回之前，使用`defer`来编写在函数中所有其他代码之后执行的代码块。无论函数是否引发错误，代码都会执行。

```
var fridgeIsOpen = false
let fridgeContent = ["milk", "eggs", "leftovers"]

func fridgeContains(_ food: String) -> Bool {
    fridgeIsOpen = true
    defer {
        fridgeIsOpen = false
    }

    let result = fridgeContent.contains(food)
    return result
}
fridgeContains("banana")
print(fridgeIsOpen)
```



