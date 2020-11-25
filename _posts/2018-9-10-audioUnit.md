---
layout: post
title: audioUnit
date: 2018-9-10 15:32:24.000000000 +09:00
tags: [Swift]
---

## 基本认识
在AudioUnitHostingFundamentals这个官方文档里有几个不错的图：

audioUnitScopes_2x.png
对于通用的audioUnit,可以有1-2条输入输出流，输入和输出不一定相等，比如mixer,可以两个音频输入，混音合成一个音频流输出。每个element表示一个音频处理上下文(context), 也称为bus。每个element有输出和输出部分，称为scope，分别是input scope和Output scope。Global scope确定只有一个element，就是element0，有些属性只能在Global scope上设置。

IO_unit_2x (1).png
对于remote_IO类型audioUnit，即从硬件采集和输出到硬件的audioUnit,它的逻辑是固定的：固定2个element,麦克风经过element1到APP，APP经element0到扬声器。

我们能把控的是中间的“APP内处理”部分，结合上图，淡黄色的部分就是APP可控的，Element1这个组件负责链接麦克风和APP，它的输入部分是系统控制，输出部分是APP控制；Element0负责连接APP和扬声器，输入部分APP控制，输出部分系统控制。

IOWithoutRenderCallback_2x (1).png
这个图展示了一个完整的录音+混音+播放的流程，在组件两边设置stream的格式，在代码里的概念是scope。

## 文件读取
demo在TFAudioUnitPlayer这个类,播放需要音频文件读取和输出的audioUnit。

文件读取使用ExtAudioFile，这个据我了解，有两点很重要：1.自带转码 2.只处理pcm。

不仅是ExtAudioFile，包括其他audioUnit,其实应该是流数据处理的性质，这些组件都是“输入+输出”的这种工作模式，这种模式决定了你要设置输出格式、输出格式等。

ExtAudioFileOpenURL使用文件地址构建一个ExtAudioFile
文件里的音频格式是保存在文件里的，不用设置，反而可以读取出来，比如得到采样率用作后续的处理。

设置输出格式

   AudioStreamBasicDescription clientDesc;
   clientDesc.mSampleRate = fileDesc.mSampleRate;
   clientDesc.mFormatID = kAudioFormatLinearPCM;
   clientDesc.mFormatFlags = kAudioFormatFlagIsSignedInteger | kAudioFormatFlagIsPacked;
   clientDesc.mReserved = 0;
   clientDesc.mChannelsPerFrame = 1; //2
   clientDesc.mBitsPerChannel = 16;
   clientDesc.mFramesPerPacket = 1;
   clientDesc.mBytesPerFrame = clientDesc.mChannelsPerFrame * clientDesc.mBitsPerChannel / 8;
   clientDesc.mBytesPerPacket = clientDesc.mBytesPerFrame;
pcm是没有编码、没有压缩的格式，更方便处理，所以输出这种格式。首先格式用AudioStreamBasicDescription这个结构体描述，这里包含了音频相关的知识：

采样率SampleRate: 每秒钟采样的次数

帧frame：每一次采样的数据对应一帧

声道数mChannelsPerFrame：人的两个耳朵对统一音源的感受不同带来距离定位，多声道也是为了立体感，每个声道有单独的采样数据，所以多一个声道就多一批的数据。

最后是每一次采样单个声道的数据格式：由mFormatFlags和mBitsPerChannel确定。mBitsPerChannel是数据大小，即采样位深，越大取值范围就更大，不容易数据溢出。mFormatFlags里包含是否有符号、整数或浮点数、大端或是小端等。有符号数就有正负之分，声音也是波，振动有正负之分。这里采用s16格式，即有符号的16比特整数格式。

从上至下是一个包含关系：每秒有SampleRate次采样，每次采样一个frame,每个frame有mChannelsPerFrame个样本，每个样本有mBitsPerChannel这么多数据。所以其他的数据大小都可以用以上这些来计算得到。当然前提是数据时没有编码压缩的

设置格式：

   size = sizeof(clientDesc);
   status = ExtAudioFileSetProperty(audioFile, kExtAudioFileProperty_ClientDataFormat, size, &clientDesc);
在APP这一端的是client,在文件那一端的是file，带client代表设置APP端的属性。测试mp3文件的读取，是可以改变采样率的，即mp3文件采样率是11025，可以直接读取输出44100的采样率数据。

读取数据
ExtAudioFileRead(audioFile, framesNum, bufferList)
framesNum输入时是想要读取的frame数，输出时是实际读取的个数，数据输出到bufferList里。bufferList里面的AudioBuffer的mData需要分配内存。
播放
播放使用AudioUnit,首先由3个相关的东西：AudioComponentDescription、AudioComponent和AudioComponentInstance。AudioUnit和AudioComponentInstance是一个东西，typedef定义的别名而已。

AudioComponentDescription是描述，用来做组件的筛选条件，类似于SQL语句where之后的东西。

AudioComponent是组件的抽象，就像类的概念，使用AudioComponentFindNext来寻找一个匹配条件的组件。

AudioComponentInstance是组件，就像对象的概念，使用AudioComponentInstanceNew构建。

构建了audioUnit后，设置属性：

kAudioOutputUnitProperty_EnableIO,打开IO。默认情况element0,也就是从APP到扬声器的IO时打开的，而element1,即从麦克风到APP的IO是关闭的。使用AudioUnitSetProperty函数设置属性，它的几个参数分别作用是：1.要设置的audioUnit 2.属性名称 3.element, element0和element1选一个，看你是接收音频还是播放 4.scope也就是范围，这里是播放，我们要打开的是输出到系统的通道，使用kAudioUnitScope_Output 5.要设置的值 6.值的大小。
比较难搞的就是element和scope,需要理解audioUnit的工作模式，也就是最开始的两张图。

设置输入格式AudioUnitSetProperty(audioUnit, kAudioUnitProperty_StreamFormat, kAudioUnitScope_Input, renderAudioElement, &audioDesc, sizeof(audioDesc));,格式就用AudioStreamBasicDescription结构体数据。输出部分是系统控制，所以不用管。

然后是设置怎么提供数据。这里的工作原理是：audioUnit开启后，系统播放一段音频数据，一个audioBuffer,播完了，通过回调来跟APP索要下一段数据，这样循环，知道你关闭这个audioUnit。重点就是：1. 是系统主动来跟你索要，不是我们的程序去推送数据 2.通过回调函数。就像APP这边是工厂，而系统是商店，他们断货了或者要断货了，就来跟我们进货，直到你工厂倒闭了、不卖了等等

所以设置播放的回调函数：

AURenderCallbackStruct callbackSt;
   callbackSt.inputProcRefCon = (__bridge void * _Nullable)(self);
   callbackSt.inputProc = playAudioBufferCallback;
AudioUnitSetProperty(audioUnit, kAudioUnitProperty_SetRenderCallback, kAudioUnitScope_Group, renderAudioElement, &callbackSt, sizeof(callbackSt));
传入的数据类型是AURenderCallbackStruct结构体，它的inputProc是回调函数，inputProcRefCon是回调函数调用时，传递给inRefCon的参数，这是回调模式常用的设计，在其他地方可能叫context。这里把self传进去，就可以拿到当前播放器对象，获取音频数据等。

回调函数

回调函数里最主要的目的就是给ioData赋值，把你想要播放的音频数据填入到ioData这个AudioBufferList里。结合上面的音频文件读取，使用ExtAudioFileRead读取数据就可以实现音频文件的播放。

播放功能本身是不依赖数据源的，因为使用的是回调函数，所以文件或者远程数据流都可以播放。

录音
录音类TFAudioRecorder，文件写入类TFAudioFileWriter和TFAACFileWriter。为了更自由的组合音频处理的组件，定义了TFAudioOutput类和TFAudioInput协议，TFAudioOutput定义了一些方法输出数据，而TFAudioInput接受数据。

在TFAudioUnitRecordViewController类的setupRecorder方法里设置了4种测试：

pcm流写入到caf文件
pcm通过extAudioFile写入，extAudioFile内部转换成aac格式，写入m4a文件
pcm转aac流，写入到adts文件
比较2和3两种方式性能
1. 使用audioUnit获取录音数据
和播放时一样，构建AudioComponentDescription 变量，使用AudioComponentFindNext寻找audioComponent,再使用AudioComponentInstanceNew构建一个audioUnit。

开启IO：
    UInt32 flag = 1;
   status = AudioUnitSetProperty(audioUnit,kAudioOutputUnitProperty_EnableIO, // use io
                                 kAudioUnitScope_Input, // 开启输入
                                 kInputBus, //element1是硬件到APP的组件
                                 &flag, // 开启，输出YES
                                 sizeof(flag));
element1是系统硬件输入到APP的element,传入值1标识开启。

设置输出格式：
AudioStreamBasicDescription audioFormat;
   audioFormat = [self audioDescForType:encodeType];
   status = AudioUnitSetProperty(audioUnit,
                                 kAudioUnitProperty_StreamFormat,
                                 kAudioUnitScope_Output,
                                 kInputBus,
                                 &audioFormat,
                                 sizeof(audioFormat));
在audioDescForType这个方法里，只处理了AAC和PCM两种格式，pcm的时候可以自己计算，也可以利用系统提供的一个函数FillOutASBDForLPCM计算,逻辑是跟上面的说的一样，理解音频里的采样率、声道、采样位数等关系就好搞了。

对AAC格式，因为是编码压缩了的，AAC固定1024frame编码成一个包(packet),许多属性没有用了，比如mBytesPerFrame，但必须把他们设为0，否则未定义的值可能造成影响。

设置输入的回调函数
AURenderCallbackStruct callbackStruct;
   callbackStruct.inputProc = recordingCallback;
   callbackStruct.inputProcRefCon = (__bridge void * _Nullable)(self);
   status = AudioUnitSetProperty(audioUnit,kAudioOutputUnitProperty_SetInputCallback,
                                 kAudioUnitScope_Global,
                                 kInputBus,
                                 &callbackStruct,
                                 sizeof(callbackStruct));
属性kAudioOutputUnitProperty_SetInputCallback指定输入的回调，kInputBus为1，表示element1。

开启AVAudioSession
   AVAudioSession *session = [AVAudioSession sharedInstance];
   [session setPreferredSampleRate:44100 error:&error];
   [session setCategory:AVAudioSessionCategoryRecord withOptions:AVAudioSessionCategoryOptionDuckOthers
                  error:&error];
[session setActive:YES error:&error];
AVAudioSessionCategoryRecord或AVAudioSessionCategoryPlayAndRecord都可以，后一种可以边播边录，比如录歌的APP，播放伴奏同时录制人声。

最后，使用回调函数获取音频数据
构建AudioBufferList,然后使用AudioUnitRender获取数据。AudioBufferList的内存数据需要我们自己分配，所以需要计算buffer的大小，根据传入的样本数和声道数来计算。

2.pcm数据写入caf文件
在TFAudioFileWriter类里，使用extAudioFile来做音频数据的写入。首先要配置extAudioFile：

构建
OSStatus status = ExtAudioFileCreateWithURL((__bridge CFURLRef _Nonnull)(recordFilePath),_fileType, &_audioDesc, NULL, kAudioFileFlags_EraseFile, &mAudioFileRef);
参数分别是：文件地址、类型、音频格式、辅助设置（这里是移除就文件）、audioFile变量。

这里_audioDesc是使用-(void)setAudioDesc:(AudioStreamBasicDescription)audioDesc从外界传入的，是上面的录音的输出数据格式。

写入
OSStatus status = ExtAudioFileWrite(mAudioFileRef, _bufferData->inNumberFrames, &_bufferData->bufferList);
在接收到音频的数据后，不断的写入，格式需要AudioBufferList，中间参数是写入的frame个数。frame和audioDesc里面的sampleRate共同影响音频的时长计算，frame传错，时长计算就出错了。

3. 使用ExtAudioFile自带转换器来录制aac编码的音频文件
从录制的audioUnit输出pcm数据，测试是可以直接输入给ExtAudioFile来录制AAC编码的音频文件。在构建ExtAudioFile的时候设置好格式：

AudioStreamBasicDescription outputDesc;
            outputDesc.mFormatID = kAudioFormatMPEG4AAC;
            outputDesc.mFormatFlags = kMPEG4Object_AAC_Main;
            outputDesc.mChannelsPerFrame = _audioDesc.mChannelsPerFrame;
            outputDesc.mSampleRate = _audioDesc.mSampleRate;
            outputDesc.mFramesPerPacket = 1024;
            outputDesc.mBytesPerFrame = 0;
            outputDesc.mBytesPerPacket = 0;
            outputDesc.mBitsPerChannel = 0;
            outputDesc.mReserved = 0;

重点是mFormatID和mFormatFlags，还有个坑是那些没用的属性没有重置为0。

然后创建ExtAudioFile:
OSStatus status = ExtAudioFileCreateWithURL((__bridge CFURLRef _Nonnull)(recordFilePath),_fileType, &outputDesc, NULL, kAudioFileFlags_EraseFile, &mAudioFileRef);

设置输入的格式：
ExtAudioFileSetProperty(mAudioFileRef, kExtAudioFileProperty_ClientDataFormat, sizeof(_audioDesc), &_audioDesc);

其他的不变，和写入pcm一样使用ExtAudioFileWrite循环写入，只是需要在结束后调用ExtAudioFileDispose来标识写入结束，可能跟文件格式有关。

4. pcm编码AAC
使用AudioConverter来处理，demo写在TFAudioConvertor类里了。

构建
OSStatus status = AudioConverterNew(&sourceDesc, &_outputDesc, &_audioConverter);

和其他组件一样，需要配置输入和输出的数据格式，输入的就是录音audiounit输出的pcm格式，输出希望转化为aac,则把mFormatID设为kAudioFormatMPEG4AAC,mFramesPerPacket设为1024。然后采样率mSampleRate和声道数mChannelsPerFrame设一下，其他的都设为0就好。为了简便，采样率和声道数可以设为和输入的pcm数据一样。

编码之后数据压缩，所以输出大小是未知的，通过属性kAudioConverterPropertyMaximumOutputPacketSize获取输出的packet大小，依靠这个给输出buffer申请合适的内存大小。

输入和转化
首先要确定每次转换的数据大小：bufferLengthPerConvert = audioDesc.mBytesPerFrame*_outputDesc.mFramesPerPacket*PACKET_PER_CONVERT;

即每个frame的大小 * 每个packet的frame数 * 每次转换的pcket数目。每次转换后多个frame打包成一个packet，所以frame数量最好是mFramesPerPacket的倍数。

在receiveNewAudioBuffers方法里，不断接受音频数据输入，因为每次接收的数目跟你转码的数目不一定相同，甚至不是倍数关系，所以一次输入可能有多次转码，也可能多次输入才有一次转码，还要考虑上次输入后遗留的数据等。

所以：

leftLength记录上次输入转码后遗留的数据长度，leftBuf保留上次的遗留数据

每次输入，先合并上次遗留的数据，然后进入循环每次转换bufferLengthPerConvert长度的数据，直到剩余的不足，把它们保存到leftBuf进行下一次处理

转换函数本身很简单：AudioConverterFillComplexBuffer(_audioConverter, convertDataProc, &encodeBuffer, &packetPerConvert, &outputBuffers, NULL);

参数分别是：转换器、回调函数、回调函数参数inUserData的值、转换的packet大小、输出的数据。

数据输入是在会掉函数里处理，这里输入数据就通过"回调函数参数inUserData的值"传递进去，也可以在回调里再读取数据。

OSStatus convertDataProc(AudioConverterRef inAudioConverter,UInt32 *ioNumberDataPackets,AudioBufferList *ioData,AudioStreamPacketDescription **outDataPacketDescription,void *inUserData){
    
    AudioBuffer *buffer = (AudioBuffer *)inUserData;
    
    ioData->mBuffers[0].mNumberChannels = buffer->mNumberChannels;
    ioData->mBuffers[0].mData = buffer->mData;
    ioData->mBuffers[0].mDataByteSize = buffer->mDataByteSize;
    return noErr;
}


