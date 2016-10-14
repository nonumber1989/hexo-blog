---
title: You Don't Know JS - Scope & Closures
date: 2016-10-13 15:21:55
tags: [javascript]
categories: javascript
---


> 最近开始You Don't Know JS系列书籍，必然先从Scope和Closure这部分搞起,作为一个水水的javascript写手，还是希望多深入下的，啃这本书，精简总结下吧。

## Scope
> 几乎所有编程语言的基础功能是将数值存储到变量之中，稍后获取或者修改值。存储值从变量中拿出的功能给予语言存储 **状态state**。我们比较关心的问题是： 这些变量存储在哪里呢？ 最重要的是，程序又是如何在需要的时候找到这些变量的呢？

``定义一组规则将变量存储到某处，规定好如何获取这些变量的值，这些规则集合就是我们的主角之一：Scope``

#### Scope
   + Engine ：负责js的编译和执行
   + Compiler： Engine的好朋友，负责一些脏活累活，比如语法分析词法分析AST的生成
   + Scope：Engine的另一个好朋友，负责收集和维护一个有关变量（包括方法的）查询列表，并定义一个规范集合去规定在代码执行阶段的变量存取方式

    var a = 2
很多人估计和我一样是这么理解这句话的：为一个变量分配内存，并标记为a，之后把值2存储到a中。
然而事实上在js中并非如此：
    + 编译器碰到变量a，Compiler询问`特定`Scope的集合中是否存在变量a，不存在就创建一个，存在的话Compiler就忽略继续
    + Compiler将会generate code为Engine运行代码做准备，运行时刻才会执行a=2(assignment)，code engine 在执行的时候会先查看当前scope的集合中是否存在a，如果有就拿出来，没有的话继续查找(nest scope) 找到a再赋值2给它，找到顶 找不到就报错
    
**也就是说声明和赋值是两个步骤，而且声明在编译阶段，赋值在执行阶段**

#### 赋值表达式
  + LHS
        a= 2 ;//Scope中先查找定义 再执行复制
  + RHS  
        console.log(a);//Scope中查询值

#### Nested Scope
   类比楼梯中找东西 第一层找不到找第二次，逐层上移，直到顶层找不到，报错
+ ReferenceError  找到楼房顶端了还是找不到
+ TypeError  找到了，但是还没赋值

#### Lexical Scope 
![气泡解释scope ](/images/js/scope.png)
lexical scope 是在方法声明的时候被定义的，也就是说变量所在的scope在写代码的时候就已经确认了,变量的查找规则就像上图所示，但是并不是仅仅只有lexical scope,js也存在其他几种方法对scope进行欺骗 cheating lexical (eval and )

##### Lexical Cheating
   + eval
          function foo(str,a){
            eval(str);//cheated
            console.log(a,b);
          }
          var b = 2;
          foo("var b = 3;",1);//1,3
   + 

## Closure

#### Closure