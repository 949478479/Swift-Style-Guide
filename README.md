# Swift 编码风格规范

# 目录

- [命名](#naming)
    - [枚举](#enumeration)
    - [协议](#protocol)
    - [代理](#delegate)
    - [泛型](#generics)
    - [类名前缀](#class-prefixes)
    - [上下文类型推导](#type-inferred-context)
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
- [闭包表达式](#closure-expressions)
- [类型](#types)
    - [常量](#constants)
    - [静态方法和静态类型属性](#static-methods-and-variable-type-properties)
    - [Optional](#optionals)
    - [结构体构造器](#struct-constructor)
    - [懒加载](#lazy-initialization)
    - [类型推导](#type-inferred)
    - [类型标注](#type-annotation)
- [语法糖](#syntactic-sugar)
- [函数和方法](#functions-vs-methods)
- [内存管理](#memory-management)
    - [延伸对象生命周期](#extending-lifetime)
- [访问控制](#access-control)
- [控制流](#control-flow)
- [黄金路径](#golden-path)
    - [失败的 Guard](#failing-guards)
- [分号](#semicolons)
- [圆括号](#parentheses)

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

<a id="type-inferred-context"></a>
### 上下文类型推导

依靠编译器的上下文类型推导来编写简洁清晰的代码。

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

<a id="closure-expressions"></a>
## 闭包表达式

如果参数列表只有最后一个参数是闭包类型，则尽可能使用尾闭包语法。在所有情况下给闭包参数一个描述性强的命名。

**推荐**

```swift
UIView.animate(withDuration: 1.0) {
    self.myView.alpha = 0
}

UIView.animate(withDuration: 1.0, animations: {
    self.myView.alpha = 0
}, completion: { finished in
    self.myView.removeFromSuperview()
})
```

**不推荐**

```swift
UIView.animate(withDuration: 1.0, animations: {
    self.myView.alpha = 0
})

UIView.animate(withDuration: 1.0, animations: {
    self.myView.alpha = 0
}) { f in
    self.myView.removeFromSuperview()
}
```

对于上下文清晰的单表达式闭包，使用隐式的返回值：

```swift
attendeeList.sort { a, b in
    a > b
}
```

在链式方法调用中使用尾闭包语法时，需要确保上下文清晰可读。

```swift
let value = numbers.map { $0 * 2 }.filter { $0 % 3 == 0 }.index(of: 90)

let value = numbers
    .map { $0 * 2 }
    .filter { $0 % 3 == 0 }
    .index(of: 90)
```

<a id="types"></a>
## 类型

如果可以的话，总是优先使用 Swift 提供的原生类型。Swift 提供了对 Objective-C 的桥接，所以你可以使用所有需要的方法。

**推荐**

```swift
let width = 120.0                                    // Double
let widthString = (width as NSNumber).stringValue    // String
```

**不推荐**

```swift
let width: NSNumber = 120.0                          // NSNumber
let widthString: NSString = width.stringValue        // NSString
```

<a id="constants"></a>
### 常量

用 `let` 关键字来定义常量，使用 `var` 关键字来定义变量。如果变量不需要被修改，则应该总是选择使用 `let`。

一个建议是，总是使用 `let` 除非编译器报警告诉你需要使用 `var`。

使用类型而非实例属性来定义常量。最好也别用全局常量，这样能更好区分常量和实例属性。

**推荐**

```swift
enum Math {
    static let e = 2.718281828459045235360287
    static let root2 = 1.41421356237309504880168872
}

let hypotenuse = side * Math.root2
```

使用 case-less 枚举的优势在于它不会被意外初始化，而仅仅作为一个命名空间来用。

**不推荐**

```swift
let e = 2.718281828459045235360287 // 污染全局命名空间
let root2 = 1.41421356237309504880168872

let hypotenuse = side * root2 // root2 含义不够明确
```

<a id="static-methods-and-variable-type-properties"></a>
### 静态方法和静态类型属性

静态方法和静态类型属性与全局方法和全局变量类似，应该谨慎使用。

<a id="optionals"></a>
### Optional

当一个变量或函数返回值可以为 nil 时，用 `?` 将其声明为 Optional 的。

对于在使用前一定会被初始化的实例变量，用 `!` 将其声明为隐式解包类型。

在访问一个 Optional 值时，如果该值只被访问一次，或者之后需要连续访问多个 Optional 值，请使用链式 Optional 语法。

```swift
self.textContainer?.textLabel?.setNeedsDisplay()
```

对于需要将 Optional 值解开一次，多处使用的情况，使用 Optional 绑定更为方便。

```swift
if let textContainer = self.textContainer {
    // do many things with textContainer
}
```

不要使用类似 `optionalString`、`maybeView` 这种名字来命名 Optional 的变量或属性，因为这层意思以及明显的体现在他们的类型声明上了。

对于 Optional 绑定，推荐直接用同样的名字，不要用 `unwrappedView`、`actualLabel` 这种命名。

**推荐**

```swift
var subview: UIView?
var volume: Double?

// later on...
if let subview = subview, let volume = volume {
    // do something with unwrapped subview and volume
}
```

**不推荐**

```swift
var optionalSubview: UIView?
var volume: Double?

if let unwrappedSubview = optionalSubview {
    if let realVolume = volume {
        // do something with unwrappedSubview and realVolume
    }
}
```

<a id="struct-constructor"></a>
### 结构体构造器

使用 Swift 原生的结构体构造器。

**推荐**

```swift
let bounds = CGRect(x: 40, y: 20, width: 120, height: 80)
let centerPoint = CGPoint(x: 96, y: 42)
```

**不推荐**

```swift
let bounds = CGRectMake(40, 20, 120, 80)
let centerPoint = CGPointMake(96, 42)
```

<a id="lazy-initialization"></a>
### 懒加载

使用懒加载机制来在对象的生命周期中实现更细粒度的内存和逻辑控制。尤其是 `UIViewController`，在加载其 views 时，尽量采用懒加载方式。可以使用 `{ }()` 这种闭包的方式或者私有工厂的方式来实现懒加载。

```swift
lazy var locationManager: CLLocationManager = self.makeLocationManager()

private func makeLocationManager() -> CLLocationManager {
    let manager = CLLocationManager()
    manager.desiredAccuracy = kCLLocationAccuracyBest
    manager.delegate = self
    manager.requestAlwaysAuthorization()
    return manager
}
```

**注意**

- 这里不需要 `[unowned self]`，因为这里没有引起循环引用。
- CLLocationManager 有一个副作用，会唤起向用户申请权限的 UI 界面，所以在这里使用懒加载机制可以达到更细粒度的内存和逻辑控制。

<a id="type-inferred"></a>
### 类型推导

为了代码紧凑，推荐尽量使用 Swift 的类型推导。不过，对于 `CGFloat`、`Int16` 这种，推荐尽量指定明确的类型。

**推荐**

```swift
let message = "Click the button"
let currentBounds = computeViewBounds()
var names = ["Mic", "Sam", "Christine"]
let maximumWidth: CGFloat = 106.5
```

**不推荐**

```swift
let message: String = "Click the button"
let currentBounds: CGRect = computeViewBounds()
let names = [String]()
```

<a id="type-annotation"></a>
### 类型标注

对于空的数组和字典，使用类型标注。

**推荐**

```swift
var names: [String] = []
var lookup: [String: Int] = [:]
```

**不推荐**

```swift
var names = [String]()
var lookup = [String: Int]()
```

<a id="syntactic-sugar"></a>
## 语法糖

推荐使用简短的声明。

**推荐**

```swift
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?
```

**不推荐**

```swift
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```

<a id="functions-vs-methods"></a>
## 函数和方法

自由函数不属于任何一个类或类型，应该尽量少用。可以的话，尽量使用方法而非自由函数。这样可读性更好，也更易查找。

最适合使用自由函数的场景是这个函数功能与任何特定的类或类型都没有关联关系。

**推荐**

```swift
let sorted = items.mergeSorted()  
rocket.launch()  
```

**不推荐**

```swift
let sorted = mergeSort(items) 
launch(&rocket)
```

**自由函数示例**

```swift
let tuples = zip(a, b)  
let value = max(x, y, z)
```

<a id="memory-management"></a>
## 内存管理

在编码中应该避免循环引用。对于会产生循环应用的地方，使用 `weak` 和 `unowned` 来解决。此外，还可以使用值类型（`struct`、`enum`）来避免循环引用。

<a id="extending-lifetime"></a>
### 延伸对象生命周期

可以通过 `[weak self]`、`guard let strongSelf = self else { return }` 来延伸对象的生命周期。

相对于 `[unowned self]`，这里更推荐使用 `[weak self]`。`[unowned self]` 在其作用的对象被释放后，会造成野指针，而 `[weak self]` 则会将对应的引用置为 nil。

相对于 Optional 拆包，更推荐明确的延长生命周期。

**推荐**

```swift
resource.request().onComplete { [weak self] response in
    guard let strongSelf = self else {
        return
    }
    let model = strongSelf.updateModel(response)
    strongSelf.updateUI(model)
}
```

**不推荐**

```swift
// 如果闭包调用之前或调用过程中 self 被释放则会野指针崩溃
resource.request().onComplete { [unowned self] response in
    let model = self.updateModel(response)
    self.updateUI(model)
}
```

**不推荐**

```swift
// self 可能会在 updateModel 和 updateUI 方法执行之间被释放
resource.request().onComplete { [weak self] response in
    let model = self?.updateModel(response)
    self?.updateUI(model)
}
```

<a id="access-control"></a>
## 访问控制

开发的访问级别不需要把 `public` 写出来，但对于 `private`，则最好写出。

除了 `static`、`@IBAction`、`@IBOutlet`，一般情况下，总是把访问控制修饰符 `private` 放在属性修饰符的第一位。

**推荐**

```swift
private let message = "Great Scott!"

class TimeMachine {  
    fileprivate dynamic lazy var fluxCapacitor = FluxCapacitor()
}
```

**不推荐**

```swift
fileprivate let message = "Great Scott!"

class TimeMachine {  
    lazy dynamic fileprivate var fluxCapacitor = FluxCapacitor()
}
```

<a id="control-flow"></a>
## 控制流

相对于 `while-condition-increment`，更推荐使用 `for-in`。

**推荐**

```swift
for _ in 0..<3 {
    print("Hello three times")
}

for (index, person) in attendeeList.enumerated() {
    print("\(person) is at position #\(index)")
}

for index in stride(from: 0, to: items.count, by: 2) {
    print(index)
}

for index in (0...3).reversed() {
    print(index)
}
```

**不推荐**

```swift
var i = 0
while i < 3 {
    print("Hello three times")
    i += 1
}

var i = 0
while i < attendeeList.count {
    let person = attendeeList[i]
    print("\(person) is at position #\(i)")
    i += 1
}
```

<a id="golden-path"></a>
## 黄金路径

当使用条件语句编写逻辑时，不要嵌套多个 `if` 语句。

**推荐**

```swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {
    guard let context = context else {
        throw FFTError.noContext
    }
    guard let inputData = inputData else {
        throw FFTError.noInputData
    }
    return frequencies
}
```

**不推荐**

```swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {
    if let context = context {
        if let inputData = inputData {
            return frequencies
        } else {
            throw FFTError.noInputData
        }
    } else {
        throw FFTError.noContext
    }
}
```

使用 `guard` 或 `if let` 拆包多个 Optional 时，推荐最小化嵌套。

**推荐**

```swift
guard
    let number1 = number1,
    let number2 = number2,
    let number3 = number3
    else {
        fatalError("impossible")
}
```

**不推荐**

```swift
if let number1 = number1 {
    if let number2 = number2 {
        if let number3 = number3 {
            // ...
        } else {
            fatalError("impossible")
        }
    } else {
        fatalError("impossible")
    }
} else {
    fatalError("impossible")
}
```

<a id="failing-guards"></a>
### 失败的 Guard

`guard` 语句一般都需要以某种方式退出执行。一般来说使用 `return`、`throw`、`break`、`continue`、`fatalError()` 即可。应该避免在退出时写大段的代码，如果确实需要在不同的退出点上编写退出清理逻辑，可以考虑使用 `defer` 来避免重复。

<a id="semicolons"></a>
## 分号

Swift 不需要在一行代码结束时使用分号。只有当你想把多行代码放在一行写时，才需要用分号隔开它们，但是一般不推荐这样做。

**推荐**

```swift
let swift = "not a scripting language"
```

**不推荐**

```swift
let swift = "not a scripting language";
```

<a id="parentheses"></a>
## 圆括号

条件判断语句外的圆括号不是必须的，推荐省略它们。

**推荐**

```swift
if name == "Hello" {
    print("World")
}
```

**不推荐**

```swift
if (name == "Hello") {
    print("World")
}
```

对于复杂的表达式，使用圆括号可以使代码更加一目了然。

**推荐**

```swift
let playerMark = (player == current ? "X" : "O")
```
