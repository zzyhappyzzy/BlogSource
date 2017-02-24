---
title: mac电脑隐藏文件的方法
date: 2016-04-28 11:34:45
tags: mac
categories: 小技巧
---
## 工具
terminal（终端）
## 显示mac电脑所有隐藏文件
```
defaults write com.apple.finder AppleShowAllFiles -bool true
```
## 隐藏mac电脑所有隐藏文件
```
defaults write com.apple.finder AppleShowAllFiles -bool false
```
## 主动隐藏某个文件(filename为想隐藏的文件的绝对路径名)
```
chflags hidden filename
```
## 主动取消某个文件的隐藏(filename为想隐藏的文件的绝对路径名)
```
chflags nohidden filename
```


