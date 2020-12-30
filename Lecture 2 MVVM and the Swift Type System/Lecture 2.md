# Lecture 2 MVVM and the Swift Type System

## 主要内容

- NVVM：即 Model-View-ViewModel，一种设计范例；
- 了解Swift中以下概念：
- 1. 结构体：struct
  2. 类：class
  3. 通用类型："Dont' Care" type （也称为 generics）
  4. 函数：functions

## NVVM

### 特点：

1. 是一种“code organizing”的结构设计范例；
2. 与响应式（"reactive"）UI协同工作；
3. 依赖于`SwiftUI`模块方能使用；
4. 与旧式iOS的UIKit中使用的MVC（Model View Controller）不同。

### 从Model到View的过程：

![image-20200723204819580](https://tva1.sinaimg.cn/large/007S8ZIlly1gh17mlv7fzj31ga0u0e82.jpg)

1. Model是不依赖于UI的，属于底层的数据与逻辑代码；
2. ViewModel充当View和Model的桥梁，当ViewModel注意到Model的变化后，起到类似于解释器的作用，发布"something changed"的事件，并将变化传输给View；
3. View收到ViewModel传来的变化后，自动根据发布的变化内容来拉取Model的数据流，并自动重建和刷新UI，反映了由Model变化带来的UI的变化，特点是"Stateless, Declared and Reactive"。

### 从View到Model的过程：

![image-20200723210208656](https://tva1.sinaimg.cn/large/007S8ZIlly1gh17me9mifj31ge0u0hdu.jpg)

1. View变化，调用“意图函数”（Intent function），告知ViewModel；
2. ViewModel处理“意图”，并修改Model

## 结构体`struct`与类`class`的异同

![image-20200723210634949](https://tva1.sinaimg.cn/large/007S8ZIlly1gh17lsl14nj31hw0u0npg.jpg)

## 通用类型 `Generics`

Swift是一种强类型的语言，不存在`untyped`类型的数据，当我们需要处理一些不在乎数据类型的数据流时，即可引入`generics`的数据类型。

典型的数据类型包括数组`Array`：

```swift
struct Array<Element> {
		...
		func append(_ element: Element) { . . . } 
}
```

## 将函数视为数据类型

函数可以接受0个或者多个输入数据，然后返回某种类型的数据，因而函数也可以视为一种特殊的数据类型，比如：

```swift
(Int,Int)->Bool //takestwoIntsandreturnsaBool
(Double) -> Void // takes a Double and returns nothing
() -> Array<String> // takes no arguments and returns an Array of Strings
() -> Void // takes no arguments and returns nothing (this is a common one)

var foo: (Double) -> Void // foo’s type: “function that takes a Double, returns nothing” 
func doSomething(what: () -> Bool) // what’s type: “function, takes nothing, returns Bool”
```

在SwiftUI中，经常进行函数式编程，所以“functions as types” 是Swift中一种重要的概念！





## Xcode小技巧

按住⌥ 键，左键单击函数或变量名查看相关文档

