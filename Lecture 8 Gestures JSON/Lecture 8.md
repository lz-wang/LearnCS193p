# Lecture 8 Gestures JSON

## 主要内容

- UserDefaults：轻量级的存储
- Gestures：从用户获取输入到APP

## Persistence

### iOS中存储数据的多种方式：

1. 使用文件系统
2. 使用SQL数据库
3. 使用iCloud
4. 使用第三方存储服务
5. 最简单且易用的一种是**UserDefaults**，但仅用于存储轻量级的数据

## UserDefaults

- UserDefaults智能存储那些称之为“属性列表（Property List）”的数据。
- 需要注意的是，Property List仅仅是一个概念，而非struct或者protocol，它可以是`String`, `Int`, `Bool`, 浮点数, `Date`, `Data`, `Array`或者`Dictionary`。
- 如果要存储其他类型的数据，必须要首先转换为以上类型的数据。 
- 在Swift语言中，使用**Codable**将结构体struct转换为Data对象

### 使用UserDefaults

#### 1. 创建UserDefaults实例

多数情况下，使用以下方式即可：

```swift
let defaults = UserDefaults.standard
```

#### 2. 存储数据

类似于字典dictionary的方法，在`object`是一个Property List的前提下，使用如下方式存储数据：

```swift
defaults.set(object, forKey: "SomeKey")
```

或者使用如下的简易写法：

```swift
defaults.setDouble(37.5, forKey: "MyDouble")
```

#### 3. 取回数据

使用如下方式：

```swift
let i: Int = defaults.integer(forKey: "MyInteger")
let b: Data = defaults.data(forKey: "MyData")
let u: URL = defaults.url(forKey: "MyURL")
let strings: [String] = defaults.stringArray(forKey: "MyString")
```

取回Array中除了String之外的数据则更为复杂：

```swift
let a = array(forKey: "MyArray")
```

将返回一个数组：`Array<any>`

## Gestures

> SwiftUI拥有强大的识别“手势”的能力，称作多点触控（multitouch）。我们需要做的就是处理这些手势，比如用户的拖拽、捏合以及点击等手势动作。

首先，为了让视图开始识别到特定的手势，应该使用如下的`.gesture`的view modifier：

```swift
myView.gesture(theGesture) // theGesture must implement the Gesture protocol
```

创建手势。在上面的例子中，通常`the Gesture`将会被一些函数或者计算值所创建，或者被一个在视图中的本地变量所创建，如：

```swift
var theGesture: some Gesture {
    return TapGesture(conut: 2)
}
```

在这个例子中，双击动作手势将被SwiftUI识别，但并不会做任何事情。

### 处理连离散手势（Discrete Gesture）

在上面的例子中，**`TapGesture`是一种离散手势，因为它发生在瞬间，并且当被识别后仅仅做一件事情**。同理，长按（LongPressGesture）也可以被视为一种离散型手势。

对于离散手势，我们使用`.onEnded{ }`进行处理：

```swift
var theGesture: some Gesture {
    return TapGesture(count: 2)
    	.onEnded { /* do something */ }
}
```

其简化的写法如下：

```swift
myView.onTapGesture(count: Int) { /* do something */ }
myView.onLongPressGesture(...) { /* do something */ }
```

### 处理非离散手势（Non-Discrete Gesture）

其他的手势多为非离散手势，**在这些非离散手势中，我们在手势发生的过程中（比如手指移动的过程中）对这些手势进行处理**。常见的例子包括：拖拽手势（DragGesture）、放大手势（MagnificationGesture）、旋转手势（RotationGesture）以及长按手势（LongPressGesture）等。

首先，对于非离散手势的处理，我们依然可以当手势结束时进行处理：

```swift
var theGesture: some Gesture {
    DragGesture(...)
    	.onEnded { value in /* do something */ }
}
```

需要注意的是，此处的`.onEnded`需要经过一个值`value`，这个值用来判断DragGesture的状态是否已经结束。

对于不同的手势而言，这个`value`是不同的；

- 对于DragGesture而言，`value`是一个struct，如手指的开始和结束位置
- 对于MagnificationGesture而言，`value`是放大的尺度，如手指分开的程度大小
- 对于RotationGesture而言，`value`是旋转的角度，如手指转的刻度大小

与离散手势不同的是，对于非离散手势而言，在非离散手势进行的过程中，每当手指移动过程中，我们都可以对某些状态进行更新。而这个状态在一个特殊标记的变量中被存储为如下格式：

```swift
@GestureState var myGestureState: MyGestureStateType = <starting value> // This can be a variable of any type you want.
```

在手势进行的过程中，我们可以存储任何我们需要的信息，如记录手指移动的距离等。

不过，在手势进行过程中，我们虽然有机会改变状态，但是这个机会仅以如下方式进行：

```swift
var theGesture: some Gesture {
    DragGesture(...)
    	.updating(%myGestureState) { value, myGestureState, transaction in
        	myGestureState = /* usually something related to value */
        }
    	.onEnded { value in /* do something */ }
}
```

此处的`.updating`将会在手指移动过程中，产生closure，进而改变状态。并且`myGestureState`语句产生的closure是极为重要的，因为这是唯一能改变`@GestureState`的地方。

当我们不需要追踪特定的`@GestureState`时，对于`.updating`相关的语句可以简写为`.onChanged`形式：

```swift
var theGesture: some Gesture {
    DragGesture(...)
    	.onChanged { value in
        	/* do something with value (which is the state of the fingers) */
        }
    	.onEnded { value in /* do something */ }
}
```

当手指的位置与你做的直接相关时，以上表述方式才有意义。比如你使用拖拽操作进行选择或者将手指当作笔使用的时候。

总之，对于离散型手势，当手指在移动的过程中：

1. 当手势处于`@GestureState`时，收集任何必要的信息来绘制视图
2. 将`.updating`添加到手势中
3. 在`.updating`中，使用`value`来更新`@GestureState`
4. 当手势结束时，`@GestureState`将会被复位重置
5. 手势结束后，视图必须要明确如何进行下一步的绘制，因而也经常使用`.onEnded`进行视图绘制