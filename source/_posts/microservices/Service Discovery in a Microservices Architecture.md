---
title: 微服务中的服务发现
tags: [microservice]
categories: microservice
---

**原文链接:[*Service Discovery in a Microservices Architecture*](https://www.nginx.com/blog/service-discovery-in-a-microservices-architecture/?utm_source=introduction-to-microservices&utm_medium=blog&utm_campaign=Microservices)**

1. *[微服务介绍](http://www.jianshu.com/p/8d2cfa1fa633)*
2. *[构建微服务之使用API网关](http://www.jianshu.com/p/9e90b2a5df7b)*
3. *[构建微服务之:微服务架构中的进程间通信](http://www.jianshu.com/p/9c03081bc0d9)*
4. *微服务中的服务发现(本文)*
5. *[微服务之事件驱动的数据管理](http://www.jianshu.com/p/9a440c5ea1db)*
6. *[选择一种微服务部署策略](http://www.jianshu.com/p/31c2a5a8b764)*
7. *[重构单体应用到微服务](http://www.jianshu.com/p/29f4d788e3bb)*

这是使用微服务构建应用的第四篇文章。第一篇文章介绍了微服务架构模式并讨论了使用微服务的优势和劣势，该系列的第二和第三篇文章 描述了微服务架构中通信的不同方面，本篇文章我们将密切讨论下服务发现的问题。

# 为什么使用服务发现

设想下，我们写了一些通过REST API或者Thrift API调用某个服务的代码，为了发起这个请求，你的代码需要知道服务实例的网络地址(IP 地址和端口号）。在传统运行在物理机器上的应用中，某个服务实例的网络地址一般是静态的，比如，代码可以从偶尔更新的配置文件中读取网络地址。
然而在现在流行的基于云平台的微服务应用中， 有更多如下图所示的困难问题需要去解决：
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/3912920-4742d0f9ff9bdeb9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
服务实例需要动态分配网络地址，而且，一组服务实例可能会因为自动扩展、失败或者升级发生动态变化，因此 你的客户端代码应该使用更加精细的服务发现机制。
有两种主要的服务发现机制：[客户端发现](http://microservices.io/patterns/client-side-discovery.html) 和 [服务端发现](http://microservices.io/patterns/server-side-discovery.html)。让我们先看客户端发现机制。

## 客户端发现模式
当我们使用 [客户端发现](http://microservices.io/patterns/client-side-discovery.html)的时候，客户端负责决定可用服务实例的网络地址并且在集群中对请求负载均衡, 客户端访问**服务登记表**，也就是一个可用服务的数据库，然后客户端使用一种负载均衡算法选择一个可用的服务实例然后发起请求。
下图展示了该结构模式：
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/3912920-76cc7f3f5107c3af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
服务实例的网络地址在服务启动的时候被登记到服务注册中 ，当实例终止服务时从服务注册中移除。服务实例的注册一般是通过心跳机制阶段性的进行刷新。

[Netflix OSS](https://netflix.github.io/) 为客户端发现机制提供了很多优秀的例子。[Netflix Eureka](https://github.com/Netflix/eureka) 实现了服务注册，它通过提供REST API来管理服务实例注册以及可用实例的查询。[Netflix Ribbon](https://github.com/Netflix/ribbon) 是一个与Eureka一起使用并在多个可用实例间对请求负载均衡的IPC客户端。我们将在下面文章深入讨论Eureka。

客户端发现机制有诸多优势和劣势：该模式除了服务注册表之外没有其他的活动部分了，相对来说还是简单直接的，而且，由于客户端知道相关的可用服务实例，那么就可以使用更加智能的，特定于应用的负载均衡机制，比如一致性哈希。一个明显的缺点是它把客户端与服务注册表紧耦合了，你必须为每一种服务客户端的编程语言和框架实现客户端服务发现。