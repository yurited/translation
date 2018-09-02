

# Swift 中的设计模式 #1 工厂方法与单例方法

title: [Design Patterns] in Swift #1: Factory Method and Singleton”
date: 2018-07-24
tags: [Design Patterns]
categories: Swift
permalink: design-pattern-creational

- - -
原文链接=https://www.appcoda.com/design-pattern-creational/
作者=Andrew Jaffee
原文日期=2018-07-24
译者=BigLuo
校对=
定稿=

大概有23种经典的设计模式被 “Gang of Four” (“GOF”) Erich Gamma,Richard Helm,Ralph Johonson，和 John Vlissides 整理在他们“[设计模式：面向对象软件设计复用的基本原理](https://smile.amazon.com/Design-Patterns-Object-Oriented-Addison-Wesley-Professional-ebook/dp/B000SEIBB8/)” 的重要著作里。本教程主要讨论被 GOF 称为创建型模式中的工厂方法和单例方法。

软件开发一直在努力的模拟真实世界的场景，希望通过创建工具的方式来加强人类的场景体验。财富管理工具，例如：像亚马逊或者 eBay 这样的银行 App 和购物辅助工具，确实相比十年前来说给消费者带来了更大的生活便利。回顾我们的开发路程。当应用变得更加强大易用时，应用的开发也已变得更加复杂。

所以开发者也创造出了一系列最佳实践，像面[向对象编程](http://iosbrain.com/blog/2017/02/26/intro-to-object-oriented-principles-in-swift-3-via-a-message-box-class-hierarchy/)，[面向协议编程](https://www.appcoda.com/pop-vs-oop/)，值语义 （[value semantics](http://iosbrain.com/blog/2018/03/28/protocol-oriented-programming-in-swift-is-it-better-than-object-oriented-programming/#value_semantics)），局部推断（[local reasoning](http://iosbrain.com/blog/2018/03/28/protocol-oriented-programming-in-swift-is-it-better-than-object-oriented-programming/#local_reasoning)）来处理这些复杂问题。比如通过定义接口（像 Swift 的扩展）、语法糖的方式将大块代码分割变小。还有我没提及的确是最重要的值得重视的实践之一，设计模式的使用。

## 设计模式

设计模式是开发者管理软件复杂性的重要工具。作为一般的模板技术，它很好的对软件中类似的、复现的、容易识别的问题进行了概念化。将它当作一个最佳实践应用到那些你日常会遇到的编程场景中，例如，在不了解类簇实现细节的情况下创建一个类簇相关的对象。设计模式主要是用在那些经常发生的问题场景中。他们被多次使用是因为这些问题很普遍，用一个具体的例子应该能带来理解上的帮助。

设计模式在某些案例中没有得到明显的体现，比如像一个含有 11 个 整数（`Int`）的 Swift array 的迭代。例如，GoF 定义了迭代器模式来为那些细节繁琐的集合对象提供共有的接口。设计模式不是语言编码。它是用于解决相同软件场景问题的一套实用的指导规则。

记住我在 AppCoda 讨论的 [“Model-View-ViewModel” or “MVVM”](https://www.appcoda.com/mvvm-vs-mvc/) 与非常著名的 [“Model-View-Controller” or “MVC”](https://www.appcoda.com/mvvm-vs-mvc/) 设计模式，它们一直受到 Apple 和苹果开发者的支持。

这两种模式一般用在整个应用中。MVVM 和 MVC 是架构设计模式，用于将 UI 从 app 数据代码和展示层逻辑中分离出来，以及将应用的数据从核心数据流程或者业务逻辑中分离。 GoF 设计模式本质上更具体，旨在解决基于程序代码中的具体问题。在一个 app 里面你也许会用到 3 种、7 种或者12 种 GOF 设计模式。除了迭代器例子，代理模式是设计模式中另一个很好的例子，尽管它在 GoF 的 23 种设计模式的名单中没有被介绍的很具体。

当 GoF 的这本书作为大量的开发者的圣经而存在时，也不乏有它的诋毁者，我们会在文章的结尾处讨论这个话题。

## 设计模式的类别

GoF 将 23 种设计模式整理分为了 3 类，“创建型”、“结构型”和“行为型”。这个教程讨论创建型类别里面的两种模式。像实例对象和类的实现，模式的作用是让复杂对象的创建变得简单、易于理解，易于维护，隐藏细节。

隐藏复杂度（封装）是聪明的程序员最高目标之一。例如，面向对象（OOP）类可以提供非常复杂的，成熟的和强大的函数而不需要知道任何关于类内部间的工作方式。在创建型模式中，开发者甚至不需要知道类的的属性和方法，但如果需要，程序员可以看到接口 - 在 Swift 中的协议中 - 也可以对那些感兴趣的类进行扩展。你会在我的第一个“工厂方法”设计模式的例子中明白我的意思。

## 工厂方法设计模式

如果你已经探索过 GOF 设计模式或在 OOP 的世界里花费了很多时间，你大概至少听说过“抽象工厂”、“工厂”，或者“工厂方法”模式。当我们为他们的“具体”命名抱怨时，我将给你展示一个最切合“工厂方法”的例子。
在这个范例中，你通过工厂方法创建对象，而不需要知道类的构造器和关于类和类层次结构的任何信息。这样来了很大的方便。用少量的代码创建 UI 与相关功能。我的工厂方法项目例子，在 [GitHub](https://github.com/appcoda/FactoryMethodInSwift) 可下载，展示了在复杂类层次结构中，如何轻松的使用对象。拿一个团队中的 UI 开发者来说：
![](https://www.appcoda.com/wp-content/uploads/2018/07/Factory_Method.gif)

大多数成功的 apps 有一个风格一致的主题 。为保证 app 主题风格统一，我们假设所有的形状在 app 中有着相同的颜色和尺寸。在 app 的自定义按钮和登录背景图中，这些形状也许会很有用。

假设设计团队同意使用我的代码作为 app 的主题背景图片。来看到假定UI 开发者无须担心的这些代码，首先是一个协议，接着是类继承这个协议，和工厂方法。

 `ShapeFactory.swift` 文件是一个用于在视图控制器内绘制形状的协议。因为可用于各种目的，所以他的访问级别是 public：

```swift
// 这些值被图形设计团队预先选定
// these values have been pre-selected by
// the graphics and design teams
let defaultHeight = 200
let defaultColor = UIColor.blue
 
protocol HelperViewFactoryProtocol {
    
    func configure()
    func position()
    func display()
    var height: Int { get }
    var view: UIView { get }
    var parentView: UIView { get }
    
}
```
记住这个 `UIView` 类有一个默认的矩形 `frame` 属性 ，所以我可以很轻松的来创建出形状基类 `Square`: 

```swift
fileprivate class Square: HelperViewFactoryProtocol {
    
    let height: Int
    let parentView: UIView
    var view: UIView
    
    init(height: Int = defaultHeight, parentView: UIView) {
        
        self.height = height
        self.parentView = parentView
        view = UIView()
        
    }
    
    func configure() {
        
        let frame = CGRect(x: 0, y: 0, width: height, height: height)
        view.frame = frame
        view.backgroundColor = defaultColor
        
    }
    
    func position() {
        
        view.center = parentView.center
        
    }
 
    func display() {
        
        configure()
        position()
        parentView.addSubview(view)
        
    }
    
} // end class Square
```
注意到我利用 OOP 来重用我的代码，这样能让我的 shape 层级更加简化和可维护。`Circle` 和 `Rectangle` 类是 `Square` 类的特化 （记住通过一个完美的正方形来绘制一个圆是多么的简单）
```swift
fileprivate class Circle : Square {
    
    override func configure() {
        
        super.configure()
        
        view.layer.cornerRadius = view.frame.width / 2
        view.layer.masksToBounds = true
        
    }
    
} // end class Circle
 
fileprivate class Rectangle : Square {
    
    override func configure() {
        
        let frame = CGRect(x: 0, y: 0, width: height + height/2, height: height)
        view.frame = frame
        view.backgroundColor = UIColor.blue
        
    }
    
} // end class Rectangle
```
我使用 `fileprivate` 来强调工厂方法模式背后的一个目的：封装。你可以看到不用改变下面的工厂方法的前提下，对 `shape` 类的层级结构进行修改和扩展是很容易的。这是工厂方法的代码，它们让对象的创建如此的抽象和简单
```swift
enum Shapes {
    
    case square
    case circle
    case rectangle
    
}

class ShapeFactory {
    
    let parentView: UIView
    
    init(parentView: UIView) {
        
        self.parentView = parentView
        
    }
    
    func create(as shape: Shapes) -> HelperViewFactoryProtocol {
        
        switch shape {
            
        case .square:
            
            let square = Square(parentView: parentView)
            return square
            
        case .circle:
            
            let circle = Circle(parentView: parentView)
            return circle
            
        case .rectangle:
            
            let rectangle = Rectangle(parentView: parentView)
            return rectangle
            
        }
        
    } // end func display
    
} // end class ShapeFactory

// 公共的工厂方法来展示形状
// Public factory method to display shapes.
func createShape(_ shape: Shapes, on view: UIView) {
    
    let shapeFactory = ShapeFactory(parentView: view)
    shapeFactory.create(as: shape).display()
    
}

// 选择公共的工厂方法来展示形状
// Alternative public factory method to display shapes.

// 严格地来说，工厂方法应该返回相关类中的一个。
// Technically, the factory method should return one of
// a number of related classes.
func getShape(_ shape: Shapes, on view: UIView) -> HelperViewFactoryProtocol {
    
    let shapeFactory = ShapeFactory(parentView: view)
    return shapeFactory.create(as: shape)
    
}
```
注意到：我已经写了一个类工厂和两个工厂方法来让你思考。严格的说，一个工厂方法应该返回一些类中的一个，这些类有着相同的基类或者协议。因为这里的目的是在视图上绘制一个形状，我个人更喜欢 `createShape(_:view:)` 这个方法。这是一个可供选择的点子，有时用于试验和探索新的可能性。

最后，我展示了两个工厂方法绘制形状的使用方式。UI 开发者不必必需知道形状类是如何被编码出来的。尤其是他/她不必为形状类如何被初始化而担忧。在 `ViewController.swift` 文件中的代码很容易阅读。

```swift
import UIKit
 
class ViewController: UIViewController {
    
    override func viewDidLoad() {
        super.viewDidLoad()
        //在加载视图后进行添加设置，一般是从nib
        // Do any additional setup after loading the view, typically from a nib.
        
    }
 
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // 废弃掉那些可以被重新创建的资源
        // Dispose of any resources that can be recreated.
    }
 
    @IBAction func drawCircle(_ sender: Any) {
        
		// 仅仅用于绘制形状
        // just draw the shape
        createShape(.circle, on: view)
        
    }
    
    @IBAction func drawSquare(_ sender: Any) {

		// 绘制图形
        // just draw the shape
        createShape(.square, on: view)
        
    }
    
    @IBAction func drawRectangle(_ sender: Any) {

		// 从工厂获取一个对象并使用他来绘制一个形状
        // actually get an object from the factory
        // and use it to draw the shape
        let rectangle = getShape(.rectangle, on: view)
        rectangle.display()
        
    }
    
} // end class ViewController  //结束 ViewController 这个类。
```
## 单例设计模式

大部分 iOS 开发者熟悉单例模式。回想一下    `UNUserNotificationCenter.current()`，`UIApplication.shared`，或 `FileManager.default` 如果你想要发送通知，或者在 Safari 里面打开一个 URL，或者操作 iOS 文件，你必须使用它们各自的单例。单例可以很好的用于保护共享资源，提供有且仅有一个对象实例进入一些系统，并且支持对象执行一些 app 层面的协作类型。在这里我们看到的是通过包装 iOS 内建的单例来提供一个值的好处。

作为一个单例，我们需要确保这个类：

* 声明和初始化一个 static 的类的常量属性，然后命名那个属性为 `shared` 来表明这个类的实例是一个单例（默认是共有的）；
* 为我们想要控制和保护的一些资源声明一个私有的属性。且只能通过 `shared` 共享。
* 声明一个私有初始化方法，只有我们的单例类能够初始化它， 在 `init` 的内部，我们初始化我们想要用于控制的共享资源。

通过定义一个 `shared` 静态常量来创建一个类的 `private` 初始化方法。我们要确保这个类只有一个实例，该类只能初始化一次，并且共享的实例在 app 的任何地方都能获取。就这样我们创建了一个单例！

我的单例案例的项目，在 [GitHub](https://github.com/appcoda/SingletonInSwift) 可用，展示了一个开发团队如何安全的、连贯的，最少错误的存储用户的偏好。这是我的 app 样品，能够记忆用户的密码文本，它们偏好设置是可见或隐藏的，事后发现，这并不是最好的想法，但我需要一个例子来向你展示我代码的工作机制。这段代码完全是出于教学的目的。我建议你永远不要让你的密码暴露。你可以看到用户可以设置他们的的密码偏好 — 且密码偏好被存储在 `UserDefaults`:

![](https://www.appcoda.com/wp-content/uploads/2018/07/Show_Pwd.gif)

当用户最终关闭回到 app 时，注意到他/她的密码偏好被记录了：

![](https://www.appcoda.com/wp-content/uploads/2018/07/Remember_Pwd_Setting.gif)

让我向你展示 `PreferencesSingleton.swift` 文件中的代码片段，在行内注释里，你将会看到我想表达确切意思。

```swift
class UserPreferences {

	// 用类的初始化方法创建一个静态的，常量实例。
    static let shared = UserPreferences()
    
	// 这是一个私有的，收我们保护的资源共享的。
    private let userPreferences: UserDefaults
    
	// 一个私有的初始化方法只能被类本身调用。
    private init() {
        
	// 获取 iOS 共享单例。我们在这里包装了它。
        userPreferences = UserDefaults.standard
        
    }
 
} // end class UserPreferences
```
就如我所理解的 Swift。这里没有必要担心影响说 app 启动时，静态属性和全局变量在初始化时是不是以懒加载形式进行的。

你也许会问，“为什么他通过包装另一个`UserDefaults`单例的方式来创建一个单例？” 首先，我主要的目的是在这向你展示在 Swift 中最佳的用于创建和使用单例的做法。 用户偏好是一个资源类型，应该有一个单一的入口。所以 `UserDefaults` 服务于一个非常明显的例子。其次，想一下你曾多少次看到 `UserDefaults` 在 app 的代码中被滥用。

我看到的 `UserDefaults` (或者之前的 `NSUserDefaults`) 在一些 app 项目代码中使用起来没有任何的条理和原由。对于在用户偏好的每个键都是写成了一个单引用。我刚刚在我的代码中发现了一个 bug 我把 “Switch” 拼写成了 “swithc” ，由于我使用了复制和粘贴，在我发现问题之前，我已经创建了不少以 “swithc” 的实例。 如果其他团队在这个 app 开始或者继续使用 “switch” 作为一个键来存储对应的值呢？这个 app 不能保存之前的那种状态。 我们希望维持 app 的部分状态，通过使 `UserDefaults`  保存的 strings 键来映射对应的值。这是描述值的好方式。因为这样让值的意思明确、简单易懂，和容易记忆。但这也不是说通过 strings 来描述是没有风险的。

在我讨论的 “swithc” 与 “switch”中。你们大多数人可能了解到了被称为 “stringly-typed” 代码, 当用 strings 作为唯一的标识符会带来细微的变化，最终会因为拼写错误带来灾难性的错误。Swift 编译器不能帮助我们避免 “stringly-typed” 错误。

解决 “stringly-typed” 错误方式是在 Swift `enum`  格式中使用 string 常量。不仅可以让我们来标准化字符串的使用，而且可以让我们以分类的方式组织他们。让我们再次看到 `PreferencesSingleton.swift`:
```swift
class UserPreferences {
    
    enum Preferences {
        
        enum UserCredentials: String {
            case passwordVisibile
            case password
            case username
        }
        
        enum AppState: String {
            case appFirstRun
            case dateLastRun
            case currentVersion
        }
 
    } // end enum Preferences
```
我们从单例模式的定义开始，但我确实想介绍清楚在大多数我的 apps 中，为什么我自己使用一个单例来包装 `UserDefaults` 。这里有很多以值的方式来增加新功能，通过简单的对 `UserDefaults` 的包装来加强代码的健壮性。当在获取和设置偏好进入大脑的时就应该要想到提供错误校验。我想加入的另一个特性是为相同的用户偏好的使用提供便捷方法，像是如何处理密码。你将会看到我下面的代码。所有的内容都在 `PreferencesSingleton.swift` 文件：

```swift
import Foundation
 
class UserPreferences {
    
    enum Preferences {
        
        enum UserCredentials: String {
            case passwordVisibile
            case password
            case username
        }
        
        enum AppState: String {
            case appFirstRun
            case dateLastRun
            case currentVersion
        }
 
    } // end enum Preferences
    	
    // 创建一个静态、常量实例并初始化
    // Create a static, constant instance of
    // the enclosing class (itself) and initialize.
    static let shared = UserPreferences()
    
    // 这是我们保护的一个私有的、共享资源
    // This is the private, shared resource we're protecting.
    private let userPreferences: UserDefaults
    
    // 一个只能蓓蕾本身调用的私有的初始化方法
    // A private initializer can only be called by
    // this class itself.
    private init() {
        // 获取 iOS 共享单例。我们在这里包装它
        // Get the iOS shared singleton. We're
        // wrapping it here.
        userPreferences = UserDefaults.standard
 
    }
    
    func setBooleanForKey(_ boolean:Bool, key:String) {
        
        if key != "" {
            userPreferences.set(boolean, forKey: key)
        }
        
    }
    
    func getBooleanForKey(_ key:String) -> Bool {
        
        if let isBooleanValue = userPreferences.value(forKey: key) as! Bool? {
            print("Key \(key) is \(isBooleanValue)")
            return true
        }
        else {
            print("Key \(key) is false")
            return false
        }
        
    }
    
    func isPasswordVisible() -> Bool {
        
        let isVisible = userPreferences.bool(forKey: Preferences.UserCredentials.passwordVisibile.rawValue)
        
        if isVisible {
            return true
        }
        else {
            return false
        }
        
    }
```
如果你看到我的 `ViewController.swift` 文件，你将会看到访问和构建单例是多么的容易：
```swift
import UIKit
 
class ViewController: UIViewController {
    
    @IBOutlet weak var passwordTextField: UITextField!
    @IBOutlet weak var passwordVisibleSwitch: UISwitch!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
        
        if UserPreferences.shared.isPasswordVisible() {
            passwordVisibleSwitch.isOn = true
            passwordTextField.isSecureTextEntry = false
        }
        else {
            passwordVisibleSwitch.isOn = false
            passwordTextField.isSecureTextEntry = true
        }
        
    } // end func viewDidLoad
 
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    @IBAction func passwordVisibleSwitched(_ sender: Any) {
        
        let pwdSwitch:UISwitch = sender as! UISwitch
        
        if pwdSwitch.isOn {
            passwordTextField.isSecureTextEntry = false
            UserPreferences.shared.setPasswordVisibity(true)
        }
        else {
            passwordTextField.isSecureTextEntry = true
            UserPreferences.shared.setPasswordVisibity(false)
        }
        
    } // end func passwordVisibleSwitched
```

## 结论

一些评论家声称设计模式在一些编程语言中的使用缺乏证明，在代码中看到反复出现的设计模式是很槽糕的一件事情。我不同意这个说法。期望一个编程语言对每件事情的处理都有对应的特性是很愚蠢的。这很可能会导致一个臃肿的语言，像 C++ 一样正在变得更大、更复杂，以致很难被学习、使用和维护。认识并解决反复出现的问题是人的一种积极性格并且这确实值得我们强化。设计模式是从历史中学习到的一个成功案例，有一些事情，人类做了很多次都失败了。对一些相同的问题进行抽象和标准化，让这些好的解决方案散播出去。

像 Swift 这样的简单紧凑的语言和设计模式这样一系列最佳实践的组合是一个理想中的令人愉快的媒介。风格统一的代码一般来说都具有较好的可读性和易维护性。不过也要记住，在数以百万的开发者不断地讨论和分享下，设计模式也在不断的发展变化，这些美好事物被万维网联系在一起，这种开发人员的讨论持续的引领着自我调节的集体智慧。
