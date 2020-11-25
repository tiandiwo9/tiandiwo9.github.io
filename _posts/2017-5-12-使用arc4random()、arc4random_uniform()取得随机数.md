---
layout: post
title: 使用arc4random()、arc4random_uniform()取得随机数
date: 2017-5-12 15:32:24.000000000 +09:00
tags: [Swift]
---

arc4random() 这个全局函数会生成10位数的随机整数（UInt32）。其生成的最大值是4294967295（2^32 - 1），最小值为0。

1，下面是使用 arc4random 函数求一个 1~100 的随机数（包括1和100）
1 | `let temp = Int(arc4random()%``100``)+``1`
2，下面是使用 arc4random_uniform 函数求一个 1~100 的随机数（包括1和100）

1 | `let temp = Int(arc4random_uniform(``100``))+``1`


