---
title: Xcode的BuildSetting配置说明
date: 2016-04-29 14:24:46
tags: Xcode
categories: Apple
---
是否采用ARC
===
1. 某个文件采用ARC，可以在`Build Phases`中的`Compile Sources`中找到该类，双击修改`Compiler Flags`为`-fobjc-arc`。 
2. 某个文件采用MRC，可以在`Build Phases`中的`Compile Sources`中找到该类，双击修改`Compiler Flags`为`-fno-objc-arc`。

全局宏定义
===
一个workspace/project如果包含多个target或者scheme，可以在`BuildSetting->Preprocessing->Preprocessor Macros`中定义自己的宏来区别对待多target喝scheme等
第三方库路径配置
===
有时第三方库（一般是.a之类的)引入时，查找路径没配好，可能导致编译失败。可以在`BuildSetting->SearchPaths`中修改库的查找路径为相对路径（不要用本地的绝对路径）

