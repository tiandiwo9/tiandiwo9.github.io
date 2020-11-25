---
layout: post
title: RunLoop
date: 2018-5-1 15:32:24.000000000 +09:00
tags: [Swift]
---

## 加载方式
imageWithContentsOfFile：+图片路径（[[NSBundle mainBundle] pathForResource:@”icon” ofType:@”png”]），不会缓存到内存，适合用于加载较大的不常用的图片降低内存消耗。

imageNamed：+图片名，会缓存到内存且无法释放。

imageWithData：+二进制data，不缓存，适合网络下载图像生成UIImage。

然后将生成的UIImage赋值给UIImageView，CA捕捉到图层树的变化在下一个run loop中提交，然后对图片进行copy操作：解压缩成位图、读到内存中、CA渲染到UIImageView图层。

## 解压缩
位图其实是一个像素集合，所以图片的文件大小和其解压后所占的内存没有任何关系，只跟图片的像素有关（图片大小：像素宽*像素高*4字节）。但解压缩需要消耗大量CPU时间而且默认在主线程进行。所以出现了，在子线程提前强制解压缩的方案（因为默认的解压缩步骤是在显示到屏幕上的时候才执行的），核心函数：CGBitmapContextCreate。

SDWebImageDecoder中的decodedImageWithImage函数提供了解决方案，原理如下：CGBitmapContextCreate创建一个位图上下文→CGContextDrawImage绘制原始位图到上下文→CGBitmapContextCreateImage创建解压后的新位图。

注：CGContextDrawImage过程中把alpha通道移除了，所以不支持透明度的图片。

图片压缩
对于超大的图片，sd还提供了decodedAndScaledDownImageWithImage压缩方法，避免内存爆炸。原理：将图像矩阵按照规则分割成小型子矩阵进行压缩，然后插值拼接。

SDWebImage
> UIImageView+WebCache.h → SDWebImageManager → SDImageCache → SDWebImageDownloader

源码解析
加载：通常我们使用UIImageView显示图片，所以SDWebImage提供了它的扩展方法，一般使用

- (void)sd_setImageWithURL:(nullable NSURL *)url  //路径

placeholderImage:(nullableUIImage *)placeholder  //缺省图

options:(SDWebImageOptions)options  //加载规则

progress:(nullableSDWebImageDownloaderProgressBlock)progressBlock  //进度

completed:(nullableSDExternalCompletionBlock)completedBlock;  //完成的block

或其变种方法进行图片加载（底层依赖UIView+WebCache的扩展方法，更底层是SDWebImageManager的loadImageWithURL方法）。

注：底层流程，SDImageCache的queryCacheOperationForKey方法判断缓存，imageDownloader的downloadImageWithURL方法开启下载任务（方法中利用SDWebImageDownloaderOperation对象配置各种属性以及实现提前子线程解压缩的流程）。

SDWebImageManager：

下载图片：核心方法→loadImageWithURL

注：其中还封装了缓存类和下载器的单例；shouldDownloadImageForURL代理设置是否在无缓存时下载；transformDownloadedImage设置是否对图片进行transform操作。

SDImageCache：

请求获取缓存：核心方法→queryCacheOperationForKey（一般用url做key），实现流程→imageFromMemoryCacheForKey判断是否在缓存中，self.memCache；diskImageForKey从磁盘中获取，含解码decodedImageWithImage。

生成缓存：核心方法→storeImage: forKey: completion:（可设置是否缓存到硬盘等），实现流程→self.memCache setObject: forKey: cost: 实现内存缓存，storeImageDataToDisk: forKey: 实现磁盘缓存（文件名使用了key即URL的MD5转换）。

缓存清理：核心方法→clearMemory清理内存，clearDiskOnCompletion异步清理磁盘；每次app退出接收到UIApplicationWillTerminateNotification通知时执行deleteOldFilesWithCompletionBlock方法清理过期缓存，若清理完成后磁盘占用大于设定值self.config.maxCacheSize，则按照时间排序（NSURLContentModificationDateKey）后删除。

注：可设置maxMemoryCost最大空间花销或者maxMemoryCountLimit最大数量进行缓存约束；SDImageCacheConfig中控制了maxCacheAge过期时间和maxCacheSize最大缓存空间。

SDWebImageDecoder：

提前解码图片：核心方法→decodedImageWithImage，image.CGImage获取CGImageRef位图，CGColorSpaceRef色彩空间，宽高等，根据前文中的解压缩原理返回一个新的UIImage对象。

压缩图片：核心方法：decodedAndScaledDownImageWithImage，根据属性判断是否需要压缩，然后获取图形上下文和位图，计算像素并分块，然后循环插值。

底层依赖于Quartz 2D的图像处理库。

Memory 和 Disk 双缓存

首先，SDWebImage 的图片缓存采用的是 Memory 和 Disk 双重 Cache 机制， 听起来挺高大上吧。其实也不复杂。

我们先来看看 Memory Cache，贴一段 SDImageCache 的代码：

@interface SDImageCache ()

#pragma mark - Properties
@property (strong, nonatomic, nonnull) NSCache *memCache;

...

这里我们发现， 有一个叫做 memCache 的属性，它是一个 NSCache 对象，用于实现我们对图片的 Memory Cache。 SDWebImage 还专门实现了一个叫做 AutoPurgeCache 的类， 相比于普通的 NSCache， 它提供了一个在内存紧张时候释放缓存的能力：

@interface AutoPurgeCache : NSCache
@end

@implementation AutoPurgeCache

- (nonnull instancetype)init {
    self = [super init];
    if (self) {
#if SD_UIKIT
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(removeAllObjects) name:UIApplicationDidReceiveMemoryWarningNotification object:nil];
#endif
    }
    return self;
}

其实就是接受系统的内存警告通知，然后清除掉自身的图片缓存。 这里大家比较少见的一个类应该是 NSCache 了。 简单来说，它是一个类似于 NSDictionary 的集合类，用于在内存中存储我们要缓存的数据。详细信息大家可以参考官方文档：https://developer.apple.com/reference/foundation/nscache。

说完 Memory Cache， 我们再来说说 Disk Cache，也就是文件缓存。

SDWebImage 会将图片存放到 NSCachesDirectory 目录中：

- (nullable NSString *)makeDiskCachePath:(nonnull NSString*)fullNamespace {
    NSArray<NSString *> *paths = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES);
    return [paths[0] stringByAppendingPathComponent:fullNamespace];
}

然后为每一个缓存文件生成一个 md5 文件名, 存放到文件中。

整体机制

为了节约篇幅，提升大家的阅读体验，这里尽量少贴大段代码。 我们前面介绍了 SDWebImage 同时使用内存和硬盘两种缓存。 那么我们来看看当使用 SDWebImage 读取图片时候的完整流程。 我们一般会使用 SDWebImage 对 UIKit 的扩展，直接加载图片：

[imageView sd_setImageWithURL:[NSURL URLWithString:@"http://swiftcafe.io/images/qrcode.jpg"]];

首先这个 Category 方法 sd_setImageWithURL 内部会调用 SDWebImageManager 的 downloadImageWithURL 方法来处理这个图片 URL：

id <SDWebImageOperation> operation = [SDWebImageManager.sharedManager downloadImageWithURL:url options:options progress:progressBlock completed:^(UIImage *image, NSError *error, SDImageCacheType cacheType, BOOL finished, NSURL *imageURL) {
            ...
}];

SDWebImageManager 内部的 downloadImageWithURL 方法会先使用我们前面提到的 SDImageCache 类的 queryDiskCacheForKey 方法，查询图片缓存：

operation.cacheOperation = [self.imageCache queryDiskCacheForKey:key done:^(UIImage *image, SDImageCacheType cacheType) {

    ...
}];

再来看 queryDiskCacheForKey 方法内部， 先会查询 Memory Cache ：

UIImage *image = [self imageFromMemoryCacheForKey:key];
if (image) {
    doneBlock(image, SDImageCacheTypeMemory);
    return nil;
}

如果 Memory Cache 查找不到， 就会查询 Disk Cache:

dispatch_async(self.ioQueue, ^{
    if (operation.isCancelled) {
        return;
    }

    @autoreleasepool {
        UIImage *diskImage = [self diskImageForKey:key];
        if (diskImage && self.shouldCacheImagesInMemory) {
            NSUInteger cost = SDCacheCostForImage(diskImage);
            [self.memCache setObject:diskImage forKey:key cost:cost];
        }

        dispatch_async(dispatch_get_main_queue(), ^{
            doneBlock(diskImage, SDImageCacheTypeDisk);
        });
    }
});

查询 Disk Cache 的时候有一个小插曲，就是如果 Disk Cache 查询成功，还会把得到的图片再次设置到 Memory Cache 中。 这样做可以最大化那些高频率展现图片的效率。

如果缓存查询成功， 那么就会直接返回缓存数据。 如果不成功，接下来就开始请求网络：

id <SDWebImageOperation> subOperation = [self.imageDownloader downloadImageWithURL:url options:downloaderOptions progress:progressBlock completed:^(UIImage *downloadedImage, NSData *data, NSError *error, BOOL finished) {
}

请求网络使用的是 imageDownloader 属性，这个示例专门负责下载图片数据。 如果下载失败， 会把失败的图片地址写入 failedURLs 集合：

if (   error.code != NSURLErrorNotConnectedToInternet
    && error.code != NSURLErrorCancelled
    && error.code != NSURLErrorTimedOut
    && error.code != NSURLErrorInternationalRoamingOff
    && error.code != NSURLErrorDataNotAllowed
    && error.code != NSURLErrorCannotFindHost
    && error.code != NSURLErrorCannotConnectToHost) {
    @synchronized (self.failedURLs) {
        [self.failedURLs addObject:url];
    }
}

为什么要有这个 failedURLs 呢， 因为 SDWebImage 默认会有一个对上次加载失败的图片拒绝再次加载的机制。 也就是说，一张图片在本次会话加载失败了，如果再次加载就会直接拒绝。SDWebImage 这样做可能是为了提高性能吧。这个机制可能会容易被大家忽略，所以这里特意提一下，说不定哪天遇到一些奇怪问题时候，这个知识点会帮你快速定位问题~

如果下载图片成功了，接下来就会使用 [self.imageCache storeImage] 方法将它写入缓存，并且调用 completedBlock 告诉前端显示图片：

if (downloadedImage && finished) {
    [self.imageCache storeImage:downloadedImage recalculateFromImage:NO imageData:data forKey:key toDisk:cacheOnDisk];
}

dispatch_main_sync_safe(^{
    if (strongOperation && !strongOperation.isCancelled) {
        completedBlock(downloadedImage, nil, SDImageCacheTypeNone, finished, url);
    }
});

好了，到此为止 SDWebImage 的整体图片加载流程就都走完了。 由于要控制篇幅，我这里只挑了最重点的几个节点写出来，SDWebImage 的完整机制肯定会更全面，先帮大家疏通思路。

是否要重试失败的 URL

SDWebImage 的整体图片处理流程咱们体验了一遍。 那么有哪些重要的点对咱们使用它会有帮助呢？ 我帮大家整理了一些。

你可以在加载图片的时候设置 SDWebImageRetryFailed 标记，这样 SDWebImage 就会加载之前失败过的图片了。 记得我们前面提到的 failedURLs 属性了吧，这个属性是在内存中存储的，如果图片加载失败， SDWebImage 会在本次 APP 会话中都不再重试这张图片了。当然这个加载失败是有条件的，如果是超时失败，不记在内。

总之，如果你更需要图片的可用性，而不是这一点点的性能优化，那么你就可以带上 SDWebImageRetryFailed 标记：

[_image sd_setImageWithURL:[NSURL URLWithString:@"http://swiftcafe.io/images/qrcodexx.jpg"] placeholderImage:nil options:SDWebImageRetryFailed];

Disk 缓存清理策略

SDWebImage 会在每次 APP 结束的时候执行清理任务。 清理缓存的规则分两步进行。 第一步先清除掉过期的缓存文件。 如果清除掉过期的缓存之后，空间还不够。 那么就继续按文件时间从早到晚排序，先清除最早的缓存文件，直到剩余空间达到要求。

具体点，SDWebImage 是怎么控制哪些缓存过期，以及剩余空间多少才够呢？ 通过两个属性：

@interface SDImageCache : NSObject

@property (assign, nonatomic) NSInteger maxCacheAge;
@property (assign, nonatomic) NSUInteger maxCacheSize;

maxCacheAge 是文件缓存的时长， SDWebImage 会注册两个通知：

[[NSNotificationCenter defaultCenter] addObserver:self
                                                 selector:@selector(cleanDisk)
                                                     name:UIApplicationWillTerminateNotification
                                                   object:nil];

[[NSNotificationCenter defaultCenter] addObserver:self
                                         selector:@selector(backgroundCleanDisk)
                                             name:UIApplicationDidEnterBackgroundNotification
                                           object:nil];

分别在应用进入后台和结束的时候，遍历所有的缓存文件，如果缓存文件超过 maxCacheAge 中指定的时长，就会被删除掉。

同样的， maxCacheSize 控制 SDImageCache 所允许的最大缓存空间。 如果清理完过期文件后缓存空间依然没达到 maxCacheSize 的要求， 那么就会继续清理旧文件，直到缓存空间达到要求为止。

了解了这个机制对我们有什么帮助呢? 我们来继续讲解，我们平时在使用 SDWebImage 的时候是没接触过它们的。那么以此推理，它们一定有默认值，也确实有：

static const NSInteger kDefaultCacheMaxCacheAge = 60 * 60 * 24 * 7; // 1 week

上面是 maxCacheAge 的默认值，注释上写的很清楚，缓存一周。 再来看看 maxCacheSize。 翻了一遍 SDWebImage 的代码，并没有对 maxCacheSize 设置默认值。 这就意味着 SDWebImage 在默认情况下不会对缓存空间设限制。

这一点可以在 SDWebImage 清理缓存的代码中求证：

if (self.maxCacheSize > 0 && currentCacheSize > self.maxCacheSize) {
    //清理缓存代码
}

说明一下， 上面代码中的 currentCacheSize 变量代表当前图片缓存占用的空间。 从这里可以看出， 只有在 maxCacheSize 大于 0 并且当前缓存空间大于 maxCacheSize 的时候才进行第二步的缓存清理。

这意味着什么呢？ 其实就是 SDWebImage 在默认情况下是不对我们的缓存大小设限制的，理论上，APP 中的图片缓存可以占满整个设备。

SDWebImage 的这个特性还是比较容易被大家忽略的，如果你开发的类似信息流的 APP，应该会加载大量的图片，如果这时候按照默认机制，缓存尺寸是没有限制的，并且默认的缓存周期是一周。 就很容易造成应用存储空间占用偏大的问题。

那么可能有人会说了，现在 iPhone 的存储空间都很大，多缓存一点也不是问题吧? 但你是否知道 iOS 上还有一个用量查询的功能呢。在设置项中用户可以查看每个 APP 的空间使用情况， 如果你的 APP 占用空间比较大的话，就很容易成为用户的卸载目标，这应该是需要关注的一个细节。

另外，过多的占用缓存空间其实并不一定有用。大部分情况是一些图片被缓存下来后，很少再被重复展现。所以合理的规划缓存空间尺寸还是很有必要的。可以这样设置：

[SDImageCache sharedImageCache].maxCacheSize = 1024 * 1024 * 50;    // 50M

maxCacheSize 是以字节来表示的，我们上面的计算代表 50M 的最大缓存空间。 把这行代码写在你的 APP 启动的时候，这样 SDWebImage 在清理缓存的时候，就会清理多余的缓存文件了。



### 缓存淘汰算法之LRU
：最近最少使用。判断最近被使用的时间，最远的优先淘汰。新数据插入链表头部，缓存被命中时移动到链表头部，链表满时丢弃尾部。

命中率：存在热点数据时效率很好但偶发性、周期性的批量操作会导致命中率急剧下降，缓存污染情况严重。

代价：遍历链表找命中的数据块，然后移动到头部。

