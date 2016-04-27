---
title: Java 注解
date: 2016-4-25 21:21:55
tags: [java,javaweb,spring]
categories: java
---

## Java 注解

> 经常使用java的注解，一些小的知识点总结下吧
> 注解是个什么东西呢？我们为什么要使用注解？注解的到底干了哪些事情呢？
> 简单来说：注解就是一种元编程的语法，注意仅仅是一种标志或者一种特殊的语法，注解本身什么都做不了，如果你想要的注解真正的发挥作用，你需要一些工具或者框架帮你生成新的代码(java本身或者Spring等框架)，转换新的代码，并且可以在运行时** (主要是运行时反射实现) **决定你注解的语义和真正的功能。
>  举个例子，JPA是java5定义的OR Mapping的规范，java自身提供了注解比如@Entity，但是这个注解仅仅指明你的当前注解的类可以与数据库的表进行映射，但具体需要Hibernate这些框架来根据注解运行时生成映射文件和SQL查询。

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

>  * Spring的注解是如何起作用的呢，其实道理很简单，runtime的注解可以通过反射在运行的时候获得，那么Spring可以通过Proxy创建一个代理对象，在代理方法中获取注解的信息进行包装。然后代码真正获取的是Spring的代理类，比如@Transactional注解就是这么处理，代理帮我们完成事务等事情的处理。

>  * 关于一个小的问题， 注解比如@Transactional到底是加载接口还是加载具体的实现类和方法上，Spring是建议我们加载具体的方法和类的，该业务是与你具体实现绑定的，而且加在接口，只会在你使用接口代理的时候才会起作用，你使用的具体的实现类并不会真正用到该注解（注解是不能继承的！）
