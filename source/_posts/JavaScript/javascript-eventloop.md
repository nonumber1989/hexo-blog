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
![javascript event loop](/images/js/js-event-loop.jpg)
	 *主线程空，轮询任务队列，查看时间，触发回调*

### 事件和回调
&ensp;&ensp;"任务队列"是一个事件的队列（也可以理解成消息的队列），IO设备完成一项任务，就在"任务队列"中添加一个事件，表示相关的异步任务可以进入"执行栈"了。主线程读取"任务队列"，就是读取里面有哪些事件。
&ensp;&ensp;"任务队列"中的事件，除了IO设备的事件以外，还包括一些用户产生的事件（比如鼠标点击、页面滚动等等）。只要指定过回调函数，这些事件发生时就会进入"任务队列"，等待主线程读取。
&ensp;&ensp;所谓"回调函数"（callback），就是那些会被主线程挂起来的代码。异步任务必须指定回调函数，当主线程开始执行异步任务，就是执行对应的回调函数。
&ensp;&ensp;"任务队列"是一个先进先出的数据结构，排在前面的事件，优先被主线程读取。主线程的读取过程基本上是自动的，只要执行栈一清空，"任务队列"上第一位的事件就自动进入主线程。但是，由于存在后文提到的"定时器"功能，主线程首先要检查一下执行时间，某些事件只有到了规定的时间，才能返回主线程。

### 事件循环
&ensp;&ensp;主线程从"任务队列"中读取事件，这个过程是循环不断的，所以整个的这种运行机制又称为Event Loop（事件循环）。
为了更好地理解Event Loop，请看下图（转引自Philip Roberts的演讲《Help, I'm stuck in an event-loop》）。
![javascript event loop](/images/js/event-loop.png)
&ensp;&ensp;主线程运行的时候，产生堆（heap）和栈（stack），栈中的代码调用各种外部API，它们在"任务队列"中加入各种事件（click，load，done）。只要栈中的代码执行完毕，主线程就会去读取"任务队列"，依次执行那些事件所对应的回调函数。

### 定时器
&ensp;&ensp;除了放置异步任务的事件，"任务队列"还可以放置定时事件，即指定某些代码在多少时间之后执行。这叫做"定时器"（timer）功能，也就是定时执行的代码。
&ensp;&ensp;定时器功能主要由setTimeout()和setInterval()这两个函数来完成，它们的内部运行机制完全一样，区别在于前者指定的代码是一次性执行，后者则为反复执行。以下主要讨论setTimeout()。
&ensp;&ensp;setTimeout()接受两个参数，第一个是回调函数，第二个是推迟执行的毫秒数。

		console.log(1);
		setTimeout(function(){console.log(2);},1000);
		console.log(3)

&ensp;&ensp;上面代码的执行结果是1，3，2，因为setTimeout()将第二行推迟到1000毫秒之后执行。
&ensp;&ensp;如果将setTimeout()的第二个参数设为0，就表示当前代码执行完（执行栈清空）以后，立即执行（0毫秒间隔）指定的回调函数。


	setTimeout(function(){console.log(1);}, 0);
	console.log(2);

&ensp;&ensp;上面代码的执行结果总是2，1，因为只有在执行完第二行以后，系统才会去执行"任务队列"中的回调函数。
总之，setTimeout(fn,0)的含义是，指定某个任务在主线程最早可得的空闲时间执行，也就是说，尽可能早得执行。它在"任务队列"的尾部添加一个事件，因此要等到同步任务和"任务队列"现有的事件都处理完，才会得到执行。
&ensp;&ensp;HTML5标准规定了setTimeout()的第二个参数的最小值（最短间隔），不得低于4毫秒，如果低于这个值，就会自动增加。在此之前，老版本的浏览器都将最短间隔设为10毫秒。另外，对于那些DOM的变动（尤其是涉及页面重新渲染的部分），通常不会立即执行，而是每16毫秒执行一次。这时使用requestAnimationFrame()的效果要好于setTimeout()。
&ensp;&ensp;需要注意的是，setTimeout()只是将事件插入了"任务队列"，必须等到当前代码（执行栈）执行完，主线程才会去执行它指定的回调函数。要是当前代码耗时很长，有可能要等很久，所以并没有办法保证，回调函数一定会在setTimeout()指定的时间执行。


### 对比nodejs

&ensp;&ensp;Node.js也是单线程的Event Loop，但是它的运行机制不同于浏览器环境。
&ensp;&ensp;请看下面的示意图（作者@BusyRich）。
![nodejs event loop](/images/js/node-event-loop.png)
&ensp;&ensp;根据上图，Node.js的运行机制如下。

	（1）V8引擎解析JavaScript脚本。
	（2）解析后的代码，调用Node API。
	（3）libuv库负责Node API的执行。它将不同的任务分配给不同的线程，形成一个Event Loop（事件循环），以异步的方式将任务的执行结果返回给V8引擎。
	（4）V8引擎再将结果返回给用户。

&ensp;&ensp;除了setTimeout和setInterval这两个方法，Node.js还提供了另外两个与"任务队列"有关的方法：process.nextTick和setImmediate。它们可以帮助我们加深对"任务队列"的理解。
&ensp;&ensp;process.nextTick方法可以在当前"执行栈"的尾部----下一次Event Loop（主线程读取"任务队列"）之前----触发回调函数。也就是说，它指定的任务总是发生在所有异步任务之前。setImmediate方法则是在当前"任务队列"的尾部添加事件，也就是说，它指定的任务总是在下一次Event Loop时执行，这与setTimeout(fn, 0)很像。请看下面的例子（via StackOverflow）

	process.nextTick(function A() {
	  console.log(1);
	  process.nextTick(function B(){console.log(2);});
	});

	setTimeout(function timeout() {
	  console.log('TIMEOUT FIRED');
	}, 0)
	// 1
	// 2
	// TIMEOUT FIRED


&ensp;&ensp;上面代码中，由于process.nextTick方法指定的回调函数，总是在当前"执行栈"的尾部触发，所以不仅函数A比setTimeout指定的回调函数timeout先执行，而且函数B也比timeout先执行。这说明，如果有多个process.nextTick语句（不管它们是否嵌套），将全部在当前"执行栈"执行。
&ensp;&ensp;现在，再看setImmediate。	

	setImmediate(function A() {
	  console.log(1);
	  setImmediate(function B(){console.log(2);});
	});

	setTimeout(function timeout() {
	  console.log('TIMEOUT FIRED');
	}, 0);

&ensp;&ensp;上面代码中，setImmediate与setTimeout(fn,0)各自添加了一个回调函数A和timeout，都是在下一次Event Loop触发。那么，哪个回调函数先执行呢？答案是不确定。运行结果可能是1--TIMEOUT FIRED--2，也可能是TIMEOUT FIRED--1--2。
&ensp;&ensp;令人困惑的是，Node.js文档中称，setImmediate指定的回调函数，总是排在setTimeout前面。实际上，这种情况只发生在递归调用的时候。


	setImmediate(function (){
	  setImmediate(function A() {
	    console.log(1);
	    setImmediate(function B(){console.log(2);});
	  });

	  setTimeout(function timeout() {
	    console.log('TIMEOUT FIRED');
	  }, 0);
	});
	// 1
	// TIMEOUT FIRED
	// 2

&ensp;&ensp;上面代码中，setImmediate和setTimeout被封装在一个setImmediate里面，它的运行结果总是1--TIMEOUT FIRED--2，这时函数A一定在timeout前面触发。至于2排在TIMEOUT FIRED的后面（即函数B在timeout后面触发），是因为setImmediate总是将事件注册到下一轮Event Loop，所以函数A和timeout是在同一轮Loop执行，而函数B在下一轮Loop执行。
&ensp;&ensp;我们由此得到了process.nextTick和setImmediate的一个重要区别：多个process.nextTick语句总是在当前"执行栈"一次执行完，多个setImmediate可能则需要多次loop才能执行完。事实上，这正是Node.js 10.0版添加setImmediate方法的原因，否则像下面这样的递归调用process.nextTick，将会没完没了，主线程根本不会去读取"事件队列"！

	process.nextTick(function foo() {
	  process.nextTick(foo);
	});

&ensp;&ensp;事实上，现在要是你写出递归的process.nextTick，Node.js会抛出一个警告，要求你改成setImmediate。
&ensp;&ensp;另外，由于process.nextTick指定的回调函数是在本次"事件循环"触发，而setImmediate指定的是在下次"事件循环"触发，所以很显然，前者总是比后者发生得早，而且执行效率也高（因为不用检查"任务队列"）。
（完）
### 补充

More info: [Steven.Xu](nonumber1989@gmail.com)
