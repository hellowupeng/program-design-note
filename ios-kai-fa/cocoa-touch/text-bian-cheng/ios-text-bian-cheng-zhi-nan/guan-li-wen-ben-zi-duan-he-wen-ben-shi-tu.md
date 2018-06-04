# 管理文本字段和文本视图

### The Sequence of Messages to the Delegate

在大多数情况下，当给定文本对象的第一响应者状态发生变化（或即将发生的变化）时，UITextField或UITextView类的实例向其委托发送一系列类似名称的消息。当用户点击文本对象时，它自动成为第一响应者;结果，系统显示键盘，并开始为该文本对象编辑会话。当用户点击另一个文本对象或敲击一个按钮以结束编辑时，当前文本对象会退出第一响应者状态。如果没有选择其他文本对象，则系统隐藏键盘;另一方面，如果用户选择另一个文本对象，它将成为第一响应者，并显示该对象的键盘。

这种常见行为有几个例外。在iPad上，如果视图控制器使用“表单（form sheet）”样式以模态方式呈现其视图，则在用户点击解除键或以程序化方式解除模态视图控制器之前，键盘一旦显示就不会隐藏。此行为的目的是为了避免过多的动画，因为用户在很大程度上（但不是全部）文本字段的视图之间移动。另一个例外涉及自定义输入视图。输入视图是分配给文本视图或自定义视图的inputView属性的系统键盘的替代品。当有输入视图时，即使文本对象是第一响应者，UIKit也可能会交换键盘，并且它可能会为开发者显示非文本对象的类似于键盘的输入视图。

文本视图和文本字段发送给其代表的消息序列如下所示：

1. 在文本对象成为第一响应者之前，`textFieldShouldBeginEditing :`\(文本字段）和`textViewShouldBeginEditing :`\(文本视图）。
   代理可以通过返回YES（默认值）或NO来验证文本对象是否应成为第一响应者。
2. 文本对象成为第一响应者之后，`textFieldDidBeginEditing :`\(文本字段）和`textViewDidBeginEditing :`\(文本视图）。

   代理可以通过更新状态信息来响应此消息，或者例如通过在编辑会话期间显示叠加视图来响应此消息。

3. 在编辑会话期间 - 各种。

   在用户输入和编辑文本时，文本对象会调用某些代理方法（如果已实现）。例如，文本视图的委托可以在任何文本更改时收到一条`textViewDidChange：`消息。当用户点击文本字段的清除按钮时，文本字段的代表可以接收到`textFieldShouldClear：`消息;委托返回一个布尔值，指示是否应该清除文本。

4. 在文本对象放弃第一响应者之前，`textFieldShouldEndEditing :`\(文本字段）和`textViewShouldEndEditing :`\(文本视图）。

   代理实现这些方法的主要原因是验证输入的文本。例如，如果文本应符合给定的格式，代理将验证输入的字符串，如果字符串不符合，则返回NO。

   文本字段的相关方法是`textFieldShouldReturn :`.当用户点击返回键时，文本字段类发送一个`textFieldShouldReturn：`消息给委托，询问是否应该退出第一个响应者。

5. 在文本对象放弃第一响应者之后，`textFieldDidEndEditing :`\(文本字段）和`textViewDidEndEditing :`\(文本视图）。

   代理可以实现这些方法来获取用户刚输入或编辑的文本。

通过观察通知，除委托以外的对象可以被通知文本视图和文本字段的第一响应者状态的变化。

### 配置文本字段和文本视图

* 文字特征：文本颜色，对齐方式，font family, font typeface 和字体大小。
* 键盘：键盘类型，返回键名称，安全文本输入和自动启用的返回键，所有这些都由`UITextInputTraits`协议声明。

* 文本字段特定：边框，背景图片，禁用图片，清除按钮和占位符文本。作为UIControl对象，文本字段也具有突出显示，选中，启用和其他属性

* 文本视图特定：可编辑状态，数据检测器（用于电话号码和URL链接）。由于文本视图继承自UIScrollView，因此您还可以通过设置适当的属性来管理滚动视图行为。

### 跟踪多个文本字段或文本视图

使用 outlet 识别传入的文本对象

```
- (BOOL)textFieldShouldEndEditing:(UITextField *)textField {
    if (textField == SSN) {
            // .....
            return NO;
        }
    return YES;
}
```

对于标记方法，声明一组枚举常量，每个标记一个常量。

```
enum {
    NameFieldTag = 0,
    EmailFieldTag,
    DOBFieldTag,
    SSNFieldTag
};
```

使用标签识别传入的文本对象:

```
- (void)textFieldDidEndEditing:(UITextField *)textField {

    switch (textField.tag) {
        case NameFieldTag:
            // do something with this text field
            break;
        case EmailFieldTag:
             // do something with this text field
            break;
        // remainder of switch statement....
    }
}
```

### 获取输入的文本和设置文本

访问输入文本的最佳代理方法是`textFieldDidEndEditing :`\(文本字段）和`textViewDidEndEditing :`\(文本视图）。

```
- (void)textFieldDidEndEditing:(UITextField *)textField {
    if ([textField.text isEqualToString:@""])
        return;

    switch (textField.tag) {
        case NameFieldTag:
            [thePerson setObject:textField.text forKey:MyAppPersonNameKey];
            break;
        case EmailFieldTag:
            [thePerson setObject:textField.text forKey:MyAppPersonEmailKey];
            break;
        case SSNFieldTag:
            [thePerson setObject:textField.text forKey:MyAppPersonSSNKey];
            break;
        default:
            break;
    }
}
```

获取 text view 文本：

```
- (void)textViewDidEndEditing:(UITextView *)textView {
    NSString *theText = textView.text;
    if (![theText isEqualToString:@""]) {
        [thePerson setObject:theText forKey:MyAppPersonNotesKey];
    }
    doneButton.enabled = NO;
}
```

### 使用带有文本字段的格式器

Foundation框架提供了抽象基类`NSFormatter`和该类的两个具体子类`NSDateFormatter`和`NSNumberFormatter`。使用这些类，用户可以将如下所示的值输入到文本字段中：

```
11/15/2010
-1,348.09
```

配置日期格式化器：

```
- (void)viewDidLoad {
    [super viewDidLoad];
    dateFormatter = [[NSDateFormatter alloc] init];
    [dateFormatter setGeneratesCalendarDates:YES];
    [dateFormatter setLocale:[NSLocale currentLocale]];
    [dateFormatter setCalendar:[NSCalendar autoupdatingCurrentCalendar]];
    [dateFormatter setTimeZone:[NSTimeZone defaultTimeZone]];
    [dateFormatter setDateStyle:NSDateFormatterShortStyle]; // example: 4/13/10
    DOB.placeholder = [NSString stringWithFormat:@"Example: %@", [dateFormatter stringFromDate:[NSDate date]]];

    // code continues....
}
```

使用NSDateFormatter对象将日期字符串转换为日期对象：

```
- (void)textFieldDidEndEditing:(UITextField *)textField {
    [textField resignFirstResponder];
    if ([textField.text isEqualToString:@""])
        return;
    switch (textField.tag) {
        case DOBField:
            NSDate *theDate = [dateFormatter dateFromString:textField.text];;
            if (theDate)
                [inputData setObject:theDate forKey:MyAppPersonDOBKey];
            break;
        // more switch case code here...
        default:
            break;
    }
}
```

### 验证输入的文本

验证输入字符串的最佳代理方法是`textFieldShouldEndEditing：`用于文本字段和`textViewShouldEndEditing：`用于文本视图。在文本字段或文本视图放弃第一个响应者状态之前调用这些方法。

使用正则表达式验证文本字段的字符串格式：

```
- (BOOL)textFieldShouldEndEditing:(UITextField *)textField {
    if (textField == SSN) { // SSN is an outlet
        NSString *regEx = @"[0-9]{3}-[0-9]{2}-[0-9]{4}";
        NSRange r = [textField.text rangeOfString:regEx options:NSRegularExpressionSearch];
        if (r.location == NSNotFound) {
            UIAlertView *av = [[[UIAlertView alloc] initWithTitle:@"Entry Error"
                message:@"Enter social security number in 'NNN-NN-NNNN' format"
                delegate:self cancelButtonTitle:@"OK" otherButtonTitles:nil] autorelease];
            [av show];
            return NO;
        }
    }
        return YES;
}
```

验证文本视图的字符串的允许长度:

```
- (BOOL)textViewShouldEndEditing:(UITextView *)textView {
      if (textView.text.length > 50) {
        UIAlertView *av = [[[UIAlertView alloc] initWithTitle:@"Entry Error"
            message:@"You must enter less than 50 characters." delegate:self cancelButtonTitle:@"OK"
            otherButtonTitles:@"Clear", nil] autorelease];
        [av show];
        return NO;
    }
    return YES;
}
```

验证输入的每个字符:

```
- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range
replacementString:(NSString *)string {
    if ([string isEqualToString:@""]) return YES;
    if (textField.tag == SalaryFieldTag) {
        unichar c = [string characterAtIndex:0];
        if ([[NSCharacterSet decimalDigitCharacterSet] characterIsMember:c]) {
            return YES;
        } else {
            return NO;
        }
    }

    return YES;
}
```

### 在文本字段中使用重叠视图

### 跟踪文本视图中的选择



