---
title: Java Exception
date: 2016-5-24 18:38:55
tags: [java,javaweb]
categories: java
---

# java exception

### Basic Info
 ![java exception architecture](/images/java/java-exception.jpg)
### Brief Desc
 关于java的异常处理，可谓是两派之争，checked Vs. unChecked 孰优孰劣各执一词由来已久。
 但是不得不承认的一点是，unChecked似乎略显上风了。
### Checked & UnChecked exception
 1. Error or RuntimeException  称为unchecked exception
   runtimeException其实很容易迷惑人，因为所有的exception都出现的运行时，纵然checked exception也只是让你编译期间做好处理准备。
 2. 剩余的自然就是checked exception
 3. 处理规范：一个方法必须声明可能抛出的受检异常，非受检异常要么不可能控制**Error**，要么应该避免发生。 当然 编译器会提示我们做好对受检异常的处理。不然编译都过不去的

### Tips
  1. 子类覆盖超类的方法，子类的异常只能更具体或者没有异常抛出
  2. 如果超类没有checked异常抛出，那么子类也不能有checked异常抛出
  
