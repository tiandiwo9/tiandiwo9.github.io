---
layout: post
title: iOS System Architecture
date: 2015-10-12 15:32:24.000000000 +09:00
---
## 一、概述
iOS系统分为可分为四级结构，由上至下分别为可触摸层（Cocoa Touch Layer）、媒体层（Media Layer）、核心服务层（Core Services Layer）、核心系统层（Core OS Layer），每个层级提供不同的服务。低层级结构提供基础服务如文件系统、内存管理、I/O操作等。高层级结构建立在低层级结构之上提供具体服务如UI控件、文件访问等。

![4级结构](https://raw.githubusercontent.com/GarfieldLover/GarfieldLover.github.io/master/assets/postImages/797918-71efb73f5f3ab3c6.png)

## 二、可触摸层（Cocoa Touch Layer）
### 高级特性
###### [App Extensions](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)
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
后台运行以便快速恢复，能更加省电。正常情况下 程序在后台是暂停的，但是有些情况允许程序继续运行在后台里。
（1）应用程序可以申请一个有限的时间去执行重要的任务
（2）后台运行特定服务
（3）本地通知
iOS多任务一致在变化，7，8，9中均有大的变化，[7的变化](https://onevcat.com/2013/08/ios7-background-multitask/)

###### [Auto Layout](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/index.html#//apple_ref/doc/uid/TP40010853)
自动布局可以帮助您构建动态接口使用很少代码。使用自动布局,您如何将元素定义规则在你的用户界面。

###### [Storyboards](https://onevcat.com/2013/12/code-vs-xib-vs-storyboard/)
故事板在一个地方让你设计你的整个用户界面,这样您就可以看到你所有的视图和视图控制器和理解它们如何一起工作。

###### [UI State Preservation](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007072)
UI状态保存


###### [Apple Push Notification Service](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html#//apple_ref/doc/uid/TP40008194)
推送通知

###### [Local Notifications](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html#//apple_ref/doc/uid/TP40008194)
本地通知

###### [Gesture Recognizers](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009541)
taps, pinches, pans, swipes, rotations

###### [Standard System View Controllers](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/index.html#//apple_ref/doc/uid/TP40007457)
- 显示和编辑联系人信息的 Address Book UI framework
- 创建和编辑日历事件的 Event Kit UI framework
- 处理邮件和短信的Message UI framework
- 打开或预览文件内容的UIDocumentInteractionController
- 拍摄和裁剪音视频的UIImagePickerController

###### [Gesture Recognizers](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009541)
taps, pinches, pans, swipes, rotations

### Cocoa Touch框架
###### [Address Book UI Framework](https://developer.apple.com/library/ios/ContactData/Conceptual/AddressBookProgrammingGuideforiPhone/Introduction.html#//apple_ref/doc/uid/TP40007744)
提供创建新联系人、编辑以及选择已存在联系人

###### [EventKit UI Framework]()
展示以及编辑日历相关的事件 标准系统控件

###### [iAd Framework](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iAd_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009881)
允许应用程序发布 横幅 广告。可以将广告放入标准视图中，视图本身基于苹果广告的服务自动管理加载、呈现以及响应点击。

###### [MapKit Framework](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/LocationAwarenessPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009497)
提供可以滑动的地图视图，可以使用地图提供方向或者显示自己感兴趣的点，同样可以添加标注（自定义图片或者内容），iOS4之后，mapview添加了拖拽注解以及自定义浮层（比如加乘车路线），iOS6之后，你可以创建寻路程序，当用户请求公交有关的方向，地图应用程序允许用户自己程序获取路线，除此之外，所有的应用都可以调用地图程序显示POI信息

###### [Message UI Framework]
MessageUI.framework为应用程序编写电子邮件或SMS消息的支持。组成支持包括您在您的应用程序呈现视图控制器的接口。可以填充这个视图控制器领域设置收件人，主题，正文内容，并且希望与消息包括任何附件。

###### [Notification Center Framework](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
通知中心框架（NotificationCenter.framework）为创建显示在通知中心信息小部件的支持。

###### [PushKit Framework]
该PushKit框架（PushKit.framework）提供对VoIP应用程序注册的支持。

###### [UIKit Framework](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIKit_Framework/index.html#//apple_ref/doc/uid/TP40006955)
 UIKit框架（UIKit.framework）包含Objective-C程序接口，提供实现图形，事件驱动的iPhone应用的关键架构。 iPhone OS中的每一个应用采用这个框架实现如下核心功能：
 
- 基本的应用程序管理和基础架构，包括应用程序的main run loop
- 用户界面管理，包括storyboards和nib的支持
- 视图控制器模型来封装用户界面的内容
- 代表标准系统视图和控件对象
- 处理触摸和运动为基础的事件支持
- 支持文档模型，其中包括iCloud的整合
- 图形和窗口支持，包括对外部显示器的支持
- 支持多任务
- 打印支持
- 定制标准的UIKit控件的外观支持
- 文本和网页内容的支持
- 剪切复制粘贴
- 对于动画用户界面内容的支持
- 集成通过URL方案和框架接口的系统上的其他应用
- 为残疾用户可访问性支持
- 支持苹果推送通知服务
- 本地通知调度和交付
- PDF创建
- 使用自定义输入视图的支持行为类似系统键盘
- 创建自定义文本视图的支持与系统交互的键盘
- 支持通过电子邮件，微博，Facebook的分享内容和其他服务

![uikit架构](https://raw.githubusercontent.com/GarfieldLover/GarfieldLover.github.io/master/assets/postImages/317394-d8f2627d537efe0e.jpg)


![uikit架](https://raw.githubusercontent.com/GarfieldLover/GarfieldLover.github.io/master/assets/postImages/20160330000207761.jpg)


## 三、媒体层（Media Layer）
### 高级特性
媒体层包含图形，音频和用来实现你的应用程序的多媒体体验视频技术。

#### 图形技术
技术 | 描述
---|---
UIKit | 定义绘制图像、贝塞尔路径、渲染图像和基于文本的内容、视图动画
Core Graphics | 支持基于路径（Path-based）画图、抗混淆（Anti-aliased）重载、梯度 （Gradients）、图像（Images）、颜色（Colors）、坐标空间转换（Coordinate-space Transformations）、pdf文档创建、显示和解析。虽然API是基于C语言的，它采用基于对象的抽象表征基础画图对象，使得图像内容易于保存和复用。
Core Animation | 一种高级动画和合成技术，它用优化的重载路径（Rendering Path）实现复杂的动画和虚拟效果。集成到iPhone OS 的许多部分，包括UIKit类如UIView，提供许多标准系统行为的动画。开发者也能利用这个框架中的Objective-C接口创建客户化的动画。
Core Image |提供了操纵视频和静止图像的高级支持
OpenGL ES和GLKit |OpenGL ES的处理使用硬件加速接口，先进的2D和3D渲染。GLKit是一套Objective-C类可提供的OpenGL ES的使用面向对象的接口。
Metal | 使复杂的图形渲染和计算任务令人难以置信的高性能
TextKit  Core Text | TextKit是用于进行精细的排版和文字管理UIKit类家族。Core Text是用于处理先进的印刷效果的基于C语言的框架
Image I/O | 提供接口读取和写入大多数图像格式
Photos Library |提供访问用户的照片，视频和媒体

#### 音频技术
iOS的音频技术与底层硬件工作，包括播放和录制高品质的音频，处理MIDI内容，并与设备的内置声音工作能力.

技术 | 描述
---|---
Media Player framework |当你想快速的音视频集成到应用程序中使用这个框架，当你不需要控制播放的行为
AV Foundation | 管理音频和视频的录制和播放一个Objective-C接口。使用此框架录制音频，当你需要在音频播放过程细粒度控制
OpenAL | Open Audio Library 开发者能应用OpenAL在需要位置音频输出的游戏或其他应用中实现高性能、高质量的音频。
Core Audio | 一个基于C语言的接口，并支持立体声（Stereo Audio）。能 产生、录制、混合和播放音频。开发者也能通过核心音频访问手机设备的振动功能。

iOS支持[多种行业标准](https://zh.wikipedia.org/wiki/%E9%9F%B3%E9%A2%91%E6%96%87%E4%BB%B6%E6%A0%BC%E5%BC%8F)和特定苹果音频格式，包括以下内容：

- AAC
- Apple Lossless (ALAC)
- A-law
- IMA/ADPCM (IMA4)
- Linear PCM
- µ-law
- DVI/Intel IMA ADPCM
- Microsoft GSM 6.10
- AES3-2003

#### 视频技术
iOS的视频技术为您的应用程序管理的静态视频内容或播放流从互联网内容的支持.

技术 | 描述
---|---
UIImagePickerController | 用户选择媒体文件的UIKit的视图控制器
AVKit | 该AVKit框架提供了视频展示了一套简单易用的接口。该框架支持全屏幕和部分屏幕的视频播放，并支持可选的播放控制的用户
AV Foundation | 提供先进的视频播放和录制功能,细粒度控制,直播视频
Core Media | 用于操纵媒体底层数据类型和接口

iOS支持多种行业标准的[视频格式](https://www.zhihu.com/question/20997688)和[压缩标准](http://www.360doc.com/content/11/0922/11/496343_150310986.shtml)，包括以下内容：

- H.264视频，高达1.5 Mbps的，640 x 480像素，每秒30帧，H.264的Baseline Profile的采用AAC-LC音频，高达160 Kbps的48千赫，立体声音效的低Complexity版本的.m4v，.MP4和.MOV文件格式
- H.264视频，高达768 Kbps的，320×240像素，每秒30帧，Baseline Profile支持高达1.3采用AAC-LC音频电平高达160 Kbps的48千赫，立体声音效的.m4v，.MP4和。 MOV文件格式
- MPEG-4视频，高达2.5 Mbps的，640 x 480像素，每秒30帧，Simple Profile支持AAC-LC音频，高达160 Kbps的48千赫，立体声音效的.m4v，.MP4和.MOV文件格式
- 众多音频格式，包括在列出的音频技术
