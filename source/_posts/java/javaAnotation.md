---
title: Java 注解
date: 2016-4-25 21:21:55
tags: [java,javaweb,spring]
categories: java
---

## Java 注解

> 经常使用java的注解，一些小的知识点总结下吧

1. 注解是不能继承的
2. 注解Target(ElementType) Retention(RetentionPolicy) Documented Inherited
3. 注解的祖先是java.lang.Annotation接口，可以理解注解是特殊的接口

> 关于Spring几个注解的小记录
         
        @Autowired and @Inject
        Matches by Type
        Restricts by Qualifiers
        Matches by Name
***
        @Resource
        Matches by Name
        Matches by Type
        Restricts by Qualifiers (ignored if match is found by name)

>   Spring的注解是如何起作用的呢，其实道理很简单，runtime的注解可以通过反射在运行的时候获得，那么Spring可以通过Proxy创建一个代理对象，在代理方法中获取注解的信息进行包装。然后代码真正获取的是Spring的代理类，比如@Transactional注解就是这么处理，代理帮我们完成事务等事情的处理。