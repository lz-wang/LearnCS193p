# Lecture 6 Animation

## 主要内容

- Property Observers：“观测”一个变量并作出相应的变化；
- @State：位于视图内部，仅用于展示警告、编辑某种事物或者动画中；
- Animation：
- 1. 隐式与显式动画（Implicit vs. Explicit Animation）
  2. 动画视图（Animating Views）
  3. 过渡动画（Transitions）
  4. 动画形状（Animating Shapes）

## Property Observers

> Property observers本质上是一种通过观测变量是否变化从而执行相关代码的方式。
>
> Property observers are essentially a way to “watch” a var and execute code when it changes.

Property observers的语法类似于一个计算变量（computed var），但实质上与之没有任何关系：

```swift
var isFaceUp: Bool {
    willSet {
        if newValue {
            startUsingBonusTime()
        } else {
            stopUsingBonusTime()
        }
    }
}
```

在上述代码中，`newValue`就是一个将要被设置（set）的特殊变量，当触发此变量后，将执行`startUsingBonusTime()`函数，否则执行`stopUsingBonusTime()`函数。

## @State

> 在SwiftUI中，所有的视图结构都是只读的。不论用于维持视图的什么样的变量都可以被看成`let`常量，也就是说，只有常量以及计算变量是只读的才有意义。
>
> It turns out that all of your View structs are completely and utterly read-only! Yes, whatever variable SwiftUI is using to hold all your Views is a let! So, other than vars that are initialized on creation of your View, it’s useless to have a var. Only lets or computed vars that are read-only make any sense whatsoever.

这样做的好处在于：使得管理变化和高效绘制视图时变得可信且可证，并且对开发者而言为一种较好的限制。

> it makes it reliable and provable for it to manage changes and efficiently redraw things. And this is actually a very wonderful restriction for you.

视图应当是“无状态（stateless）”的（一直在绘制模型），因而一般不消耗存储空间或者内存。而实际上，视图需要状态时的时间是极为短暂的，在需要状态时，务必标记所有与之相关的变量如下：

```swift
@State private var somethingTemporary: SomeType // this can be of any type
// Marked private because no one else can access this anyway (except upon creating your View).
```

## Animation

### 两种使用动画的方式：

1. 隐式的，使用view modifier：`.animation(Animation)`
2. 显式的，通过使用`withAnimation(Animation) { }`类语法环绕在代码外侧

### 三类控制动画曲线的方法：

1. `.linear`：一类速度为线性不变的动画；
2. `.easeInOut`：一类速度为“慢----快----慢“的动画；
3. `spring`：一类在动画末尾提供一种“软着陆”的动画。

### 过渡动画`transtion`

从一种视图变换到另一种视图时的过渡动画，示例如下：

```swift
ZStack {
    if isFaceUp {
        RoundedRectangle()s // default .transition is .opacity 
        Text(“👻 ”).transition(.scale)
    } else {
        RoundedRectangle(cornerRadius: 10).transition(.identity)
    } 
}
```

所有的过渡动画API都可以视为`type erased`的，在ViewModifier中，使用`AnyTransition`结构来实现过渡：

- `AnyTransition.opacity`：透明度变换
- `AnyTransition.scale`：尺度变换
- `AnyTransition.offset(CGSize)`：移动视图
- `AnyTransition.modifier(active:identity:)`：两类ViewModifier的变换

**注意⚠️：过渡动画仅仅在CTAAOS（Containers that are already on-screen）视图下才能实现。**

`.onApperar()`以及`onDisappear()`函数提供了出现或者消失视图的方法。