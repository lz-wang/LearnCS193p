# Lecture 7 Multithreading EmojiArt

## 主要内容

- Colors and Images：调用色彩和图像的两类方式
- 1. Color vs. UIColor
  2. Image vs. UIImage
- Multithreaded Programming：利用多线程技术避免APP卡死
- EmojiArtDemo：新的SwiftUI实例，主要包含以下的内容
- 1. 对MVVM进行回顾
  2. 滑动视图：ScrollView
  3. 文件保险箱：学习使用iOS中的文件管理机制
  4. iOS中的拖拽操作：Drag and Drop
  5. 使用UIImage管理图像
  6. iOS中的多线程技术

## Color vs. UIColor

### Color

- 通过`Color`符号及对应代码来变更SwiftUI中的色彩
- 可以看作是一个色彩说明器，例如`.foregroundColor(Color.green)`
- 可以像是**ShapeStyle**，如：`.fill(Color.blue)`
- 可以像**View**，如：`Color.white`
- ⚠️正是由于以上的多功能性，导致在诸多的创造与对比场景中，此API的局限性较大

### UIColor

- 用于管理色彩
- 包含比**Color**多得多的内建色彩管理机制，包括系统相关色彩
- 可以在不同的色彩空间中进行转换

## Image vs. UIImage

### Image

- 主要作为**视图View**进行使用
- 可以通过`Image(_ name: String)`在Xcode的“Assets.xcassets”中访问图像
- 可以通过`Image(systemName:)`访问众多的**系统图像**，详见[SFSymbols](developer.apple.com/design)
- 可以通过`.iamgeScale()`这样的视图修改器来控制**系统图像**的大小，这些系统图像可以起到十分有用的标识作用

### UIImage

- 是创建和控制图像的一种类型，并且存储在变量中
- 对于图像而言，是一种极为强大的展现工具，可以通过`Image(uiImage:)`控制自己所需的图像属性

## Multithreading

### 为什么需要多线程？

- 在移动APP开发中，切记不能让APP的UI无响应
- 有时APP需要进行一些其他耗时的工作，如网络请求或者处理CPU密集型任务
- 通过创建一个新的线程来执行上述的非UI任务，而不是将这些任务放在UI之上

### Swift语言中的队列：Queues

> A queue is just a bunch of blocks of code, lined up, waiting for a thread to execute them.
>
> We specify the blocks of code waiting in a queue using closures (aka functions as arguments). 
>
> The core multithreading API is very simple: plop a closure onto a queue.

#### 主要队列：Main Queue

- 在所有的队列中最重要的队列称为主要队列
- 主要队列包含所有与**UI**相关的代码块
- 不论任何时候需要做与UI相关的事情时，一定会使用到主要队列
- 系统使用一个单独的进程来处理所有与主要队列相关的代码，所以它也经常用于同步

#### 后台队列：Background Queues

- 后台队列处理非UI性质的任务
- 系统会用众多的线程来执行这些后台队列的代码，因而这些任务也可以视为与UI主队列“并行”运行
- 可以自定义这些后台任务的优先级，但是主队列的优先级一定是高于后台队列

### GCD

处理队列任务的基本API称为**GCD**，即**Grand Central Dispatch**

GCD中有着众多不同的函数，但是仅处理两类基本任务：

1. 获取队列权限
2. 将代码块放入队列

### 两种创建队列的方式

**第一种：`DispatchQueue.main`：**UI相关代码必须执行的队列

**第二种：`DispatchQueue.global(qos: QoS) `**：包含服务质量的非UI型任务

其中qos是下述方式的一种：

1. `.userInteractive`：快做这个任务，UI依赖于这个
2. `.userInitiated`：用户要求做这任务个，现在做这个
3. `.utility`：这个任务需要发生，但用户并没有要求
4. `.background`：维护性任务，如清理缓存等

### 两种队列同步的方式

有两种在队列上添加阻断/同步（closure）的方式：

`let queue = DispatchQueue.main or DispatchQueue.global(qos:) `

**第一种：`queue.async { /* code to execute on queue */ }`**

在涉及UI的代码中，主要使用此类同步方式，它告诉后面的队列“稍等一下”

**第二种：`queue.sync { /* code to execute on queue */ }`**

将会阻断/屏蔽那些等待完成的队列，因而不适用于UI代码中，但在后台队列中可以适当使用

### Nesting

这个API的精妙之处在于结束nesting的时候，参看代码：

```swift
DispatchQueue(global: .userInitiated).async {
    // do something that might take a long time
    // this will not block the UI because it is happening off the main queue
    // once this long-time thing is done, it might require a change to the UI
    // but we can’t do UI here because this code is executing off the main queue
    // no problem, we just plop a closure with the UI code we want onto the main queue
    DispatchQueue.main.async {
    	// UI code can go here! we’re on the main queue! 
    }
}
```

上述API的机制使得异步的代码看起来像是同步发生的，所以对于耗时较长的任务需要谨慎使用此API。