---
title: Angular  原理拾遗
date: 2016-4-1 17:41:55
tags: [angularjs,javascript]
categories: javascript
---

## AngularJs  原理拾遗
本文摘自网络多篇关于AngularJs的优秀博客，侵删


### AngularJs


> 当你用浏览器去访问index.html的时候，浏览器依次做了如下一些事情：
  1. 加载html，然后解析成DOM；
  2. 加载angular.js脚本；
  3. AngularJS等待**DOMContentLoaded**事件的触发；
  4. AngularJS寻找ng-app指令，根据这个指令确定应用程序的边界；
  5. 使用ng-app中指定的模块配置$injector；
  6. 使用injector创建injector创建**$compile**服务和**$rootScope**；
  7. 使用compile服务编译DOM并把它链接到compile服务编译DOM并把它链接到rootScope上；
  8. ng-init指令对scope里面的变量name进行赋值；
  9. 对表达式{{name}}进行替换，于是乎，显示为“Hello World!”

* * *

> 其中第7步,把DOM结构扔给$compile函数处理：
　　1. 找出DOM结构中有变量占位符
　　2. 匹配找出DOM中包含的所有指令引用
　　3. 把指令关联到DOM
　　4. 关联到DOM的多个指令按权重排列**(Priority)**
　　5. 执行指令中的compile函数（改变DOM结构，返回link函数）
　　6. 得到的所有link函数组成一个列表作为$compile函数的返回

