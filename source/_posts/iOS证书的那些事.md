---
title: iOS证书的那些事
date: 2017-03-09 11:33:22
tags: [证书, certificate, iOS]
categories: Apple
---

提到iOS开发，肯定少不了与证书打交道。本文主要记录下个人对证书的一些理解，希望大家少踩坑。

<!-- more -->
概要
===
虽然苹果整个生态链是封闭未开源的，但安全性在业内绝对靠前。证书，也可以说是数字签名，只是整个生态链安全性中的一环而已。无论是底层系统的设计，上架审核，还是对https的推行，以及近期火热的JSPatch等动态更新问题，都可以看出苹果还是很重视生态链的稳定和安全。

在这里，我并不会告诉你证书生成的具体流程，因为网上相关资料很多。

要想对app进行签名，需要拥有苹果开发者账号（个人或企业），在[开发者中心](https://developer.apple.com/account/)创建根证书（开发development和生产production），然后在根证书的基础上，不同的应用application根据bundleId不一样，创建相应的推送证书、开发证书等一系列相关的证书。
>如果发布应用，需要下载Apple全球开发者证书[AppleWWDRCA.cer](https://developer.apple.com/certificationauthority/AppleWWDRCA.cer)

苹果的证书，采用非对称加密算法，有公钥和私钥，通常我们所说的CSR`Certificate Signing Request`文件就是用来创建私钥的。所以，为了便于后续私钥的管理，建议所有证书用相同的CSR文件，CSR文件一直保留
>当然，不同的证书可以有不同的CSR文件，只要你自己不嫌麻烦
>CSR文件是用KeyChain生成的(`Keychain Access > Certificate Assistant > Request a Certificate From a Certificate Authority`)。注意不要填写CA，邮箱最好填AppleId账号，名字看着取，最后选择存储到磁盘即可
>谁电脑创建的证书，需要将证书导出（KeyChain找到相应的证书，可以将展开的两个都选中，也就是公钥和私钥都选中，右键导出为p12文件），传给没有私钥的开发

应用支持的功能（GameCenter、PushNotification、InAppPurchase等），在开发者中心的Identifiers（即app IDs）里随时编辑的。
>比如开始某app没有推送，后期接入推送，需要在app IDs里找到相应的bundleId开启PushNotification功能，然后生成推送证书

证书过期
===
苹果的证书一般有效期是一年，即将过期时，开发者需要对证书进行更新，整个过程类似新建证书
>对于即将过期的证书，并不需要撤销Revoke。如果一定要撤销，最好在新证书都搞定后，服务器也更新了相应的推送证书后，再操作（保证不影响线上产品）
>推送证书是和app的bundleId绑定的，服务器只需更新最新的推送证书即可，之前的版本也可以收到推送，无需重新发包
>企业证书等非appstore的证书(InHouse/AdHoc等)签发的安装包，一旦过期，必须用最新的证书重新打包，发布新版本(过期证书签发的包无法使用)。
>appstore证书签发的包，一旦过期，已存在的产品不受影响（注意：如果推送证书过期，还是必须更新推送证书的）

模拟推送
===
将推送证书p12转换为pem文件，可以使用下面命令
```shell
openssl pkcs12 -in cert.p12 -out apple_push_notification.pem -nodes -clcerts
```
可以使用php、ruby、python、nodeJ等各种语言实现。

推荐个ruby写的推送模拟：[Houston](https://github.com/nomad/houston)

