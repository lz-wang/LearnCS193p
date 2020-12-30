# Lecture 3 Reactive UI + Protocols + Layout

## 主要内容

- protocol
- enum
- 由以上概念生成游戏的响应式Demo
- 了解SwiftUI中的视觉布局模型 Layout

## Protocol 协议类型

### 将`protocol`看作一种精简的`struct`或者`class`：

拥有函数及变量，但却没有实现/存储，其声明方式与`struct`和`class`相似。

```swift
protocol Moveable {
      func move(by: Int)
      var hasMoved: Bool { get }
      var distanceFromStart: Int { get set }
}
```

而对于其他数据类型，可以声明使用上述的`Moveable`：

比如`struct`：`PortableThing`，以及`protocol`：`Vehicle`和`class`：`Car`

```swift
struct PortableThing: Moveable {
		// must implement move(by:), hasMoved and distanceFromStart here 
}

protocol Vehicle: Moveable {
		var passengerCount: Int { get set }
}

class Car: Vehicle {
		// must implement move(by:), hasMoved, distanceFromStart and passengerCount here 
}
```

对于`class`而言，也可以同时声明使用多个`protocol`：

```swift
class Car: Vehicle, Impoundable, Leasable {
		// must implement move(by:), hasMoved, distanceFromStart and passengerCount here 
  	// and must implement any funcs/vars in Impoundable and Leasable too
}
```

### 将`protocol`看作一种数据类型进行使用：

```swift
var m: Moveable
var car: Car = new Car(type: “Tesla”)
var portable: PortableThing = PortableThing()
m = car // perfectly legal
m = portable // perfectly legal
```

### 对`protocol`进行扩展（extension）

对`protocol`添加实现：

```swift
struct Tesla: Vehicle {
		// Tesla is constrained to have to implement everything in Vehicle
		// but gains all the capabilities a Vehicle has too 
}

// Adding protocol implementation
extension Vehicle {
		func registerWithDMV() { /* implementation here */ } 
}
```

直接对`protocol`自身的函数/变量进行扩展：

```swift
protocol Moveable {
    func move(by: Int)
    var hasMoved: Bool { get }
    var distanceFromStart: Int { get set }
}
extension Moveable {
		var hasMoved: Bool { return distanceFromStart > 0 }
}

struct ChessPiece: Moveable {
    // only need to implement move(by:) and distanceFromStart here
    // don’t have to implement hasMoved because there’s a default implementation out there 
		// would be allowed to implement hasMoved here if we wanted to, though
}
```

此外，通过类似的方式，也可以对`struct`或者`class`进行扩展：

```swift
struct Boat {
		...
}
extension Boat {
		func sailAroundTheWorld() { /* implementation */ }
}

// or, you can even make something conform to a protocol purely via an extension ...
extension Boat: Moveable {
		// implement move(by:) and distanceFromStart here 
}
// Now Boat conforms to the Moveable protocol!
```

### `protocol`的作用

1. 是一种令其他类型（`structs`/`classes`/其他`protocols`）的数据获得某种能力的方式；
2. 是一种令其他代码拥有超过其原数据类型行为的方式；
3. 是一种“格式化”数据结构的方式；
4. 是“函数式编程”的重要体现；
5. 与`generics`结合后可以实现更多的功能，如下：

```swift
protocol Greatness {
		func isGreaterThan(other: Self) -> Bool 
} // the type Self means “the actual type of the thing implementing this protocol”

// add an extension to Array like this ...
extension Array where Element: Greatness {
		var greatest: Element {
        // for-loop through all the Elements
        // which (inside this extension) we know each implements the Greatness protocol 
        // and figure out which one is greatest by calling isGreaterThan(other:) on them
				return the greatest by calling isGreaterThan on each Element 
    }
}
```

## Enum 枚举类型

- 可以看作一种有着“离散值”集合的数据类型；

- Swift的枚举类型更为强大的点在于，它拥有函数和计算变量，并且能够存储每个离散值的“关联数据”；

## Layout

#### How is the space on-screen apportioned to the Views?

1. Containers Views "offer" space to the Views inside them
2. Views then choose what size they want to be
3. Containers Views then position the Views inside of them

#### HStack an VStack

- 分割UI空间
- 对齐UI中的元素

#### Modifiers

修改元素的属性，典型函数如下：

- `.padding`：控制元素间距
- `.aspectRatio`：调整元素比例
- `.font`：调整字体
- `.foregroundColor`：调整前景色

```swift
HStack { // aside: the default alignment here is .center (not .top, for example) 		
		ForEach(viewModel.cards) { card in
				CardView(card: card).aspectRatio(2/3, contentMode: .fit) 
		}
}
    .foregroundColor(Color.orange) 
    .padding(10)
```

#### GeometryReader

控制元素的几何参数

```swift
var body: View {
 		GeometryReader { geometry in // not showing content: parameter label ...
		}
}

struct GeometryProxy {
    var size: CGSize
    func frame(in: CoordinateSpace) -> CGRect
    var safeAreaInsets: EdgeInsets
}
```

#### Safe Area

iPhone X及以后异形屏iOS设备的刘海部分屏幕显示区域。

## Xcode小技巧

按住⌘单击函数或变量等名称以快速修改相关内容：![image-20200720203741317](https://tva1.sinaimg.cn/large/007S8ZIlly1ggxpwlu74dj30zy0mi7ma.jpg)

