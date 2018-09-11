---
title: ios各种指针说明
date: 2016-04-29 14:01:59
tags: iOS
categories: Apple
---
strong、weak、unsafe_unretained、autoreleasing、copy说明
===
1. strong 指针的默认类型。该指针指向的对象在指针存活期间一直存在，不会被销毁。
2. weak   当没有strong指针指向对象时，weak指针指向的对象将被销毁，同时weak指针为变为nil。
3. unsafe_unretained 该指针指向的对象销毁时，该指针不会被置为nil，而是被挂起(类似野指针)。`最好别用`
4. autoreleasing 一般指向函数的参数类型为`id *`并且自动释放的参数。
5. copy 类似strong，但是会复制一份新的对象（即指向新的内存地址），新指向的对象存活期同strong。

参考链接
===
[Apple](https://developer.apple.com/library/ios/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4)
