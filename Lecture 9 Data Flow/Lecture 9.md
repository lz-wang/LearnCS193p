# Lecture 9 Data Flow

## 主要内容

- Property Wrappers：对于`@State`, `@Published`, `@ObservedObject`的具体解释
- Publisher：初步涉及此话题

## Property Wrapper

**简而言之，`@Something`的语句都是property wrappers，它的实质其实是一种结构体struct。**这些结构体将一些模版包装起来，并应用到对应的变量上。例如：

1. `@State`让变量存在于堆内存中
2. `@Published`让变量发布其变化
3. `@ObservedObject`的作用是当有变化发布被检测到时，让视图进行重绘

同时，这些property wrappers还添加了“语法糖（syntactic sugar）”来让这些结构体容易创建和使用

### Property Wrapper Syntactic Sugar

比如：

```swift
@Published var emojiArt: EmojiArt = EmojiArt()
```

其实是如下结构体：

```swift
struct Published {
	var wrappedValue: EmojiArt
	var projectedValue: Publisher<EmojiArt, Never> 
}
```

并且可以近似看作，Swift语言赋予我们将以下这些变量：

```swift
var _emojiArt: Published = Published(wrappedValue: EmojiArt()) 
var emojiArt: EmojiArt {
	get { _emojiArt.wrappedValue }
	set { _emojiArt.wrappedValue = newValue } 
}
```

需要注意的是，还有另外的变量需要使用`$`才能访问，如`$emojiArt`。这种类型位于Property Wrapper之上。

> the Wrapper struct does something on set/get of the wrappedValue.

### @Published

当Published的wrappedValue被set时，首先，它通过自己的projectedValue（即$emojiArt，一种Publisher）发布变化；接下来，利用`objectWillChange.send()`这样的API告知ObservableObject。

### @Binding

> The wrappedValue is: a value that is bound to something else.
>
>  What it does: gets/sets the value of the wrappedValue from some other source. 
>
> What it does: when the bound-to value changes, it invalidates the View.
>
>  Projected value (i.e. $): a Binding (self; i.e. the Binding itself)

@Binding的作用将@State或者@ObservedObject的变量与其他视图共享：

```swift
struct MyView: View {
    @State var myString = "Hello"
    var body: View {
        OtherView(sharedText: $myString)
    }
}

struct OtherView: View {
    @Binding var sharedText: String
    var body: View {
        Text(sharedText)
    }
}
```

在上例中，`OtherView`的主体（body）就是在`MyView`中的`myString`。

## Publisher

Publisher是一个发送值的对象，并且当发送失败时很可能是一个失败的对象。

```swift
Publisher<Output, Failure>
```

其中，`Output`是Publisher发布的事物的类型，而`Failure`则是当Publisher试图发布事物失败时，执行通讯的事物类型。













