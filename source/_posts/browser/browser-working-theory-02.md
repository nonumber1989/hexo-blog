---
title: 浏览器工作原理学习（二）：补充篇
date: 2015-12-31 17:21:55
tags: [coding,javascript，html,browser]
categories: javascript
---

### 上一篇基本就是copy-learning 过程，这一篇找个总结性质的，直接拿干货！

## 浏览器工作大流程
**1. 浏览器解析三样东西**
> +  HTML/SVG/XHTML，事实上，Webkit有三个C++的类对应这三类文档。解析这三种文件会产生一个DOM Tree。
> +  CSS，解析CSS会产生CSS规则树。
> +  Javascript，脚本，主要是通过DOM API和CSSOM API来操作DOM Tree和CSS Rule Tree.

**2. 解析完成后，浏览器引擎会通过DOM Tree 和 CSS Rule Tree 来构造 Rendering Tree**
> + Rendering Tree 渲染树并不等同于DOM树，因为一些像Header或display:none的东西就没必要放在渲染树中了。
> + CSS 的 Rule Tree主要是为了完成匹配并把CSS Rule附加上Rendering Tree上的每个Element。也就是DOM结点。也就是所谓的Frame。**CSS匹配DOM Tree主要是从右到左解析CSS的Selector**
> + 然后，计算每个Frame（也就是每个Element）的位置，这又叫layout和reflow过程。

**3. 后通过调用操作系统Native GUI的API绘制。**

## 渲染

**渲染的流程基本为四个步骤**

> + 计算CSS样式
> + 构建Render Tree
> + Layout – 定位坐标和大小，是否换行，各种position, overflow, z-index属性 ……
> + 正式开画

这里重要要说两个概念，一个是Reflow，另一个是Repaint。这两个不是一回事。

	Repaint——屏幕的一部分要重画，比如某个CSS的背景色变了。但是元素的几何尺寸没有变。
	Reflow——意味着元件的几何尺寸变了，我们需要重新验证并计算Render Tree。是Render Tree的一部分或全部发生了变化。这就是Reflow，或是Layout。

**（HTML使用的是flow based layout，也就是流式布局，所以，如果某元件的几何尺寸发生了变化，需要重新布局，也就叫reflow）**reflow 会从<html>这个root frame开始递归往下，依次计算所有的结点几何尺寸和位置，在reflow过程中，可能会增加一些frame，比如一个文本字符串必需被包装起来。Reflow的成本比Repaint的成本高得多的多。DOM Tree里的每个结点都会有reflow方法，一个结点的reflow很有可能导致子结点，甚至父点以及同级结点的reflow

所以，下面这些动作有很大可能会是成本比较高的。

> + 当你增加、删除、修改DOM结点时，会导致Reflow或Repaint
> + 当你移动DOM的位置，或是搞个动画的时候。
> + 当你修改CSS样式的时候。
> + 当你Resize窗口的时候（移动端没有这个问题），或是滚动的时候。
> + 当你修改网页的默认字体时。

reflow有如下的几个原因：

> + Initial。网页初始化的时候。
> + Incremental。一些Javascript在操作DOM Tree时。
> + Resize。其些元件的尺寸变了。
> + StyleChange。如果CSS的属性发生变化了。
> + Dirty。几个Incremental的reflow发生在同一个frame的子树上。

**一般来说，浏览器会把这样的操作积攒一批，然后做一次reflow，这又叫异步reflow或增量异步reflow**

