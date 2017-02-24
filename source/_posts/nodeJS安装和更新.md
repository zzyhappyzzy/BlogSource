---
title: nodeJS安装和更新
date: 2016-06-23 11:06:30
tags: node.js
categories: javascript
---

## 说明
本文只记录macOSX系统，其它系统参考[官网](https://nodejs.org/en/)
<!--more-->
## 安装
### [HomeBrew](http://brew.sh/)安装
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```
### 安装包
[官网](https://nodejs.org/en/)下载最新安装包即可
## 更新
### HomeBrew安装的node
```
brew upgrade node
```
### 其它方式安装的node
使用node的包管理工具[n](https://github.com/tj/n)  
 
```
sudo npm cache clean -f
sudo npm install -g n
sudo n stable
```
## n简要说明
`sudo n version` 可以安装指定node版本，如`sudo n v4.4.5`（如果电脑没有指定的版本，则会自动安装并将node切换到该版本；如果安装了，则直接切换到指定的版本）。  
`n list`可以查看所有的node版本  
`n`可以查看已安装的node版本
## 卸载
HomeBrew安装的`brew uninstall node`  
n安装的`sudo n rm version`