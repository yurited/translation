title: "Swift 中的属性"
date: 2018-03-15
tags: [Swift]
categories: [Thomas Hanning]
permalink: properties-in-swift
keywords: Swift, Properties
custom_title: Swift 中的属性
description: 本文简单介绍了 Swift 中属性

---
原文链接=http://www.thomashanning.com/properties-in-swift/
作者=Thomas Hanning
原文日期=2018-03-15
译者=Sunnyyoung
校对=
定稿=

<!--此处开始正文-->

Swift 中有两种类型的属性：存储属性与计算属性。存储属性将值（常量或者变量）保存为实例或类型的一部分，而计算属性没有存储值。

提示：这篇文章已经更新至 Swift 4

<!--more-->

## 存储属性

让我们从存储属性开始看起。想象一下你有一个名为 Circle 的类：

```swift
class Circle {

    var radius: Double = 0

}

let circle = Circle()
circle.radius = 10

print("radius: \(circle.radius)") //radius: 10.0
```

Circle 拥有名为 `radius` 的实例变量，默认值为 0。在 Swift 中，每个实例变量都为一个属性。因此你可以添加所谓的属性观察者。在 Swift 中有两种类型的属性观察者：一种在赋值之前调用，另一种在赋值之后调用。

在赋值后调用的属性观察者采用 `didSet` 关键字标记。在我们的示例中，你可以使用它来监测新分配的值：

```swift
class Circle {
    
    var radius: Double = 0 {
        didSet {
            if radius < 0 {
                radius = oldValue
            }
        }
    }
    
}
 
let circle = Circle()
 
circle.radius = -10
print("radius: \(circle.radius)") //radius: 0.0
 
circle.radius = 10
print("radius: \(circle.radius)") //radius: 10.0
```

在属性观察者中你可以通过变量 `oldValue` 来访问属性的旧值。

你还可以使用 `willSet` 属性观察者，它在赋值之前调用：

```swift
class Circle {
    
    var radius: Double = 0 {
        willSet {
            print("About to assign the new value \(newValue)")
        }
        didSet {
            if radius < 0 {
                radius = oldValue
            }
        }
    }
    
}
 
let circle = Circle()
 
circle.radius = 10 //About to assign the new value 10.0
```

在 `willSet` 中，你可以通过变量 `newValue` 来访问属性的新值。

## 计算属性

与存储属性相反，计算属性没有存储值。因此在每次调用计算属性时，都要计算该值。在 `Circle` 类中，你可以将属性 `area` 定义为计算属性：

```swift
class Circle {
    
    var radius: Double = 0 {
        didSet {
            if radius < 0 {
                radius = oldValue
            }
        }
    }
    
    var area: Double {
        get {
            return radius * radius * Double.pi
        }
    }

}

let circle = Circle()
circle.radius = 5

print("area: \(circle.area)") //area: 78.5398163397448
```
计算属性总是需要一个 `getter`。如果缺少 `setter`，则该属性称为只读属性。以下是一个很好的例子来说明 `setter`：

```swift
import Foundation

class Circle {
    
    var radius: Double = 0 {
        didSet {
            if radius < 0 {
                radius = oldValue
            }
        }
    }
    
    var area: Double {
        get {
            return radius * radius * Double.pi
        }
        set(newArea) {
            radius = sqrt(newArea / Double.pi)
        }
    }
    
}

let circle = Circle()

circle.area = 25

print("radius: \(circle.radius)") //radius: 2.82094791773878
```

现在在为 `area` 分配新值之后，`radius` 将会被计算出来。

## 存储属性的初始化

每个存储属性在它的对象实例化之后都必须有值。属性初始化有两种方法：

- 在 `init` 方法中初始化值
- 给属性设置默认的值

以下示例涵盖两种情况：

```swift
class Circle {
    
    var radius: Double
    var identifier: Int = 0
    
    init(radius: Double) {
        self.radius = radius
    }
    
}

var circle = Circle(radius: 5)
```

如果存储属性在对象实例化之后没有值，则代码无法通过编译。

## 懒加载属性

如果具有默认值的存储属性使用了关键字 `lazy` 标记，则其默认值不会立即初始化，而是在第一次访问该属性的时候。

因此如果该属性永远不会被访问，它将永远不会被初始化。你可以在初始化对 CPU 或内存消耗很大的时候使用该特性。

```swift
class TestClass {
    
    lazy var testString: String = "TestString"
    
}
 
let testClass = TestClass()
print(testClass.testString) //TestString
```

该属性在被访问之前不会进行初始化。在这个例子中并不容易看出来。但由于初始化也可以在 block 里面实现，我们可以使它更明显一些：

```swift
class TestClass {
    
    lazy var testString: String = {
        print("about to initialize the property")
        return "TestString"
    }()
    
}

let testClass = TestClass()
print("before first call")
print(testClass.testString)
print(testClass.testString)
```

这个例子的输出：

```shell
before first call
about to initialize the property
TestString
TestString
```

这意味着该 block 仅被调用一次 - 第一次访问该属性的时候。由于存储属性是可变的，因此可以更改初始值。

## 类型属性

类型属性是类的的一部分，但不是实例的一部分，也称为静态属性。存储属性和计算属性都可以使类型属性。为此你需要使用关键字 `static`：

```swift
class TestClass {
    
    static var testString: String = "TestString"
    
}
 
print("\(TestClass.testString)") //TestString
```

如你所见，它们使用类名而不是实例对象来访问它们。此外类型属性始终需要默认值，因为没有初始化方法。

## 具有私有 Setter 的公共属性

正如我在[本文](http://www.thomashanning.com/public-properties-with-private-setters/)中更详细说明的那样，这是一种常见的情况，你不想提供一个公共的 getter，而是提供一个私有的 setter。这是封装的基本原则。通过这样只有类本身可以操作该属性，但仍可从类外部访问读取。

来看看下面的例子：

```swift
public class Circle {
    
    public private(set) var area: Double = 0
    public private(set) var diameter: Double = 0
    
    public var radius: Double {
        didSet {
            calculateFigures()
        }
    }
    
    public init(radius:Double) {
        self.radius = radius
        calculateFigures()
    }
    
    private func calculateFigures() {
        area = Double.pi * radius * radius
        diameter = 2 * Double.pi * radius
    }
}

let circle = Circle(radius: 5)

print("area: \(circle.area)") //area: 78.5398163397448
print("diameter: \(circle.diameter)") //diameter: 31.4159265358979

circle.area = 10 //Compiler Error: cannot assign to property: 'area' setter is inaccessible
```

这里的属性 `area` 和 `diameter` 可以从类的外部访问，但只能在类内部赋值。为此你必须使用 `public private(set)` 的组合。根据我的经验，这个特性在 iOS 开发中很少使用，但它对代码编写非常有用，能够避免更多 bugs 的情况。
