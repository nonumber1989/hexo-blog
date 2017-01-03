---
title: 构建微服务之:微服务架构中的进程间通信
tags: [microservice]
categories: microservice
---

**原文链接:[*Building Microservices: Inter-Process Communication in a Microservices Architecture*](https://www.nginx.com/blog/building-microservices-inter-process-communication/?utm_source=introduction-to-microservices&utm_medium=blog&utm_campaign=Microservices)**

1. *[微服务介绍](http://www.jianshu.com/p/8d2cfa1fa633)*
2. *[构建微服务之使用API网关](http://www.jianshu.com/p/9e90b2a5df7b)*
3. *构建微服务之:微服务架构中的进程间通信(本文)*
4. *[微服务中的服务发现](http://www.jianshu.com/p/1bf9a46efe7a)*
5. *[微服务之事件驱动的数据管理](http://www.jianshu.com/p/9a440c5ea1db)*
6. *[选择一种微服务部署策略](http://www.jianshu.com/p/31c2a5a8b764)*
7. *[重构巨石应用到微服务](http://www.jianshu.com/p/29f4d788e3bb)*

这是使用微服务架构构建应用系列的第三篇文章。第一篇文章介绍了微服务架构模式并讨论了使用微服务的优势和劣势 ；第二篇文章介绍了应用的客户端如何通过API网关作为中介实现服务间的通信；在这篇文章中我们将看一看同一系统间的服务如何通信；第四篇文章主要介绍服务发现的问题。

## 介绍
在传统单体应用中，模块间使用编程语言级别的方法或功能彼此调用。然而微服务架构应用本质上是运行在多台机器上的分布式系统，每个服务都是一个进程！因此，下图为我们展示，微服务必须使用进程间通信（IPC）的机制实现交互：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/3912920-f333ee79c1ebd318.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

稍后，我们将看具体的 IPC 技术实现，但首先让我们探讨不同方案设计中的问题。

## 交互风格
当我们为服务选择一种IPC机制的时候，我们首先要考虑服务间如何交互，技术上存在多种 client⇔service 交互风格：它们可以按照两大维度分类：第一维度是服务间交互是一对一还是一对多； 
+ 一对一：每个客户端请求只会被一个服务实例处理。
+ 一对多：每个请求将会被多个服务实例处理

第二个维度是交互是同步模式还是异步模式：
+ 同步：客户端期望来自服务端的及时响应，甚至可能阻塞并等待。
+ 异步：客户端等待响应时不会阻塞，对异步来讲，及时响应并不是必须的。

下列表格展示了两种方式的不同

有下面几种一对一的交互方式：
+ 请求/响应模式： 客户端向服务端发送请求并等待响应，并期望服务端可以及时的返回响应。在一个基于线程的应用中，发出请求的线程可能在等待时阻塞线程的执行。
+ 通知（也就是单向请求）：客户端往服务端发送请求，但并不等待响应返回
+ 请求/异步响应：客户端往一个异步返回响应的服务发送请求。客户端等待式并不会阻塞线程，因为设计时就假设请求不会立即返回（js回调）

有下面几种一对多的交互方式：
+ 发布/订阅模式：客户端发布一个通知消息，消息将会被0或多个感兴趣的服务消费
+ 发布/异步响应：客户端发布一个请求消息，并在一定时间内等待消费消息的服务响应。

每个服务通常会使用多种交互风格的组合：对一些服务来讲，简单的IPC机制可能已经足够了，但另外一些服务可能需要几种IPC机制的组合。下图展示了在**taxi-hailing**应用中，当用户请求行程时，服务是如何交互的：
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/3912920-c46c209a8b21013f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个服务使用了通知、请求/响应、发布/订阅风格的组合。比如，乘客使用智能手机向行程管理服务发送一个接送需求的**通知**，行程管理服务将使用**请求/响应**模式调用乘客服务来验证乘客账号是否为活动状态，然后行程管理服务创建行程并使用**发布/订阅**方式来通知诸如分发器（用来定位空闲司机）等服务。

我们已经讨论了交互风格，那么再来看下如何定义API。

# 定义API
服务API是服务与客户之间的契约。抛开选择哪种IPC机制的选择，使用一些接口定义语言interface definition language (IDL)准确定义服务API是很重要的！.当然，最好考虑使用API优先的方式来定义服务，通过先写接口定义语言来开始开发，并与客户端开发者（服务消费者）一起review你的设计，先对API定义进行迭代，再去实现这些服务。这样做设计的话将会使你构建更加符合客户需求的服务！

后续文章你将会发现，服务定义和你选择哪种IPC机制息息相关，如果你是要消息机制，API就由消息频道和消息类型组成；如果你使用http，API就是由URLs以及request/response格式组成。稍后我们将会讨论更多关于接口定义语言的细节。

## API进化
服务API将会不可避免的随着时间进化，在传统单体应用中，我们可以很直接的去修改服务并更新所有服务的调用者（refactor）。但是在基于微服务架构的应用中，哪怕服务API的其他消费者都是在一个应用中，去更新所有服务也是相当困难的。你通常不能强制让所有的客户端升级来保持和服务端升级维持步调一致，而且，你还可能会增量部署新服务使得新老服务同时运行，寻找一种处理此种情况的策略是很重要的。

你是如何根据更改的大小来处理服务API的变化的呢？一些变化很小，通常可以与之前版本做到向后兼容，比如，你为请求或相应添加了一个属性；对此，设计服务时考虑服务和客户消费者的鲁棒性原则是很有必要的：使用就版本服务API的客户端可以在新版本服务API下正常工作，服务端为客户端缺失的属性提供默认值，客户端自动忽略额外添加的响应属性。最后强调，注意使用IPC机制和定义消息格式使你的API可以简单方便的进化！

当然，有时候我们不得不对API做一些较大的，不再兼容的变化，而我们这时候又不可能强制每个客户端升级，因此我们的服务就要继续支持运行一段时间的老版本API。如果使用http，我们可以在URL里嵌入服务版本，每个服务实例可能同时处理多个版本的服务，当然，你也可以选择为每个版本服务部署单独的服务实例。
