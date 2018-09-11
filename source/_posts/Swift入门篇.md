---
title: Swift入门篇
date: 2016-05-16 14:27:08
tags: iOS
categories: 学习笔记
---

变量申明
===
变量用var，常量用let；可以显示指明变量数据类型，也可以隐式由变量赋值决定。如果是可以为nil的变量，需要用可选符号`?`修饰。
```swift
 let a: NSInteger = 12
    var b = 13
    b = 15
    print(a+b)
    //2***********?
    var mayNilStr: String? = "hello"
//    var str: String = "world"
    mayNilStr = nil
//    str = nil
    print("\(mayNilStr)")
    //3********** ??(如果变量为nil，则取？？之后的值)
    var yourName :String? = nil
    yourName = "Tom"
    let defaultName = "Jack"
    let greet = "hello \(yourName ?? defaultName)"
    print(greet)
```
<!-- more -->
数组和字典
===
数组和字典申明时可以显示指明元素、键值的数据类型，也可以隐式由包含的元素决定。
```swift
    //1.可变数组和字典
    var shopping = ["catfish","water","tulips","blue paint"]
    shopping[0] = "beer"
    var dic1 = ["name": "Jack",
                "age": 12,
                "school": "Harverd"]
    dic1["key1"] = 8888.88
    print("shopping arr \(shopping)")
    print("dic1 list is \(dic1)")

    //2.空数组和空字典
    var emptyArray = [String]()
    var emptyDictionary = [String:Float]()
    emptyArray.append("111")
    emptyArray.insert("222", atIndex: 1)
    emptyDictionary["salary"] = 112.0

    /*
    var shoppingList = []
    var dicList = [:]
    */
    print(emptyArray)
    print(emptyDictionary)
```

类型转换
===
不同数据类型的转换，可以通过函数或者转义符实现。
```swift
    //1*********  函数
    let label = "the width is "
    let width = 12
    let widthLabel = label + String(width)
    print(widthLabel)
    //2**********  \()
    let apples = 3
    let oranges = 5
    let appleSummary = "I have \(apples) apples.\n"
    let orangeSummary = "I have \(oranges) oranges\n"
    let summary = "I have \(apples+oranges) pieces of fruit."
    print(appleSummary)
    print(orangeSummary)
    print(summary)
```

条件控制和
===
for循环中，`..<`表示小于，`...`表示小于等于。switch语句不在需要break并且支持模糊匹配等。
```swift
 //1
    let scores = [12,35,86,98,13]
    for score in scores {
        if score < 60 {
            print("\(score) is C")
        }else if score < 80 {
            print("\(score) is B")
        }else {
            print("\(score) is A")
        }
    }

    //2
    var c = 1000;
    while (c > 0) {
        c -= 100;
    }
    print("final c value is \(c)")

    //3
    let vegetable = "red pepper"
    switch vegetable {
    case "celery" :
        print("Add some rausins and make ants on a log")
    case "cucumber", "watercress" :
        print("That would make a good tea sandwich")
    case let x where x.hasSuffix("pepper") :
        print("It is a spicy \(x)?")
    default :
        print("Everything tastes good in soup")
    }

    //4
    let numbers = [
        "num1":[1,2,3,5,6,4,9,8],
        "num2":[12,15,17,86,23,11,90],
        "num3":[876,123,234,124,242,634]
    ]
    var largestNum = 0
    for (_,kind) in numbers {
        for num in kind {
            if (num > largestNum) {
                largestNum = num;
            }
        }
    }
    print("the largest num is \(largestNum)");

    //5
    var n = 2
    repeat {
        n = n*2
    }while n < 100
    print("final n is \(n)")

    //6 ******  ..<小于   ...小于等于
    var loop1 = 0
    for i in 0..<4 {
        loop1 += i;
    }
    print("loop1 value is \(loop1)")

    var loop2 = 0
    for i in 0...4 {
        loop2 += i;
    }
    print("loop1 value is \(loop2)")
```
函数
===
函数参数动态确定。可以作为参数或者返回值。
```swift
func parameterFunction(name: String,age: Int16) -> String {
    return "your name is \(name), and age is \(age)"
}

func calculateStaticstics(scores: [Int]) -> (min: Int,max: Int,sum: Int) {
    var min = 0
    var max = 0
    var sum = 0
    for score in scores {
        sum += score
        if (score > max) {
            max = score
        }
        if (score < min) {
            min = score
        }
    }
    return (min,max,sum)
}

func testDynamicParameter(numbers: Int...) -> Int {
    var sum = 0
    for num in numbers {
        sum += num
    }
    if numbers.count > 0 {
        return sum/numbers.count
    }
    return 0
}

/**
 返回值为函数
 */
func functionCanbeReturnType() -> ((Int) -> Int) {
    func theReturnFunc(age: Int) -> Int {
        return age + 5
    }
    return theReturnFunc
}

/**
 参数为函数
 */
func functionCanbeParameter(lists:[Int],condition:((Int) -> Bool)) -> Bool {
    for item in lists {
        if (condition(item)) {
            return true
        }
    }
    return false
}
```
类
===
类中可以声明属性和方法等，可以创建构造函数和析构函数等
```swift
//类
class Shape {
    //property
    var numberOfSides = 0
    var name: String? = nil
    var age: Int
    let extralProperty = 1

    //构造函数
    init(name: String){
        self.name = name
        self.age = 12
    }

    init(age: Int) {
        self.age = age;
    }

    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }

    //实例销毁前的cleanup方法
    deinit {
        print("deinit method \(self.name)")
    }

    //fucntion
    func shapeDescription() ->String {
        return "the shape side num is \(numberOfSides),name is \(name)"
    }
}
```
继承
===
```swift
class Squar: Shape {
    var sideLength: Double = 0.0

    //property set and get method
    var perimeter: Double {
        get {
            return 4*sideLength
        }

        set {
            sideLength = sideLength/2
        }
    }

    //enum(支持嵌入方法)
    enum Rank: Int {
        case Ace = 1
        case Two,Three,Four,Five,Six,Seven,Eight
        case Jack,Tom

        func simpleDescription() -> String {
            switch self {
            case .Ace: return "ace"
            case .Jack: return "jack"
            case .Tom: return "tom"
            default: return String(self.rawValue)
            }
        }
    }

    //struct
    struct Card {
        var rank: Rank

        func simpleDescription() -> String {
            return "The \(rank.simpleDescription())"
        }
    }

    init(sideLength: Double, name: String) {
        self.sideLength = sideLength;
        super.init(name: name)
    }

    override func shapeDescription() -> String {
        return "override shape description"
    }
}
```
协议
===
```swift
//声明协议
protocol testProtocol {
    var simpleProtocolMethod1: String {
        get
    }
    mutating func adjust()
}

//实现协议
class complishProtocol: testProtocol {
    var simpleProtocolMethod1: String = "a very simple class"
    func adjust() {
        simpleProtocolMethod1 += " Now 100% adjus"
    }
}
```
