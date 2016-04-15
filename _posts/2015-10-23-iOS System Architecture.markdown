---
layout: post
title: iOS System Architecture
date: 2015-10-12 15:32:24.000000000 +09:00
---
# 概述
iOS系统分为可分为四级结构，由上至下分别为可触摸层（Cocoa Touch Layer）、媒体层（Media Layer）、核心服务层（Core Services Layer）、核心系统层（Core OS Layer），每个层级提供不同的服务。低层级结构提供基础服务如文件系统、内存管理、I/O操作等。高层级结构建立在低层级结构之上提供具体服务如UI控件、文件访问等。

![4级结构](http://example.com/example.png)


