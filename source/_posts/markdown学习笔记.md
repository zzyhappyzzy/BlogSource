---
title: markdown学习笔记
date: 2016-04-14 15:40:08
tags: markdown
categories: 学习笔记
---

##  markdown的目标是实现: 易读易写

#### <a name="html">1、兼容html。</a>  

<p>A paragraph in <b>HTML</b> displayed as code</p>

```html
<p>A paragraph in <b>HTML</b> displayed as code</p>
```
#### 2、不在markdown涵盖范围之内的标签，都可以直接在文档里用html编写，不需要额外标注。
#### 3、html文件中，有两个字符需要特殊处理：&lt;`&lt;`和&amp;`&amp;`。&lt;用于起始标签，&amp;用于标记html实体。插入版权符号&copy;`&copy;`
#### 4、段落的前后要有一个以上的空行，普通段落不需要空格或制表符来缩进。
<!-- more -->
#### 5、支持两种标题的语法，类Setext和类atx形式。例如：
```
This is an H1
============

This is an H2
------------
```
任何数量的=和-都可以有效果。
也可以选择类Atx形式在行首插入1到6个#,对应1到6阶标题，例如:  
```
# 这是H1
###### 这是H6
```
#### 6、区块引用Blockquotes，在段落前加上>来标记区块引用。可以叠加嵌套，例  
> ## This is the first level of quoting.
>
> > This is nested blockquote.
>
> Back to the first level.

#### 7、列表
markdown支持无序和有序列表。无序列表使用星号、加号或减号作为列表标记  
```
* Red
* Green
* Blue
```
有序表则使用数字接着一个英文句点:  
```
1. Bird
2. Mchale
3. Parish
```
列表项中可以包含多个段落，每个分类下的段落必须缩进4个空格或1个制表符  
```
1.	This is a list item with two paragraphs. Lorem ipsum dolor sit 
   	amet,consiter adipiscing elit. A;iquam henderit mi posure lecture
   	Greateful.

   	Another paragraph

2. 	Suspend id is what a good.
3. 	Third item
```
#### 8、建立分割线。可以在一行中用三个以上的星号、减号、底线来建立分割线。也可以在星号和减号之间插入空格  
```
***
---
* * *
```
#### 9、链接`[tittle](url)`
markdown中文网站 [markdown](http://www.markdown.cn)
markdown的VIM编辑器 [vim-markdown](https://github.com/plasticboy/vim-markdown)
#### 10、图片`![title](imgUrl)`
This is an image ![IMG](http://iissnan.com/nexus/next/next-schemes.jpg)

* * *

#### 11、表格  
|Colum1|Colum2  |Colum3 | Colum4  |
|:----:|:----:  |:----- | -------:|
|中对齐|中对齐  |左对齐 | 右对齐  |
|中对齐|好的好的|哈     | 简单介绍|
|中对齐|但是    |动画   | 哈哈    |

表格代码如下:
```
|Colum1|Colum2  |Colum3 | Colum4  |
|:----:|:----:  |:----- | -------:|
|中对齐|中对齐  |左对齐 | 右对齐  |
|中对齐|好的好的|哈     | 简单介绍|
|中对齐|但是    |动画   | 哈哈    |
```
#### 12、[锚点链接](#html)
```
之前的第一条定义如下：
<a name="html">1、兼容html。</a>
然后可以直接锚点跳转过去，使用方法如下:
[锚点名称](#锚点名)
```
#### 13、视频链接
其实就是插入视频截图然后将链接设置为视频的实际地址`[![vedio](/ios10/ios10_preview.png)](http://open.163.com/movie/2011/10/F/G/M7GF17HPS_M7GHGQTFG.html)`  
[![vedio](/vedio/vedio_1.png)](http://open.163.com/movie/2011/10/F/G/M7GF17HPS_M7GHGQTFG.html)
