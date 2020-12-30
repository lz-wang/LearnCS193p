# Lecture 5 ViewBuilder + Shape + ViewModifier

## 主要内容

- @ViewBuilder
- Shape
- ViewModifier

## @ViewBuilder

>  在Swift语言中支持“有向列表语法（list-oriented syntax）”，而@ViewBuilder是一种简单而便捷的，支持多个列表视图的机制。
>
> Based on a general technology added to Swift to support “list-oriented syntax”. It’s a simple mechanism for supporting a more convenient syntax for lists of Views.

**任何函数或者只读的计算变量（computed var）可以被@ViewBuilder标记。当标记之后，函数或者变量中的值将被解释为一组列表视图（a list of Views）。**比如：

```swift
@ViewBuilder
func front(of card: Card) -> some View {
    RoundedRectangle(cornerRadius: 10) 
    RoundedRectangle(cornerRadius: 10).stroke() 
    Text(card.content)
}
```

以上将返回为元祖视图（TupleView）：`TupleView<RoundedRectangle,RoundedRectangle,Text>`

**也可以使用@ViewBuilder来标记返回视图的参数。**比如：

```swift
struct GeometryReader<Content> where Content: View {
    init(@ViewBuilder content: @escaping (GeometryProxy) -> Content) { . . . } 
}
// The content parameter is just a function that returns a View.
// Now all users of GeometryReader get to use the list syntax to express the Views to be sized.
```

在接触到的SwiftUI组件中，`ZStack`， `HStack`，` VStack`，`ForEach`，`Group`都是在做上述相同的事情。

## Shape

>  默认情况下，Shape通过填满前景色进行绘制。但其实它也可以通过`.stroke()`和`fill()`等来进行改变。
>
> By default, Shapes draw themselves by filling with the current foreground color. But we’ve already seen that this can be changed with .stroke() and .fill(). They return a View that draws the Shape in the specified way (by stroking or filling).

Shape可以通过如下方式使用：

```swift
func fill<S>(_ whatToFillWith: S) -> View where S: ShapeStyle
```

首先，`S`的类型是“don't care“；其次，它可以为能实现`ShapeStyle`协议的东西，如`Color`，`ImagePaint`，`AngularGradient`，`LinearGradient`等。

创建自己的Shape可以参考如下代码：

```swift
func path(in rect: CGRect) -> Path {
	return a Path 
}
// In here you will create and return a Path that draws anything you want. 
// Path has a ton of functions to support drawing (check out its documentation).
```

## ViewModifier

所有对视图进行修改的函数都可以看作为ViewModifier。比如，修改比例的函数`.modifier(AspectModifier(2/3))`中，`AspectModifier`可以为任意的遵从ViewModifier协议的东西。

```swift
protocol ViewModifier {
    associatedtype Content // this is a protocol’s version of a“don’t care” 
    func body(content: Content) -> some View {
    	return some View that represents a modification of content 
    }
}
```

在下面的示例中，可以观察到ViewModifier是如何实现其自身作用的：

```swift
Text(“!”).modifier(Cardify(isFaceUp: true)) // eventually .cardify(isFaceUp: true)
struct Cardify: ViewModifier {
    var isFaceUp: Bool
    func body(content: Content) -> some View { 
        ZStack {
            if isFaceUp {
                RoundedRectangle(cornerRadius: 10).fill(Color.white)
                RoundedRectangle(cornerRadius: 10).stroke()
                content
            } else {
                RoundedRectangle(cornerRadius: 10)
            }
        }
    }
}
```

![image-20200727164137622](https://tva1.sinaimg.cn/large/007S8ZIlly1gh5mf9yd24j31ai0oiqv5.jpg)