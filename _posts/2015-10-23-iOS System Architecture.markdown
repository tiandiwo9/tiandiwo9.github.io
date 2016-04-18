---
layout: post
title: iOS System Architecture
date: 2015-10-12 15:32:24.000000000 +09:00
---
## 一、概述
iOS系统分为可分为四级结构，由上至下分别为可触摸层（Cocoa Touch Layer）、媒体层（Media Layer）、核心服务层（Core Services Layer）、核心系统层（Core OS Layer），每个层级提供不同的服务。低层级结构提供基础服务如文件系统、内存管理、I/O操作等。高层级结构建立在低层级结构之上提供具体服务如UI控件、文件访问等。

![4级结构](https://raw.githubusercontent.com/GarfieldLover/GarfieldLover.github.io/master/assets/postImages/797918-71efb73f5f3ab3c6.png)

## 二、可触摸层（Cocoa Touch Layer）
#### 高级特性
###### [App Extensions](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)
iOS 8支持以下领域，这被称为扩展应用信息：

- Share。分享内容与社交网站或其他实体。😖**没做过  补齐例子**
- Action。执行与当前内容一个简单的任务。😖
- Widget。提供了快速更新或启用通知中心的今日观点的简要任务。
- Photo editing。执行编辑照片应用程序中的照片或视频。
- Document provider。提供可以由其他应用程序访问的文件存储位置。使用文件选择器视图控制器应用程序可以打开文档提供商管理的文件或移动文件到文件提供者。😖
- Custom keyboard。提供自定义的键盘，用户可以在发生系统键盘的设备上的所有应用程序中进行选择。

###### [Handoff](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338)
Handoff是OS X和iOS上扩展的连续性跨设备的用户体验。切换使得用户可以在一个设备上开始一个活动，然后切换到另一台设备，并恢复在另一台设备上的同一个活动。例如，浏览Safari中长文用户移动到的签署到相同的Apple ID的iOS设备，并在Safari自动打开同一网页，使用相同的滚动位置与原始设备上。Handoff是iOS使这方面的体验尽可能无缝。  😖**没做过**

###### [Document Picker](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/DocumentPickerProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014451)
文档选择器功能允许用户选择文件应用程序沙箱以外。这些包括文档存储在iCloud驱动程序和第三方提供的文件的扩展名。用户可以直接打开这些文件,编辑它们。这个访问可以简化应用程序之间共享文档和支持更复杂的工作流。例如,用户可以很容易地编辑一个文档中使用多个应用程序。  😖**没做过**

###### [AirDrop](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIActivityViewController_Class/index.html#//apple_ref/doc/uid/TP40011976)
AirDrop允许用户分享照片、文件、url、与附近的设备和其他类型的数据。支持发送文件到其他使用空投iOS设备是建在现有UIActivityViewController类。这类显示不同的选项以分享您所指定的内容。  😖**没做过**

###### [TextKit](https://developer.apple.com/library/ios/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009542)
UIMenuController、 NSAttributedString

###### [UIKit Dynamics](https://developer.apple.com/library/ios/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009542)
弹性效果原来在[这里](https://onevcat.com/2013/06/uikit-dynamics-started/)！！

###### [Multitasking](https://developer.apple.com/library/ios/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009542)
iOS多任务一致在变化，7，8，9中均有大的变化，[7的变化](https://onevcat.com/2013/08/ios7-background-multitask/)

###### [Auto Layout](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/index.html#//apple_ref/doc/uid/TP40010853)
自动布局可以帮助您构建动态接口使用很少代码。使用自动布局,您如何将元素定义规则在你的用户界面。

###### [Storyboards](https://onevcat.com/2013/12/code-vs-xib-vs-storyboard/)
故事板在一个地方让你设计你的整个用户界面,这样您就可以看到你所有的视图和视图控制器和理解它们如何一起工作。
