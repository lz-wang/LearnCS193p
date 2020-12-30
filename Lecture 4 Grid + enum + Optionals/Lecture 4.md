# Lecture 4 Grid + enum + Optionals

## 主要内容

- Grid
- `enum`：Swift中的枚举类型
- `Optional`：Swift中极为重要的一种枚举类型数据

## enum

`enum`作为枚举类型，可以容纳一组离散值/元素：

```swift
enum FastFoodMenuItem {
    case hamburger
    case fries
    case drink
    case cookie 
}
```

`enum`也可以对其中的每一个元素拥有其对应的“相关数据”，如：

```swift
enum FastFoodMenuItem {
    case hamburger(numberOfPatties: Int)
    case fries(size: FryOrderSize)
    case drink(String, ounces: Int) // the unnamed String is the brand, e.g. “Coke”
    case cookie 
}

// FryOrderSize would also probably be an enum
enum FryOrderSize {
    case large
    case small 
}
```

`enum`可以作为常量或者变量的属性使用，但如果枚举的元素拥有属性必须标明如下：

```swift
let menuItem: FastFoodMenuItem = FastFoodMenuItem.hamburger(patties: 2) 
var otherItem: FastFoodMenuItem = FastFoodMenuItem.cookie
```

对于`enum`类型的数据，在Swift中，使用`switch`来进行检查：

```swift
var menuItem = FastFoodMenuItem.hamburger(patties: 2) 
switch menuItem {
    case FastFoodMenuItem.hamburger: print(“burger”) 
    case FastFoodMenuItem.fries: print(“fries”) 
    case FastFoodMenuItem.drink: print(“drink”) 
    case FastFoodMenuItem.cookie: print(“cookie”)
}
```

在`enum`中，如果对于某一种情况/元素（`case`）不需要给出任何相应，可以使用`break`语法：

```swift
var menuItem = FastFoodMenuItem.hamburger(patties: 2) 
switch menuItem {
    case .hamburger: break // would print nothing on the console
    case .fries: print(“fries”) 
    case .drink: print(“drink”) 
    case .cookie: print(“cookie”)
}
```

在`enum`中，使用`default`来处理额外的情况：

```swift
var menuItem = FastFoodMenuItem.cookie 
switch menuItem {
    case .hamburger: break
    case .fries: print(“fries”) 
    default: print(“other”)
}
```

`enum`中的“相关数据”可以通过`switch`中的`let`语法进行访问：

```swift
var menuItem = FastFoodMenuItem.drink(“Coke”, ounces: 32) 
switch menuItem {
    case .hamburger(let pattyCount): print(“a burger with \(pattyCount) patties!”) 
    case .fries(let size): print(“a \(size) order of fries!”)
    case .drink(let brand, let ounces): print(“a \(ounces)oz \(brand)”)
    case .cookie: print(“a cookie!”)
 }
```

获取`enum`中的全部元素/情况，可以使用`allCases`如下：

```swift
enum TeslaModel: CaseIterable {
      case X
      case S
      case Three
      case Y
}

// Now this enum will have a static var allCases that you can iterate over.
for model in TeslaModel.allCases { 
    reportSalesNumbers(for: model)
}

func reportSalesNumbers(for model: TeslaModel) { 
    switch model { ... }
}
```

## Optional

`Optional`是一种枚举类型数据，形如：

```swift
enum Optional<T> { // a generic type, like Array<Element> or MemoryGame<CardContent> 
    case none
    case some(<T>) // the some case has associated value of type T 
}
```

`Optional`仅仅有两种值/状态：1⃣️ `is set` (some)，2⃣️ `not set`(none)。因而，它的使用场景经常为：

- 如果匹配项不在Array中时，用作`firstIndex(matching:)`的返回类型。
- 当示例游戏中游戏首次开始时，对当前为正面的卡片索引值而言。

`Optional`使用举例：

```swift
enum Optional<T> {
    case none
    case some(<T>)
}

// 下述语法意义相同
var hello: String?
var hello: Optional<String> = .none

var hello: String? = “hello” 
var hello: Optional<String> = .some(“hello”) 

var hello: String? = nil
var hello: Optional<String> = .none
```

`Optional`类型的数据的相关值可以通过如下方式进行访问：

```swift
// 直接访问数据
let hello: String? = ...
print(hello!)

switch hello {
    case .none: // raise an exception (crash)
    case .some(let data): print(data)
}

// 使用安全的表达方式 if let
if let safehello = hello {
    print(safehello)
} else {
    // do something else
}

switch hello {
    case .none: { 
        // do something else
    }
    case .some(let data): {
        print(data)
    }
}
```

`Optional`也可以与`??`表达方式等价替换：

```swift
enum Optional<T> {
    case none
    case some(<T>)
}

let x: String? = ... 
let y = x ?? “foo”

switch x {
    case .none: y = “foo”
    case .some(let data): y = data
}
```