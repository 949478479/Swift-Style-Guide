# Swift 编码风格规范

# 目录

- [命名](#naming)
    - [枚举](#enumeration)
    - [协议](#protocol)
    - [代理](#delegate)
    - [使用上下文类型推断](#use-type-inferred-context)
    - [泛型](#generics)
    - [类名前缀](#class-prefixes)
- [代码组织](#code-organization)
    - [协议实现](#protocol-conformance)
    
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

<a id="code-organization"></a>
## 代码组织

使用 // MARK: - 根据「代码功能类别」、「protocol/delegate 方法实现」等依据对代码进行分块组织。代码的组织顺序从整体上尽量遵循我们的认知顺序。

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
