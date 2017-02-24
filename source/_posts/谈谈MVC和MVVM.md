---
title: 谈谈MVC和MVVM
date: 2017-02-24 15:42:18
tags:
    - MVC
    - MVVM
categories: 设计模式
---

在IOS开发的过程中，你肯定听说过[MVC](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html)
下面记录下个人对这种设计模式的理解，顺便谈谈衍生的[MVVM](https://msdn.microsoft.com/en-us/library/hh848246.aspx)设计模式。

<!-- more -->

前言
===
不管采用哪种设计模式，都是为了程序结构更清晰，便于后续扩展、复用，从某方面来说，程序员的最大敌人就是需求的不断更新和增加，而我们要做的就是：以不变应万变。这就需要相应的设计模式了，否则，我们的程序只会越来越庞大，到处都是冗余代码，无法维护和扩展，最终谁也拿它没辙。

换句话说，编码的原则是DRY`Don't repeat yourself`，如果你发现项目里有很多重复/类似的代码、某个类特别庞大、改需求特别痛苦、新项目完全从零起步，那我们就必须思考问题出在哪，并用相应的设计模式来重构代码，避免发生上述的状况

Model-View-Controller
===
![](/mvc/model_view_controller.png)

* #### Model
模型层，封装数据结构和相应的逻辑操作等。不能和视图层有直接的通信。  
用户在视图层的操作，通过控制层更新相应的模型层，然后通知控制层更新视图层界面。

* #### View
视图层是展现给用户看的，知道怎样布局，响应用户的操作。通常视图层需要和模型层解耦。模型层的改变，通过控制层来更新视图层的显示。

* #### Controller
控制层协调单个或多个模型和视图的关联，分配任务及管理app的生命周期等。

>MVC模式里，Model、View和Controller相互独立，互不影响。
>通常为了实现需求，而没有将Controller进行拆分，导致Controller控制的状态过多，后期很难维护。
>需要将协调视图View和模型Model无关的代码抽出去，比如网络请求、数据库的读写等可以放到单独的模型Model里，Controller只需接受Model的更改并通知视图View更新即可。
>不同层不要相互引用，降低耦合。如：Model层不要直接引用View层，最好只引用同类别的Model

MVC在IOS开发中的不足
===
开发的过程中，由于Controller和View的紧耦合，导致MVC的三部分拆分并不完全独立，以及单元测试难以覆盖等问题。

Model-View-ViewModel
===
![](/mvc/ms_mvvm.png)

对比MVC，可以发现他们很相似，视图View和数据Model并没有直接通信，都要通过中间控制器协调（Controller或ViewModel）。  
那么在IOS开发中，ViewController和View都被划分到MVVM的视图层，Model还是模型层，ViewModel能主动调用Model的api对Model进行更改，也能在Model更改时对自身进行更新，然后通过View和ViewModel之间的绑定，对View进行更新。

View和ViewModel的绑定，常见的有两种方法
* KVO 如[Bond](https://github.com/ReactiveKit/Bond)
* 函数式响应编程 如[ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa)
