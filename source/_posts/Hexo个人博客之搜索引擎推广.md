---
title: Hexo个人博客之搜索引擎推广
date: 2016-04-22 17:21:49
tags: SEO
categories: 疑难解答
---

### 说明
前段时间抽空搭建了个人博客，来回也折腾了不少时间，好不容易写了点东西，发现搜索引擎居然不能找到自己写的东西，于是就开始研究了下搜索引擎推广。

### 平台
我的博客采用[Hexo](http://hexo.io)框架搭建的，存储在[Github](https://github.com)上。然后自己买了个域名，现在博客主页基本配置完成[我的主页](http://zyoung.me)。

### 确认自己网站是否被收录
在浏览器的搜索框输入`site:zyoung.me`，查看搜索结果就能明白了。（这个域名是我的，大家换成自己的网站域名就可以了）。提供下Google和Baidu的网站验证入口
* [Google搜索引擎提交入口](https://www.google.com/webmasters/tools/home?hl=zh-CN)
* [Baidu搜索引擎提交入口](http://www.baidu.com/search/url_submit.htm)

<!-- more -->

如果你的博客也在gitHub上，百度搜索引擎估计不好用（Github屏蔽了百度的抓取)。
### 验证网站
一般验证域名所有权有很多方法，但是最简单的还是下载html文件到网站的根目录下。如果你的博客是hexo的框架搭建的，需要注意:
> 下载的html文件需要放在source目录下，而不是手动添加到根目录下，类似的CNAME也需要放在source目录下。

由于hexo发布时，默认会对文件进行格式化等操作，但是我们下载的验证文件是不允许做格式化等操作的（会导致验证文件内容更改），否则验证无法通过。需要告诉hexo不要格式化特定的文件，需要在文件内容加入以下内容:
```
layout: false
---
```
所以，最终Google的验证文件内容cat出来为
```
layout: false
---
google-site-verification: google*******.html
```
### 站点地图sitemap.xml
验证网站后，一般还需要生成站点地图，进一步方便搜索引擎抓取网站的内容。
如果使用hexo的小伙伴，可以直接使用插件，发布时会自动生成sitemap。
```
npm install hexo-generator-sitemap --save
npm install hexo-generator-baidu-sitemap --save
```
生成的sitemap文件在public目录
### 结束语
暂时就这么多吧，其他的比如百度push、结构化数据标记等高阶内容，暂时还没研究
