---
layout: post
title: CADisplayLink、NSTimer
date: 2017-2-2 15:32:24.000000000 +09:00
tags: [Objective-C]
---

## 使用CADisplayLink、NSTimer有什么注意点？
CADisplayLink、NSTimer会造成循环引用，可以使用YYWeakProxy或者为CADisplayLink、NSTimer添加block方法解决循环引用
## BAD_ACCESS在什么情况下出现？
访问了悬垂指针，比如对一个已经释放的对象执行了release、访问已经释放对象的成员变量或者发消息。 死循环

