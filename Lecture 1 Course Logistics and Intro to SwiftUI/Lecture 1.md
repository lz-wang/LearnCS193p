# Lecture 1 Course Logistics and Introduction to SwiftUI

**Swift是一种强类型的编程语言。**

## 本节课主要讲了什么？

1. 课程开始的欢迎语
2. Xcode的下载与使用
3. 尝试开发卡片式的iOS应用
4. 代码编写过程中注意优雅一些

## Slide摘抄

> **What is this course about?**
>
> Developing applications for iOS (iPhone and iPad mostly, but applicable to Apple Watch/TV too). 
>
> Using SwiftUI (which shipped for the first time a few months ago in iOS 13!).
>
> (If you want to learn UIKit (Swift or Objective-C), see previous CS193p iterations on YouTube.)



> **What will I learn?**
>
> Some things will be familiar to you (OOP, C-like programming language, etc.)
>
> But a number of things will likely be very new to some of you ... 
>
> Swift Programming Language
>
> Functional Programming
>
> “Reactive” User-Interface Development Paradigm (including MVVM) 
>
> Object-Oriented Databases
>
> And this is a great place to experience “real life” applications of numerous CS concepts ... 
>
> CHI, API design, Language Design, Animation, Persistence, Networking, Multi-threading, etc.



> **Coronavirus**
>
> Occasionally, Stanford records CS193p to make it available free to the world.
>
> Coincidentally (and luckily), this is one of those quarters!
>
> So you will not miss a minute of exciting lecture action!
>
> (Though lectures will be weird for me with no one there.)
>
> Due to potentially huge time-zone differences of students, lectures will not be “live” on line. The vast majority of two-way interaction in this course has always been via Piazza anyway. That will continue to be true.
>
> We will certainly miss some things (no questions in lecture, no face-to-face office hours, etc.). But overall, I expect (hope?) that CS193p will be one of the less-impacted courses.
>
> In any case, we’ll adjust as needed on the fly!



> **How does this course work?**
>
> Demos (lots and lots)
>
> Lecture Slides (for concepts mostly)
>
> Reading Assignments (to learn Swift; first 3 weeks only)
>
> Programming Assignments (5 or 6 of them in the first 7 weeks)
>
> Final Project (last 3 weeks)



> **Let’s get started!**
>
> We’re going to start by building an application together over the first few weeks Starting right now ...

## 章节最终代码

```swift
//
//  ContentView.swift
//  Lecture1
//
//  Created by lzwang on 2020/7/18.
//  Copyright © 2020 lzwang. All rights reserved.
//

// Swift是一种强类型语言

import SwiftUI

struct ContentView: View {
    var body: some View {
        HStack {
            ForEach(0..<4) { index in
                CardView(isFaceUp: true)
            }
        }
            .padding()
            .foregroundColor(Color.orange)
            .font(Font.largeTitle)
    }
}

struct CardView: View{
    var isFaceUp: Bool
    
    var body: some View{
        ZStack {
            if isFaceUp {
                RoundedRectangle(cornerRadius: 10.0).fill(Color.white)
                RoundedRectangle(cornerRadius: 10.0).stroke(lineWidth:3)
                Text("👻")
            } else {
                RoundedRectangle(cornerRadius: 10.0).fill()
            }
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

## 效果图

![image-20200719154216878](https://tva1.sinaimg.cn/large/007S8ZIlly1ggwbqye2bqj31e40u0wz5.jpg)

