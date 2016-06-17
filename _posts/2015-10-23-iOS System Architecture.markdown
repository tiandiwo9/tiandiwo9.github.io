---
layout: post
title: iOS System Architecture
date: 2015-10-12 15:32:24.000000000 +09:00
tags: [Cocoa Touch , Architecture , Frameworks]
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

#### AirPlay技术
AirPlay让应用串流声音和视频内容到Apple TV或者串流声音内容到第三方扬声器和接收器。

AirPlay内建于许多框架，包括UIKit、Media Player、AVFoundation、Core Audio。因此在大多数情况你不需要为了支持它做任何事。在使用那些框架时，当播放内容时自动获得AirPlay支持。当用户选择使用AirPlay播放内容时系统自动进行路由。

### Media Layer框架
###### [Assets Library Framework](https://github.com/coderyi/blog/blob/master/articles/2014/1016_AssetsLibrary.md)
提供访问的照片应用程序在用户的设备上管理的照片和视频

###### [AV Foundation Framework](https://developer.apple.com/library/ios/documentation/AudioVideo/Conceptual/AVFoundationPG/Articles/00_Introduction.html#//apple_ref/doc/uid/TP40010188)
提供了一组Objective-C类的用于播放，录制和管理音频和视频内容
- 音频会话管理，包括声明你的应用程序的音频功能的系统支持
- 您的应用程序的媒体资产管理
- 编辑媒体内容支持
- 捕捉音频和视频的能力
- 回放音频和视频的能力
- 跟踪管理
- 元数据管理的媒体项目
- 立体声声像
- 声音之间的精确同步
- 一个Objective-C接口，用于确定有关的声音文件，如数据格式，采样率和通道数的详细信息
- 通过AirPlay的流媒体内容的支持

###### AVKit Framework
AVPlayerViewController

###### [Core Audio](https://developer.apple.com/library/ios/documentation/MusicAudio/Conceptual/CoreAudioOverview/Introduction/Introduction.html#//apple_ref/doc/uid/TP40003577)

框架 | 功能
---|---
[CoreAudio.framework](http://www.jianshu.com/p/40e6c9a16add) | 定义整个核心音频使用的音频数据类型
[AudioToolbox.framework](http://www.cnblogs.com/sunminmin/p/4475710.html) | 提供对音频文件和流的播放和录制服务。该框架还提供了用于管理音频文件，播放系统警告声音，和触发某些设备的震动功能的支持
[AudioUnit.framework](http://www.jianshu.com/p/40e6c9a16add) | 提供了使用内置的音频单元，它们是音频处理模块的服务。
[CoreMIDI.framework](https://github.com/petegoodliffe/PGMidi) | 提供了一种标准的方式与MIDI设备，包括硬件键盘和合成器进行通信。
MediaToolbox.framework | 提供对音频接口。

###### CoreAudioKit Framework
Core MIDI 和 CoreAudioKit 可以被用来使应用程序表现为 MIDI 设备

###### [Core Graphics Framework](http://southpeak.github.io/blog/2014/11/10/quartz-2dbian-cheng-zhi-nan-zhi-%5B%3F%5D-:gai-lan/)
基于矢量的绘图在OS X中使用的引擎支持基于路径绘图，抗锯齿渲染，渐变，图像，颜色，坐标空间变换和PDF文档的创建，显示和分析。虽然API是基于C，它使用基于对象的抽象以表示基本绘图对象，便于存储和重用你的图形内容

###### [Core Image Framework](https://www.pupboss.com/core-image/)
提供一组强大的内建过滤器来操作视频和静态图像。
你能在触摸弹起、纠正图片以及面部和特征检测等许多方面使用这些内建的过滤器。这些过滤器的先进特点是它们操作在非破坏方式，即原先的图像不被改变。

###### [Core Text Framework](http://geeklu.com/2013/03/core-text/)
提供一个对文本进行布局和字体处理的简单的、高性能的C-based接口。
该框架用在不使用TextKit但仍想获得在字处理应用中发现的先进文本处理能力。
该框架提供了一个智能的文本布局引擎，包括在其它内容周围环绕文本的能力，它也支持使用多种字体和呈现属性的先进的文本风格。

###### Core Text Framework
为Core Media框架提供缓冲和缓冲池支持。多数应用从不直接使用该框架。

###### [Game Controller Framework](https://developer.apple.com/library/ios/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276)
在应用中发现和配置针对iPhone/iPod/iPad设备的游戏控制器。游戏控制器可以是物理连接到iOS设备或者是通过蓝牙无线连接。

###### [GLKit Framework](http://www.raywenderlich.com/5223/beginning-opengl-es-2-0-with-glkit-part-1)
该GLKit框架（GLKit.framework）包含了一组Objective-C的基础工具类，简化了创建一个OpenGL ES的应用程序所需的工作。GLKit支持应用程序开发的四个关键领域：

- 该GLKView和GLKViewController类提供了一个标准实现启用OpenGL ES视图，以及相关的渲染循环。视图管理代表应用程序的底层帧缓冲区对象。
- 该GLKTextureLoader类提供图像转换和加载程序到您的应用程序，允许它的纹理图像自动加载到您的上下文。它可以同步或异步加载纹理。当加载纹理不同步，您的应用程序提供了当纹理加载到你的上下文被称为完成处理程序块。
- 所述GLKit框架提供向量，矩阵，和四元的实现，以及为提供在OpenGL ES 1.1中找到的相同的功能的矩阵堆栈操作。
- 该GLKBaseEffect，GLKSkyboxEffect和GLKReflectionMapEffect类提供现有的，可配置的图形着色器实现常用的图形操作。特别是，GLKBaseEffect类实现中发现灯光和材料模型的OpenGL ES 1.1规范，简化了从的OpenGL ES 1.1迁移应用更高版本的OpenGL ES所需的工作量。

###### [Image I/O Framework](https://developer.apple.com/library/mac/documentation/GraphicsImaging/Conceptual/ImageIOGuide/imageio_basics/ikpg_basics.html#//apple_ref/doc/uid/TP40005462-CH216-TPXREF101)
ImageI/O 框架(ImageIO.framework)提供输入和输出图像数据和图像元数据的接口。
该框架利用CoreGraphics数据类型和功能，并支持在ios 上所有的可获得的标准的图像类型。你能使用这个框架存取Exif和IPTC元数据属性。

###### Media Accessibility Framework
管理媒体文件的字幕内容

###### [Media Player Framework](http://blog.csdn.net/weisubao/article/details/42126083)
提供应用中播放声音和视频的高级别支持。

###### [Metal Framework](http://objccn.io/issue-18-2/)
[图形渲染流水线](http://www.cocoachina.com/game/20141013/9890.html)  
Metal是与OpenGL ES是并列的，它们都是应用对GPU访问的底层接口。而Metal则提供了更底层，更面向硬件的接口.

###### OpenAL Framework
开放音频库（OpenAL）接口是在应用程序发布方位音频的跨平台标准。

###### [OpenGL ES Framework](http://www.cocoachina.com/cms/tags.php?/OpenGL+ES/)
OpenGLES 框架 (OpenGLES.framework)提供绘制2d和3d内容的工具， 它是一个C-based的框架。该框架以最接近设备硬件的方式为全屏沉浸式应用例如游戏提供细粒度的图形控制和高的帧率。

###### [Photos Framework](http://kayosite.com/ios-development-and-detail-of-photo-framework.html)
PhotoKit 是一套比 AssetsLibrary 更完整也更高效的库，对资源的处理跟 AssetsLibrary 也有很大的不同。

###### Photos UI Framework
可以在照片应用中编辑图片和视频资源。

###### [Quartz Core Framework](http://blog.zzist.cn/index.php/archives/CoreAnimation1.html)
QuartzCore 框架(QuartzCore.framework)包含Core Animation接口。Core Animation是一个先进的复合技术，使用它能容易创建快和有效的view-based的动画。

###### [SceneKit Framework](http://objccn.io/issue-18-3/)
3D 渲染框架。修改器、节点约束、骨骼动画。支持了粒子效果、物理引擎、脚本事件以及多通道分层渲染等多种技术。
[demo](http://www.cocoachina.com/ios/20141113/10205.html)
 
###### [SpriteKit Framework](https://onevcat.com/2013/06/sprite-kit-start/)
为2d和2.5d游戏提供硬件加速的动画系统。SpriteKit提供大多数游戏需要的基础，包括一个图形引擎和动画系统，声音播放支持，一个物理仿真引擎。
在Sprite Kit应用中内容组织为场景。一个场景包括纹理对象，视频，路径图形，核心图像过滤器和其它的特效。
[SpriteKit和cocos2d对比](http://blog.sina.com.cn/s/blog_4b55f6860101iy87.html)

## 四、核心服务层（Core Services Layer）
### 高级特性

###### [Peer-to-Peer Services](http://www.cocoachina.com/industry/20140408/8118.html)
这个Multipeer Connectivity框架提供通过蓝牙进行p2p连接的能力。你能使用p2p连接来启动与附近设备的通讯会话。

###### [iCloud Storage](http://www.devtf.cn/?p=574#)
iCloud存储让应用把用户文档和数据写到一个中心位置，用户然后能从他们的计算机和ios 设备存取这些数据。
- iCloud的文档存储，使用此功能可以存储用户文档和数据用户的iCloud帐户。
- iCloud的键值数据存储。使用此功能，以您的应用程序实例之间共享少量数据。
- CloudKit存储。当您要创建公开共享的内容，或当你要管理自己的数据的传输使用此功能。

###### [Block Objects](http://www.jianshu.com/p/29d70274374b)
blockobject本质上是一个异步匿名函数。Blocks尤其用作回调或放在你需要一种容易的组合执行代码和相关数据方式的地方。
在ios，通常在下面的场景使用Blocks：
- 作为代理或代理方法的代替；
- 作为回调功能的代替；
- 为某个一次性操作实现其完成处理函数；
- 在一个集合中的所有项上执行一个任务；
- 与提交队列一起执行异步任务。

###### [Data Protection](http://blog.csdn.net/lifengzhong/article/details/7739477)
DataProtection允许应用利用设备上已有的内建的加密方法来使用用户的敏感数据。
当应用指定一个特定的文件被保护时，系统在磁盘上以加密格式存储该文件。

###### File-Sharing Support
添加UIFileSharingEnabled在应用程序的Info.plist的文件，并设置键的值是。

###### [Grand Central Dispatch](http://www.dreamingwish.com/article/gcdgrand-central-dispatch-jiao-cheng.html)
Grand Central Dispatch简称（GCD）是苹果公司开发的技术，以优化的应用程序支持多核心处理器和其他的对称多处理系统的系统。这建立在任务并行执行的线程池模式的基础上的。

###### [In-App Purchase](http://blog.devtang.com/2012/12/09/in-app-purchase-check-list/)
In-App Purchase 提供在应用中销售应用特定的内容和服务以及来自iTunes的内容的能力。这个功能使用StoreKit框架实现，并提供使用用户的iTunes账号来处理金融方面的事务需要的基础。

###### [SQLite](http://ios.jobbole.com/83542/)
SQLite库让你在你的应用中嵌入一个轻量级的sql数据库，你能创建本地数据库文件，管理数据库表和表中的数据记录。

###### [XML Support](https://github.com/robbiehanson/KissXML)
Foundation框架提供一个NSXMLParser类用来从一个xml文档中引出元素。操作xml内容的额外的支持由libxml2库提供支持.

### Core Services框架
###### [Accounts Framework](http://www.peterfriese.de/the-accounts-and-twitter-framework-on-ios-5/)
为确定的用户账号提供单点登录模式。单点登录通过消除用户分离的多个账号需要的多次登录提示，来增强用户体验。

###### [Address Book Framework](http://blog.csdn.net/runintolove/article/details/51387594)
提供可编程存取用户的联系人数据库的方式。存取用户的联系人数据需要用户的明确的许可。应用因此必须准备好用户拒绝存取的情形。应用也鼓励提供Info.plist键来描述需要存取的原因。

###### [Ad Support Framework](http://www.cnblogs.com/BigPolarBear/p/3359526.html)
提供可编程存取用户的联系人数据库的方式。存取用户的联系人数据需要用户的明确的许可。应用因此必须准备好用户拒绝存取的情形。应用也鼓励提供Info.plist键来描述需要存取的原因。  
注意：由于idfa会出现取不到的情况，故绝不可以作为业务分析的主id，来识别用户。 
注意：如果用户将属于此Vender的所有App卸载，则idfv的值会被重置，即再重装此Vender的App，idfv的值和之前不同。

###### [CFNetwork Framework](http://www.tqcto.com/article/web/37146.html)
高性能的使用面向对象对网络协议进行抽象的一组C-based接口。这些抽象提供对协议栈细节的控制，使它容易使用低级别的构造例如BSDsockets。

- 使用BSD sockets。
- 使用SSL或TLS创建安全连接。
- 解析DNS主机
- 与HTTP服务器、认证HTTP服务器、HTTPS服务器交互。
- 与FTP服务器交互。
- 发布、解决和浏览Bonjour服务。

CFNetwork物理和理论上基于BSD sockets。

###### [CloudKit Framework](http://www.devtf.cn/?p=574#)
应用程序和iCloud中之间移动数据的管道，也可以把数据存储在云端，既可以完全把数据存储在云端，也可以本地和远程都存储。

###### [Core Data Framework](http://allluckly.cn/%E6%8C%81%E4%B9%85%E5%8C%96/chijiuhua01)
CoreData框架打算在数据模式是高结构化的应用中使用，在xcode中能够使用图形工具来建立一个表现你的数据模式的模型。在运行时，你的数据模式实体的实例通过CoreData框架被创建、管理和获得。
通过为你的应用管理其数据模式，CoreData大大减少了必须书写的代码量。CoreData也提供如下功能：

- 为优化性能在SQLite数据库中存储对象数据；
- 一个管理数据表视图结果的NSFetchedResultsController类；
- 对基本的文本编辑之外的undo/redo的管理；
- 支持属性值的校验；
- 支持传播改变确保对象之间的关系保持一致性；
- 支持分组、过滤和在内存中优化数据。

###### [Core Foundation Framework](http://www.lanou3g.com/bbs/forum.php?mod=viewthread&tid=5268)
一组C-based接口，为ios应用提供基本的数据管理和服务功能。该框架包括如下支持：

- 收集的数据类型（数组，集合等）
- Bundles
- 字符串管理
- 日期和时间管理
- 原始数据块管理
- 偏好管理
- 网址和视频流处理
- 线程和循环运行
- 端口和套接字通信
CoreFoundation框架与Foundation框架紧密相关，为相同的基本功能提供Objective-C接口。当你需要混合使用Foundation对象和Core Foundation类型时，你能利用两个框架之间存在的“toll-freebridging”。toll-free bridging”意味着你能可交换地在两个框架的方法和功能中使用一些CoreFoundation和Foundation类型。这个支持对许多数据类型可用，包括集合和字符串数据类型。

###### [Core Location Framework](http://www.cnblogs.com/dongwenbo/p/4299160.html)
为应用提供位置信息。该框架使用GPS、蜂窝、或者Wi-Fi来定位用户的当前经度和纬度。

###### Core Media Framework
此框架提供AV Foundation框架使用的底层媒体类型。只有少数需要对音频或视频创建及展示进行精确控制的应用程序才会涉及该框架，其他大部分应用程序应该都用不上。

###### [Core Motion Framework](http://www.jianshu.com/p/1e933586f72b)
提供一组接口来存取设备上可获得的运动数据。
该框架支持使用一组新的block-based接口来存取原始和加工过的加速度计数据。对于带有陀螺仪的设备，你也能获得原始的陀螺仪数据和加工过的反应设备方向和旋转速度的数据。
你能在游戏或其它使用运动作为输入或作为增强用户体验的方式的应用中使用加速度计和陀螺仪两种数据。对于带有计步硬件的设备，你能存取它的数据来跟踪健康相关的运动。

###### [Core Telephony Framework](http://blog.csdn.net/jymn_chen/article/details/19240903)
提供与蜂窝电话的通话相关的信息交互的接口。

###### [EventKit Framework](http://www.xuebuyuan.com/1849897.html)
提供存取用户设备上的月历事件的接口。能够使用该框架来做如下事情：
 办 ｀                     
- 获取现有的从用户的日历事件和提醒
- 添加事件到用户的日历
- 为用户创建了提醒，并让它们出现在提醒应用程序
- 配置告警日历活动，包括当这些报警器应触发设置规则

```
重要提示：访问用户的日历和提醒数据需要来自用户的明确许可。
应用程序，因此必须为用户准备否认访问。
我们也鼓励企业应用套件提供的Info.plist描述为需要访问的原因键。
```
###### [Foundation Framework](http://www.cnblogs.com/kenshincui/p/3885689.html)
提供Core Foundation框架提供的许多功能的Objective-C封装。该框架提供如下功能的支持：

- 集合数据类型（数组、集合等等）；
- Bundles；
- 字符串管理；
- 日期和时间管理
- 原始数据块管理
- Preferences管理；
- URL和流操作；
- 线程和运行环；
- Bonjour；
- 通讯端口管理；
- 国际化；
- 规则表达式匹配；
- Cache支持。

###### [HealthKit Framework](http://swift.gg/2016/05/13/healthkit-introduction/)
应用可以使用它来分享健康和健身数据。HealthKit管理从不同来源获得的数据，并根据用户的偏好设置，自动将不同来源的所有数据合并起来。

###### [HomeKit Framework](http://www.cocoachina.com/ios/20150326/11411.html)
用于与通信并控制在用户的家连接的设备的新框架。被介绍为家庭新设备提供更多的连接和更好的用户体验。HomeKit提供标准化的方式与这些设备进行通信。

###### [JavaScript Core Framework](http://www.cnblogs.com/shaoting/p/5247208.html)
为许多标准的JavaScript对象提供Objective-C语言的封装。使用该框架来执行JavaScript代码和分析JSON数据。

###### [Mobile Core Services Framework](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/UTIRef/Introduction/Introduction.html)
定义在通用类型标识符（UTIs）中使用的低级别类型。

###### [Multipeer Connectivity Framework](http://www.cocoachina.com/industry/20140408/8118.html)
支持附近设备的发现，并与那些设备直接通讯（不需要Internet连接）。
使用该框架能够与附近设备通讯、容易的创建多人会话、支持可靠地传输顺序和实时数据。

###### [NewsstandKit Framework](http://www.jianshu.com/p/935d18c5b5ae)
通过Newsstand提供杂志和报纸内容的出版商能够使用NewsstandKit 框架(NewsstandKit.framework)创建它们自己的iOS应用，让用户启动新杂志和报纸新闻的后台下载。

###### [PassKit Framework](http://lijianfei.sinaapp.com/?p=344)
Passbook应用为用户提供了一个存储订货单、登机卡、入场券和商业折扣卡的位置。代替物理携带这些东西，用户现在能在IOS设备上存储它们。

###### [Quick Look Framework](https://segmentfault.com/a/1190000005010273)
可以使用该框架提供的视图控制器直接在用户界面中显示该文件的内容。

###### Safari Services Framework
提供以可编程的方式增加URLs到用户的Safari的书签的支持。

###### [Social Framework](http://swift.gg/2016/02/04/social-framework-introduction/)
提供一个简单的接口来存取用户的社交媒体账号。该框架取代Twitter框架并增加了其它社交账号，包括Facebook、Sina微博以及其它。

###### [StoreKit Framework](http://blog.devtang.com/2012/12/09/in-app-purchase-check-list/)
提供在ios应用中购买内容和服务的支持，也被称作应用内购买。

###### [System Configuration Framework](https://developer.apple.com/library/ios/samplecode/Reachability/Introduction/Intro.html#//apple_ref/doc/uid/DTS40007324)
能用它来确定设备的网络配置，也能使用该框架确定一个Wi-Fi或蜂窝连接是否在用以及一个特定的主机服务器是否能够存取。

###### [WebKit Framework](http://www.cocoachina.com/ios/20150203/11089.html)
除了显示HTML，可以提供基本的编辑支持，使用户可以替换文本和操作文档的文本和属性，包括CSS属性。

## 四、核心操作系统层（Core OS Layer）
CoreOS层包含其它大多数技术建在其之上的低级别的功能。虽然应用不直接使用这些技术，它们被其它框架使用。在需要显而易见的处理安全或与外设通讯的情形，你也能使用该层提供的框架。

### Core Services框架
###### [Accelerate Framework](https://github.com/mattt/Surge#readme)
包含执行数字信号处理、线性代数、图像处理计算的接口。
使用该框架的优点是它们针对所有的ios设备上存在的硬件配置做了优化，因此你能写一次代码确保在所有设备上有效运行。

###### [Core Bluetooth Framework](http://southpeak.github.io/blog/2014/07/29/core-bluetoothkuang-jia-zhi-%5B%3F%5D-:centralyu-peripheral/)
允许开发者与蓝牙低耗电外设（LE）交互。
- 扫描蓝牙外设，连接和断开发现的蓝牙外设；
- 声明应用的服务，转换ios 设备成其它蓝牙设备的外设；
- 从IOS设备广播iBeacon信息；
- 保存你的蓝牙连接的状态，当应用重新启动时恢复那些连接；
- 蓝牙外设可获得性变化时获得通知。

###### [External Accessory Framework](http://www.cnblogs.com/evangwt/archive/2013/04/04/2999661.html)
外部附件框架 提供与连接到基于iOS的设备硬件配件通信支持

###### [Generic Security Services Framework](http://www.ibm.com/developerworks/cn/aix/library/au-security_auth/)
给ios应用提供一组标准安全相关的服务。该框架的基本接口规定在IETFRFC2743andRFC4401。

###### [Local Authentication Framework](http://www.cocoachina.com/ios/20141114/10223.html)
本地认证框架（LocalAuthentication.framework）使您可以使用触摸ID来验证用户的身份。

###### [Local Authentication Framework](http://www.cocoachina.com/ios/20141114/10223.html)
本地认证框架（LocalAuthentication.framework）使您可以使用触摸ID来验证用户的身份。

###### [Network Extension Framework](http://blog.zorro.im/posts/iOS8-Network-Extension.html)
为配置和控制虚拟专用网络（VPN）的支持。使用此框架来创建VPN配置。

###### [Security Framework](http://www.code4app.com/ios/RSA-Encrypt-and-Decrypt/5061d6476803faf86c000001)
该框架提供管理证书、公有和私有key和信任策略的接口。支持产生加密安全伪随机码。它也支持在keychain（保存敏感用户数据的安全仓库）中保存证书和加密key。
在你创建的多个应用之间共享keychain是可能的。共享使它容易在相同的一套应用之间更平滑的协作。例如，你能使用该功能来共享用户口令或其它元素，否则可能使每个应用都需要提示用户。

###### [System](http://www.code4app.com/ios/RSA-Encrypt-and-Decrypt/5061d6476803faf86c000001)
IOS提供一组存取许多操作系统低级别功能的接口。应用通过LibSystem库存取这些功能。该C based的接口提供如下功能的支持：
- 多任务（POSIX线程和GCD)
- 网络（BSDsockets）
- 文件系统存取
- 标准I/O
- Bonjour和DNS服务
- 位置信息
- 内存分配
- 数学计算

###### [64-Bit Support](http://www.jianshu.com/p/3fce0bd6f045)
所有的系统库和框架是支持64位的，意味着它们能在32-bit和64-bit应用中使用。当以64-bit运行时编译时，应用可能运行的更快，因为在64-bit模式可以获得额外的处理器资源。
