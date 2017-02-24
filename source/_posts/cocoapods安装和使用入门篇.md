---
title: cocoapods安装和使用入门篇
date: 2016-04-25 16:35:07
tags: cocoapods
categories: 学习笔记
---

cocoapods是Xcode管理第三方类库的工具（当然也可以管理自己封装的私有库），能够处理类库之间的依赖关系，方便多个类库的升级和管理。它是用ruby语言编写的。
<!-- more -->

#### 一、更换ruby源(ruby官方源很可能被墙了)
打开命令行窗口terminal，依次输入下面的命令
```
gem sources --remove https://rubygems.org/
gem sources --add https://ruby.taobao.org/
```
接着输入`gem sources -l`即可看到如下结果
```
*** CURRENT SOURCES ***

https://ruby.taobao.org/
```
最好顺便更新下gem，输入命令`sudo gem update --system`即可。（`可选`）

#### 二、安装cocoapods
* 安装最新版本`sudo gem install -n /usr/local/bin cocoapods`
* 安装指定版本`sudo gem install -n /usr/local/bin cocoapods --version 1.0.0`

#### 三、下载pod仓库索引
输入`pod setup`开始下载索引表，该过程耗时较长，请耐心等待。
如果下载一个小时还遥遥无期，那你可以从别人做的镜像下载索引表，步骤如下
```
pod repo remove master
pod repo add master https://git.coding.net/jasper/CocoaPods.git
pod repo update
```
>上面从镜像下载索引表的命令同样适用于私有库（如：公司内部搭建的各种私有库git索引表），只需输入
>pod repo add [repoName] url 时repoName不一样就行。
>cocoapods支持多个源（官方源和私有源等）

#### 四、更新pod索引表
每天都有人上传和更新pod仓库，所以最好定时更新pod索引表
```
pod repo update
```

#### 五、升级cocoapods版本
其实升级和安装的命令一样
* 升级到最新版本`sudo gem install -n /usr/local/bin cocoapods`
* 升级到指定版本`sudo gem install -n /usr/local/bin cocoapods --version 1.0.0`

#### 六、使用cocoapods
工程根目录下新建文件Podfile（该文件无后缀名）,编辑Podfile里的内容
* pod版本低于1.0.0

```
platform:ios, '7.0'
pod 'AFNetworking', '~> 2.3.1'
```

* pod版本大于等于1.0.0

```
#官方源
source 'https://github.com/CocoaPods/Specs.git'
#如果有内部私有源，再添加一条source即可

platform :ios, '7.0'
#use_frameworks!
inhibit_all_warnings!

target 'targetName' do
pod 'AFNetworking', '~> 2.3.1'
end

#修改工程配置等，根据情况配置，可选
post_install do |installer|
    installer.pods_project.targets.each do |target|
        target.build_configurations.each do |config|
            config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = '7.0'
            if target.name == 'OpenCore'
                config.build_settings['ENABLE_BITCODE'] = 'NO'
            end
        end
    end
end

```

#### 七、安装第三方库
使用`pod install`安装第三方库。
如果修改过Podfile文件(增加/删除podame)，需要使用`pod install`，而不是`pod update`。
update命令若不指定podName则更新所有有新版本的库`pod update [podName]`
>使用`pod install`后，需要点击\*.xcworkspace打开工程，而不是以前的\*.project

#### 八、卸载cocoapods
输入命令`sudo gem uninstall cocoapods`即可卸载cocoapods    

>每次执行`pod install`后，Podfile.lock都会更新。如果团队使用git/svn之类的版本管理工具，必须将Podfile.lock纳入版本管理。否则，团队中不同的人看到的cocoapods管理的第三方库版本可能不一致，甚至报错。
