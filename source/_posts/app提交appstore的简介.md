---
title: app提交apptore的简介
date: 2016-04-26 10:38:17
tags: iOS
categories: Apple
---

申请开发者账号
===
1. 登陆苹果官网[Apple Develper](https://developer.apple.com/programs/enroll/),注册开发者账号
2. 生成certificate证书(用系统的keychain生成.CSR文件，该文件最好长期保存，后续加入新app或者证书过期需重新生成时都需要该文件，这样服务器就不用更新推送证书了，而且兼容之前老证书发的包)，生成对应的证书打包提交AppStore。（个人开发者账号打的包提交appstore，企业开发者账号打的是Inhouse证书的包，无需通过appstore，可以直接通过[fir](http://www.fir.im/)等分发就行）
3. 登陆[Itunes Connect](https://itunesconnect.apple.com),创建App
<!-- more -->
Itunes Connect注意事项
===
1. AppIcon,尺寸1024*1024px,包括Ipad和Iphone
2. Screenshots,app截图，最多5张，以竖屏为例子
      * Iphone4/4s: 640*960px
      * Iphone5/5s: 640*1136px
      * Iphone6: 750*1334px
      * Iphone6 plus: 1242*2208px
      * Ipad: 1536*2048px
3. 基本信息
      * app的描述
      * 关键字，多个关键字逗号隔开，用于appStore搜索
      * 技术支持网站
      * 推广网站 `可选`
      * 隐私声明网站 `可选`
      * app的类别，如教育-工具
      * 年龄评级，勾选相关项，系统会自动计算
      * 设置价格
      * 根据实际产品，选择性设置In-AppPurchase、Game Center、Newsstand等 `可选`
      * 发布的市场。默认全球appStore上线，如果要求在特定区域的市场上线，需要提交.geojson格式的文件 `可选`
      * 发布方式。默认是审核通过就发布到appStore，也可以选择手动发布
4. 版权信息
      * 版权所有，如“2015 Acme Inc”
      * 负责人姓名、手机、邮箱
5. 审核
      * 审核时遇到问题时的联系人：姓名、手机、邮箱
      * 审核时的测试账号(长期有效，最高权限)
      * 审核时的注意事项和备注
6. 将通过测试验收的工程，Xcode等工具打包并添加相应的证书，提交appStore
7. 确认符合苹果[审核要求](https://developer.apple.com/app-store/review/guidelines/)
8. 进入[Itunes Connect](https://itunesconnect.apple.com)，确认信息完整后，点击右上角的提交审核按钮
