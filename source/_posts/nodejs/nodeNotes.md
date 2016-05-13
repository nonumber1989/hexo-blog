---
title: Node 学习小笔记
date: 2016-05-13 15:21:55
tags: [nodejs,javascript]
categories: javascript
---


# Why 
学习nodejs的过程中总会碰到一些小的问题，希望把这些小点滴记录下来

### Node 设计本身
  1.  node中根据相对路径读取文件。
      **Relative paths work, but they are relative to process.cwd(), not the currently executing module.**
      *use **__dirname** to solve this problem*
  2.  node中的文件分隔符
      *一直使用   \    就可以，这个分隔符目前涵盖所有的os*
