---
title: ios app 性能优化的几个建议
date: 2016-04-28 18:14:10
tags: iOS
categories: Apple
---
## 参考链接
[raywenderlich](https://www.raywenderlich.com/31166/25-ios-app-performance-tips-tricks)

## 优化建议
当你开发了几款app后，是否考虑过怎样才能提高自己的app的性能。在开发的过程中，只要我们注意一些要点，可以很轻松地开发出性能优雅的app。
这篇文章介绍了23个提高app性能的tips。
<!-- more -->
#### 使用ARC
和retain/release说拜拜。
#### 使用重用机制
特别是tableview/collectionview的cell，必须使用重用机制。
#### view的Opaque属性设置为YES
#### 不要在一个xib或者storyboard放置太多的东西
因为每次系统加载xib到内存的时候，所有相关的内容都会加载（不管是否展现）,并且相关联的图片等资源也会被加载到内存（图片等还会缓存），很可能导致内存暴涨。
#### 不要阻塞主线程
耗时等操作在分线程执行，只有需要刷新界面是才在主线程。学会多线程开发和异步操作很有必要。
#### 尽可能使图片的大小和imageview一样大
网络下载的图片，可以先重画为imageview一样大小在显示。因为imageview拉伸或缩小图片代价较大，特别是当imageview嵌套在UIScrollview里的时候。
#### 选用合适的数据容器。[容器说明](https://developer.apple.com/library/ios/#documentation/cocoa/conceptual/collections/Collections.html)
```
* NSArrayi:有序的。能搞按照索引快速查找元素。按照值查找很慢，插入和删除很慢
* NSDictionary:无序的。键值对存储。按照key可以快速查找。
* NSSet: 无序的。按照值可以快速查找，插入和删除很快。
```
#### 服务器和客户端采用GZIP压缩
可以减少网络交互的等待时间和流量。
#### 尽量重用view。
重复生成相同模板的view时，可以参照UITableview的重用机制，自己写一套reuse机制。只有在需要时才加载相关view，无关的view只有需要才加载。（如果为了更好的用户体验，在某些情况下可以预加载某些view并让它隐藏，需要时才显示）
#### 缓存，缓存，缓存
服务器的图片下载完成并缓存，以后就可以快速读取并加载。tableviewcell的高度也可以缓存。
#### 考虑图片资源和绘图的代价
某些效果是切图好还是代码绘制好，需要衡量下
#### 响应系统发出的内存不足的警告
如果收到内存不足的通知，需要马上释放缓存及其它可以再次创建的对象。
#### 重用一些初始化比较慢的对象
NSDateFormatter/NSCalendar等，一般可以在你的类中添加一个相应的property即可。
```objc
// in your .h or inside a class extension
@property (nonatomic, strong) NSDateFormatter *formatter;

// inside the implementation (.m)
// When you need, just use self.formatter
- (NSDateFormatter *)formatter {
    static NSDateFormatter *formatter;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        _formatter = [[NSDateFormatter alloc] init];
        _formatter.dateFormat = @"EEE MMM dd HH:mm:ss Z yyyy"; // twitter date format
    });
    return formatter;
}
//这里用GCD可以避免多线程调用重复alloc的问题（如果某个属性不会被多个线程同时调用，不用这么复杂），如下：
- (NSDateFormatter *)formatter {
    if (! _formatter) {
        _formatter = [[NSDateFormatter alloc] init];
        _formatter.dateFormat = @"EEE MMM dd HH:mm:ss Z yyyy"; // twitter date format
    }
    return _formatter;
}
```
#### 游戏开发，使用sprite sheets
#### 尽量避免数据结构的来回转换
服务器的json数据最好是不做处理就能直接使用，可以减少很多中间的转换过程。（如:json转成model）（汗!个人还是推荐转成model）
#### 选择合适的数据类型
服务器端发来的数据类型常见的有json和xml，具体看情况选择。xml支持边下载边解析，json必须下载完成才能开始解析。通常还是选择json
#### 选择合适的背景图
如果你有一张全屏的背景图，在底层价格UIImageView更好
```objc
// You could also achieve the same result in Interface Builder
UIImageView *backgroundView = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"background"]];
[self.view addSubview:backgroundView];
```
如果你有一张较小的带颜色的图，采用背景色较好
```objc
self.view.backgroundColor = [UIColor colorWithPatternImage:[UIImage imageNamed:@"background"]];
```
#### 设置阴影路径
当需要给一个view增加阴影效果时，你会怎么做？
大部分的开发者会这样写：
```objc
#import <QuartzCore/QuartzCore.h>

// Somewhere later ...
UIView *view = [[UIView alloc] init];

// Setup the shadow ...
view.layer.shadowOffset = CGSizeMake(-1.0f, 1.0f);
view.layer.shadowRadius = 5.0f;
view.layer.shadowOpacity = 0.6;
```
看起来很简单，不是吗？
但糟糕的是，这样并不好。CoreAnimation框架需要耗费不小代价才能绘制出阴影。
好消息是，有一种更简单的方法：设置阴影路径。
```objc
view.layer.shadowPath = [[UIBezierPath bezierPathWithRect:view.bounds] CGPath];
```
缺点是，每次frame改变后，都需要更新shadow path。

#### 优化tableview
为了使tableview滑动更平滑，需要确保下面几项
* cell复用
* view的opaque属性尽可能设置为YES
* 避免渐变、图片伸缩、离屏渲染
* 如果cell高度不变，缓存计算出的高度
* 如果cell展示的内容包含网络请求的内容，缓存并且异步加载
* 阴影用shadow path
* 尽可能减少cell的subviews数量
* 在cellForRowAtIndexPath方法尽量少做事，如果必须做，尽可能缓存耗时操作的结果。
* 抽象出合理的数据结构来展示信息
* 直接设置rowHeight、SectionFooterHeight、SectionHeaderHeight的值，不要调用相应的代理delegate

#### 选择合适的数据存储器
当你需要存储/读取大量数据的时候，你会怎么做？
一般有以下几个选择:
* 用NSUserDefaults存储
* 用格式化文件存储（如XML、JSON、Plist等）
* 归档（实现NSCoding协议）
* 数据库，如SQlite等
* 用IOS特有的Core Data

NSUserDfault只适用于存储少量的数据。当你需要存储大量的数据时，其它选项更好
格式化文件存储，缺点是你必须将整个文件读取到内存才能开始解析。
好吧，那么NSCoding？但是它也是文件存储，缺点通格式化文件一样。
最好的选择就是SQlite或CoreData。通过这种存储，你可以只加载需要的数据，并且不用写复杂的查询等语法。在性能方面，sqlite和coreData差不多。
通常苹果建议使用coreData，但你也可以使用轻量型的Sqlite。如果你决定采用Sqlite存储，可以参考[FMDB](https://github.com/ccgus/fmdb)
#### 复杂耗时操作异步执行
#### @autoreleasepool可以保证块中的内容合理释放
[参考](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmAutoreleasePools.html)
```objc
NSArray *urls = <# An array of file URLs #>;
for (NSURL *url in urls) {
    @autoreleasepool {
        NSError *error;
        NSString *fileContents = [NSString stringWithContentsOfURL:url
                                         encoding:NSUTF8StringEncoding error:&error];
        /* Process the string, creating and autoreleasing more objects. */
    }
}
```
#### 图片是否在内存缓存
ios有两种加载图片的方法
* imageName
* imageWithContentOfFile

那么，这两种方法有什么区别呢？
imageName会将加载的图片缓存到内存中，而imageWithContentOfFile仅仅加载图片，不缓存。
如果加载大图并且调用次数很少，建议用imageWithContentOfFile。小图或者经常调用的图可以用imageName加载。
