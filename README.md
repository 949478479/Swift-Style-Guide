# Swift 编码风格规范

# 目录

- [命名](#naming)
    - [枚举](#enumeration)
    - [协议](#protocol)
    - [代理](#delegate)
    - [泛型](#generics)
    - [类名前缀](#class-prefixes)
    - [使用上下文类型推断](#use-type-inferred-context)
- [代码组织](#code-organization)
    - [协议实现](#protocol-conformance)
    - [无用代码](#unused-code)
    - [最小引用](#minimal-imports)
- [空格](#spacing)
- [注释](#comments)
- [类和结构体](#classes-and-structures)
    - [使用哪个](#which-one-to-use)
    - [定义示例](#example-definition)
    - [使用 Self](#use-of-self)
    - [计算属性](#computed-properties)
    - [Final](#final)
- [函数声明](#function-declarations)

<a id="naming"></a>
## 命名

使用驼峰命名法为类、方法、变量等取一个描述性强的命名。类、结构体、枚举、协议这些类型名应该首字母大写，而方法、变量则应该首字母小写。

**推荐**

```swift
private let maximumWidgetCount = 100
class WidgetContainer {
    var widgetButton: UIButton
    let widgetHeightPercentage = 0.85
}
```

**不推荐**

```swift
let MAX_WIDGET_COUNT = 100
class app_widgetContainer {
    var wBut: UIButton
    let wHeightPct = 0.85
}
```

一般情况下，应该避免使用缩略词。遵循 [API Design Guidelines](https://swift.org/documentation/api-design-guidelines/) （[中文传送门](http://swift.gg/2016/05/18/api-design-guidelines/)）的规范，当你使用常见的缩略词时，应该保持它们的大小写一致性，要么所有字母都大写，要么所有字母都小写。

**推荐**

```swift
let urlString: URLString
let userID: UserID
```

**不推荐**

```swift
let uRLString: UrlString
let userId: UserId
```

对于函数和构造器，除非上下文已经很清晰，最好为所有参数添加局部参数名。如果可以的话，最好也添加外部参数名来让函数调用语句更易读。

```swift
func date(from string: String) -> Date?
func convertPoint(atColumn column: Int, row: Int) -> CGPoint
func timedAction(afterDelay delay: NSTimeInterval, perform action: SKAction) -> SKAction!

date(from: "2014-03-14")
convertPoint(atColumn: 42, row: 13)
timedAction(afterDelay: 1.0, perform: someOtherAction)
```

<a id="enumeration"></a>
### 枚举

遵循苹果的 API 设计规范对 Swift 的要求，使用首字母小写的驼峰命名法来给枚举值命名。

```swift
enum Shape {
    case rectangle
    case square
    case rightTriangle
    case equilateralTriangle
}
```

<a id="protocol"></a>
### 协议

遵循苹果的 API 设计规范，当协议是用来「描述一个东西是什么」时，协议名应该是一个名词，比如：`Collection`、`WidgetFactory`。当协议是用来「描述一种能力」时，协议名应该以 `-ing`、`-able` 或 `-ible` 结尾，比如：`Equatable`、`Resizing`。

<a id="delegate"></a>
### 代理

创建代理方法时，方法的第一个匿名参数应该表示代理源对象。

**推荐**

```swift
func namePickerView(_ namePickerView: NamePickerView, didSelectName name: String)
func namePickerViewShouldReload(_ namePickerView: NamePickerView) -> Bool
```

**不推荐**

```swift
func didSelectName(namePicker: NamePickerViewController, name: String)
func namePickerShouldReload() -> Bool
```

<a id="generics"></a>
### 泛型

泛型名应该有较好的阅读性，用首字母大写的驼峰式命名。当一个类型没有有意义的关系和角色，使用传统的 `T`、`U`、`V` 来替代。

**推荐**

```swift
struct Stack<Element> { ... }
func write<Target: OutputStream>(to target: inout Target)
func swap<T>(_ a: inout T, _ b: inout T)
```

**不推荐**

```swift
struct Stack<T> { ... }
func write<target: OutputStream>(to target: inout target)
func swap<Thing>(_ a: inout Thing, _ b: inout Thing)
```

<a id="class-prefixes"></a>
### 类名前缀

Swift 的类型会被自动包含到它所在模块的命名空间中，所以没有必要再给 Swift 的类型添加前缀了。如果两个不同模块的存在相同的名字，你可以通过在它们前面添加模块名来避免冲突。当然，你应该只在必要的时候才添加模块名前缀。

```swift
import SomeModule
let myClass = MyModule.UsefulClass()
```

<a id="use-type-inferred-context"></a>
### 使用上下文类型推断

使用编译器的上下文类型推断来编写简洁清晰的代码。

**推荐**

```swift
let selector = #selector(viewDidLoad)
view.backgroundColor = .red
let toView = context.view(forKey: .to)
let view = UIView(frame: .zero)
```

**不推荐**

```swift
let selector = #selector(ViewController.viewDidLoad)
view.backgroundColor = UIColor.red
let toView = context.view(forKey: UITransitionContextViewKey.to)
let view = UIView(frame: CGRect.zero)
```

<a id="code-organization"></a>
## 代码组织

使用 `// MARK: -` 根据「代码功能类别」、「protocol/delegate 方法实现」等依据对代码进行分块组织。代码的组织顺序从整体上尽量遵循我们的认知顺序。

<a id="protocol-conformance"></a>
### 协议实现

其中，当你为一个类实现某些协议时，推荐添加一个独立的 `extension` 来实现具体的协议方法，这样可以让协议相关的代码聚合在一起，从而保持代码结构的清晰性。

**推荐**

```swift
class MyViewController: UIViewController {
    // class stuff here
}

// MARK: - UITableViewDataSource
extension MyViewController: UITableViewDataSource {
    // table view data source methods
}

// MARK: - UIScrollViewDelegate
extension MyViewController: UIScrollViewDelegate {
    // scroll view delegate methods
}
```

**不推荐**

```swift
class MyViewController: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
    // all methods
}
```

由于编译器不允许在派生类重复声明对协议的实现，所以并不要求总是复制基类的 `extension` 组。尤其当这个派生类是一个终端类，只有少量的方法需要重载时。何时保留 extension 组，这个应该由作者自己决定。

对于 UIKit 的 ViewControllers，可以考虑将 Lifecycle、Custom Accessors、IBAction 放在独立的 `extension` 中实现。

<a id="unused-code"></a>
### 无用代码

无用的代码，包括 Xcode 代码模板提供的默认代码，以及占位的评论，都应该被删掉。除非你是在写教程需要读者来阅读你注释的代码。

**推荐**

```swift
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return Database.contacts.count
}
```

**不推荐**

```swift
override func didReceiveMemoryWarning() {
    super.didReceiveMemoryWarning()
    // Dispose of any resources that can be recreated.
}

override func numberOfSections(in tableView: UITableView) -> Int {
    // #warning Incomplete implementation, return the number of sections
    return 1
}

override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    // #warning Incomplete implementation, return the number of rows
    return Database.contacts.count
}
```

<a id="minimal-imports"></a>
### 最小引用

只 import 你需要的模块。比如，如果引用 `Foundation` 以及足够，就不要再引用 `UIKit` 了。

<a id="spacing"></a>
## 空格

- 使用 Tab 而非空格。
- 方法的大括号以及其他的大括号（`if`/`else`/`switch`/`while` 等）总是与关联的程序语句在同一行打开，而在新起的一行结束。

**推荐**

```swift
if user.isHappy {
    // Do something
} else {
    // Do something else
}
```

**不推荐**

```swift
if user.isHappy
{
    // Do something
}
else {
    // Do something else
}
```
- 方法之间应该保留一行空格来使得代码结构组织更清晰。在方法中，可以用空行来隔开功能块，但是当一个方法中存在太多功能块时，那就意味着你可能需要重构这个大方法为多个小方法了。
- 冒号的左边总是不空格，右边空 1 格。除了在三元运算符 `? :` 和空字典 `[:]` 中。

**推荐**

```swift
class TestDatabase: Database {
    var data: [String: CGFloat] = ["A": 1.2, "B": 3.2]
}
```

**不推荐**

```swift
class TestDatabase : Database {
    var data :[String:CGFloat] = ["A" : 1.2, "B":3.2]
}
```

<a id="comments"></a>
## 注释

只在需要的时候添加注释来解释一段代码的意义，注释要么保持与对应的代码一起更新，要不然就删掉。

避免使用在代码中使用块注释，代码应该是自解释的。除非你的注释是用来生成文档的。

<a id="classes-and-structures"></a>
## 类和结构体

<a id="which-one-to-use"></a>
### 使用哪个

结构体是值类型，使用结构体来表示那些没有区别性的事物。一个包含 [a, b, c] 元素的数组和另一个包含 [a, b, c] 元素的数组是完全可替换的，你用第一个数组和用第二个数组没有任何区别，因为它们代表着同样的东西。所以数组是结构体。

类是引用类型，使用类来表示那些有区别性的事物。你用类来表示「人」这个概念，是因为两个「人」的实例是两个不一样的事情。两个「人」的实例就算拥有相同的姓名、生日，也不代表他们是一样的。但是「人」的生日数据应该用结构体表示，因为一个 1950-03-03 和另一个 1950-03-03 是一回事，日期这个概念没有区别性。

有时候，一些概念本应是用结构体，但是由于历史原因被实现为类了，比如 NSDate、NSSet。

<a id="example-definition"></a>
### 定义示例

以下是一个设计较好的定义示例：

```swift
class Circle: Shape {
    var x: Int, y: Int
    var radius: Double
    var diameter: Double {
        get {
            return radius * 2
        }
        set {
            radius = newValue / 2
        }
    }
    
    init(x: Int, y: Int, radius: Double) {
        self.x = x
        self.y = y
        self.radius = radius
    }
    
    convenience init(x: Int, y: Int, diameter: Double) {
        self.init(x: x, y: y, radius: diameter / 2)
    }
    
    override func area() -> Double {
        return Double.pi * radius * radius
    }
}

extension Circle: CustomStringConvertible {
    var description: String {
        return "center = \(centerString) area = \(area())"
    }
    private var centerString: String {
        return "(\(x),\(y))"
    }
}
```

上面的例子展示了下面的设计准则：

- 属性、变量、常量和参数等在声明时，`:` 号后有空格，而前面没有空格，比如 `x: Int`, 和 `Circle: Shape`。
- 定义多个变量或数据结构时，如果它们关系紧密，可以定义在同一行。
- 缩进 getter，setter 和属性观察器的定义。
- 不要添加诸如 `internal` 这样的默认修饰符，也不要在重写一个方法时添加访问修饰符。
- 使用 `extension` 来组织方法。
- 使用 `private extension` 隐藏非公开的细节，例如上述代码示例中 `centerString` 属性的实现。

<a id="use-of-self"></a>
### 使用 Self

为了简洁，能不用 `self` 的地方就不用，因为 Swift 不需要用它来访问属性或调用方法。

在下面情况中，你需要使用 `self`：

- 在构造器中，为了区别传入的参数和属性。
- 在 `@escaping` 闭包中访问属性，编译器要求用 `self`。

<a id="computed-properties"></a>
### 计算属性

为了简洁，如果计算属性是只读的，那么就省略 get。只有当同时写了 set 语句时，才写 get 语句。

**推荐**

```swift
var diameter: Double {
    return radius * 2
}
```

**不推荐**

```swift
var diameter: Double {
    get {
        return radius * 2
    }
}
```

<a id="final"></a>
## Final

如果类不会被继承，那么将它设为 `final` 的。

```swift
final class Box<T> {
    let value: T
    init(_ value: T) {
        self.value = value
    }
}
```

<a id="function-declarations"></a>
## 函数声明

将较短的函数声明写为一行，包括左大括号。

```swift
func reticulateSplines(spline: [Double]) -> Bool {
    // reticulate code goes here
}
```

对于较长的函数声明，在合适的地方换行，并在新起的一行加缩进。

```swift
func reticulateSplines(
    spline: [Double],
    adjustmentFactor: Double,
    translateConstant: Int, 
    comment: String)
    -> Bool 
{
    // reticulate code goes here
}
```






**推荐**

```swift
```

**不推荐**

```swift
```
