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


### 关于单页面应用SPA 与Angular

>  前段框架火得是一塌糊涂，搞得我这个java方向目标的程序猿硬生生的开始玩前端。 Angular呢肯定是玩SPA的，那么SAP又是什么呢？内部又是怎么玩的？为什么我们选择Angular呢？
   现在我来大致总结下吧。

  *通过与JQuery（当然，人家是个lib）比较，可以发现AngularJS：*

    1. 代码更简洁 （少写好多js，福利！）
    2. 扩展了html的功能 （directive让html强大起来！）
    3. 实现了model和view的双向绑定 （随时在input里输入，随时通过ng-model绑定到name这个model上）


  *其实AngularJS还：*

    1. 分离了JS逻辑和页面渲染 （本来JS又要忙数据交互，又要忙业务逻辑，又要忙页面渲染，现在html干渲染，js专心玩业务吧）
    2. 为JS提供了MVC框架 （后端大神给后端猿猴们的前端框架啊）
    3. 提供了One Page Application流畅的用户体验 （不像以前，每换一个页面都要重新向后台申请，现在是页面无缝平滑切换）
    4. 提供依赖注入机制 （更支持模块化，Spring的既视感）
    5. 便于写单元测试 （行为和界面分离后，行为很方便测试~质量更有保障~）

