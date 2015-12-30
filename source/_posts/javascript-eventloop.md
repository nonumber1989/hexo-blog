---
title: JavaScript 运行机制Event Loop理解
date: 2015-12-30 15:21:55
tags: [coding,javascript]
categories: javascript
---


## JavaScript 运行机制Event Loop理解
本文学习自阮一峰老师的文章[JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)

### JavaScript 是单线程

&ensp;&ensp;JavaScript 是单线程的，是学习javaScript必先接受的前提，尤其是我这样的java为主业的小码农。
&ensp;&ensp;单线程模型简单，现在单线程反而是特色，nodejs不就是看上这点么，解放java多线程的火坑啊。虽然html5提供了web worker标准，允许JavaScript创建多个线程，但是子线程完全受主线程的控制，**而且worker线程不可以操作dom**。所以这个标准并没有改变JavaScript单线程的本质


### 任务队列
&ensp;&ensp;单线程就意味着，所有任务需要排队，前一个任务结束，才会执行后一个任务。如果前一个任务耗时很长，后一个任务就不得不一直等着。这就是阻塞队列了，一般阻塞队列主要是IO·网络之类（AJAX请求）的引起的，此时CUP这么宝贵的资源只能可惜的等待，等待好痛苦啊！
&ensp;&ensp;JavaScript设计ZEN:这时主线程完全可以不管IO设备，挂起处于等待中的任务，先运行排在后面的任务。等到IO设备返回了结果，再回过头，把挂起的任务继续执行下去。
&ensp;&ensp;现在任务分两类：一种是**同步任务（synchronous）**，另一种是**异步任务（asynchronous）**。同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；异步任务指的是，不进入主线程、而进入**"任务队列"（task queue）**的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。
	1. 同步任务在主线程执行，形成执行栈（execution context stack）。
	2. 主线程之外存在"任务队列"（task queue），异步任务有了运行结果，就在"任务队列"之中放置一个事件。
	3. "执行栈"中所有同步任务执行完毕，系统就会读取"任务队列"，看有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
	4. 主线程重复上面的第三步。

### 事件和回调

### 事件循环

### 定时器

### 对比nodejs

### 补充

More info: [Steven.Xu](nonumber1989@gmail.com)
