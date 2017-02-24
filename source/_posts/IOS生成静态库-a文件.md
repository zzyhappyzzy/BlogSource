---
title: IOS生成静态库.a文件
date: 2016-04-25 11:32:26
tags: IOS
categories: 学习笔记
---

说明
===
库是程序代码的合集，常见的有开源库和闭源库，其中.a就是闭源库（无法看到具体实现方法），而[Github](https://github.com)上的代码基本都是开源库。静态库执行时会被复制到可执行文件中，多次调用则会出现冗余拷贝，不建议使用静态库。可以使用动态库.dylib(动态库貌似通不过appstore审核)或者源码。
生成.a文件
===
一个.a文件，要想兼容各种不同的cpu架构，需要包含各种指令集(amrv7/armv7s/arm64/i386/x86_64等)。常用的查看和合并.a文件的指令`lipo`。
<!-- more -->
步骤和说明
===
1. 打开Xcode创建新工程，选择相应的系统，本文以IOS系统为例。选择`Framework&Library`中的`Cocoa Touch Static Library`。接着输入工程名字即可。
2. 新建文件夹。暂时取名StaticDemo,所有需要打包生成.a的文件都将放在该目录下。
3. 在StaticDemo目录下分别创建3个类`TestPublicClass/TestObj1/TestObj2`，其中`TestPublicClass.h`准备对外公开。
4. 随便选择一个模拟器(如iphoen6)，`Command+B`编译完成后就会生成.a文件，该文件是用于模拟器的（Release版本可以不合并），然后选择`Generic IOS Device`，再次编译完成，会生成.a文件，该文件是用于真机的。 他们的路径可以右键.a文件在Finder中查看。
5. 查看.a文件支持的arm指令集，使用`lipo -inof *.a`,如:
```
lipo -info libGenerateStaticLib.a 
Architectures in the fat file: libGenerateStaticLib.a are: armv7 arm64 
```
6. 合并不同架构的.a文件，使用命令`lipoi -create source1.a source2.a -output dest.a`，如：
```
lipo -create Debug-iphoneos/libGenerateStaticLib.a Debug-iphonesimulator/libGenerateStaticLib.a -output libGS.a
```
	合并后，可以通过`lipo -info *.a`确认
```
lipo -info libGS.a 
Architectures in the fat file: libGS.a are: armv7 x86_64 arm64 
```
	可以看到.a已经合并成功。接着将头文件`TestPublicClass.h`复制出来，.a文件打包就大功告成了。
iphone设备arm架构表
===
[参考链接](https://www.innerfence.com/howto/apple-ios-devices-dates-versions-instruction-sets)

device name | ARM
:--:        | :--:
iphone4s及之前 | armv7
iphone5/5c     | armv7s
iphone5s/6/6s/6p/se | arm64
结束
===
arm指令能够向下兼容，所以只需32和64位各一个.a文件就行了。目前xcode7.2默认的architecture为armv7和arm64，已经能够适应任何iphone设备了。
