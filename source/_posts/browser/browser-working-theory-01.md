---
title: 浏览器工作原理学习（一）：基础知识
date: 2015-12-31 15:21:55
tags: [coding,javascript，html,browser]
categories: javascript
---

### 关于该博客
前端的坑（摔）已入，虽然之前写了js，也用了Angular这种JS框架（也接触SAP UI5），用过Bootstrap,Sementic UI,KendoUI和其他的框架，但是自己毕竟是前端技术的小菜鸟，该博客就是整理下基础知识，加深下对前段技术的理解。
[参阅How browsers work](http://taligarsiel.com/Projects/howbrowserswork1.htm)

## 1. 浏览器结构（High Level Structure）

> 1. 用户接口 – 包括地址栏，前进后退，书签菜单等窗口上除了网页显示区域以外的部分。
> 2. 浏览器引擎 – 查询与操作渲染引擎的接口。
> 3. 渲染引擎 – 负责显示请求的内容。比如请求到HTML, 它会负责解析HTML 与 CSS 并将结果显示到窗口中。
> 4. 网络 – 用于网络请求, 如HTTP请求。它包括平台无关的接口和各平台独立的实现。
> 5. UI后端 – 绘制基础元件，如组合框与窗口。它提供平台无关的接口，内部使用操作系统的相应实现。
> 6. JavaScript解释器。用于解析执行JavaScript代码。
> 7. 数据存储。这是一个持久层。浏览器需要把所有数据存到硬盘上，如cookies。新的HTML规范 (HTML5) 规定了一个完整（虽然轻量级）的浏览器中的数据库：’web database’。

![浏览器组件](/images/browser/browser-structure.png)

*与其它浏览器不同，chrome使用多个渲染引擎实例，每个Tab一个，每个Tab都是一个独立进程。*

## 2. 渲染引擎（The rendering engine）

&ensp;&ensp;渲染引擎的职责就是渲染，即在浏览器窗口中显示所请求的内容。
&ensp;&ensp;默认情况下，渲染引擎可以显示html、xml文档及图片，它也可以借助插件（一种浏览器扩展）显示其他类型数据，例如使用PDF阅读器插件，可以显示PDF格式,**渲染引擎最主要的用途——显示应用了CSS之后的html及图片。**

### 补充
>Firefox、Chrome和Safari是基于两种渲染引擎构建的，Firefox使用Geoko——Mozilla自主研发的渲染引擎，Safari和Chrome都使用webkit。

### 渲染主流程（The main flow）
>渲染引擎首先通过网络获得所请求文档的内容，通常以8K分块的方式完成。
>渲染引擎在取得内容之后的基本流程：
>解析html以构建dom树 -> 构建render树 -> 布局render树 -> 绘制render树
![渲染引擎流程](/images/browser/browser-render.png)
>渲染引擎开始解析html，并将标签转化为内容树中的dom节点。接着，它解析外部CSS文件及style标签中的样式信息。这些样式信息以及html中的可见性指令将被用来构建另一棵树——render树。

>Render树由一些包含有颜色和大小等属性的矩形组成，它们将被按照正确的顺序显示到屏幕上。
>Render树构建好了之后，将会执行布局过程，它将确定每个节点在屏幕上的确切坐标。再下一步就是绘制，即遍历render树，并使用UI后端层绘制每个节点。

>值得注意的是，这个过程是逐步完成的，为了更好的用户体验，渲染引擎将会尽可能早的将内容呈现到屏幕上，并不会等到所有的html都解析完成之后再去构建和布局render树。它是解析完一部分内容就显示一部分内容，同时，可能还在通过网络下载其余内容。

![webkit render flow](/images/browser/webkit-render-flow.png)
<center>webkit 渲染流程</center>
![geoko render flow](/images/browser/geoko-render-flow.png)
<center>Geoko  渲染流程</center>
>Gecko称可见的格式化元素组成的树为frame树，每个元素都是一个frame，webkit则使用render树这个名词来命名由渲染对象组成的树。Webkit中元素的定位称为布局，而Gecko中称为回流。Webkit称利用dom节点及样式信息去构建render树的过程为attachment，Gecko在html和dom树之间附加了一层，这层称为内容接收器，相当制造dom元素的工厂

## 解析与DOM树构建（Parsing and DOM tree construction）

### 解析（Parsing－general）
>解析一个文档即将其转换为具有一定意义的结构——编码可以理解和使用的东西。解析的结果通常是表达文档结构的节点树，称为解析树或语法树。
>例如，解析“2＋3－1”这个表达式，可能返回这样一棵树
![浏览器组件](/images/browser/math-parse.png)