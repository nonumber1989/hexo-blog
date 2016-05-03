---
title: Java 事务学习拾遗
date: 2016-5-3 22:21:55
tags: [java,javaweb]
categories: java
---

# java transaction

## 本地事务
> 本地事务其实就是原始的JDBC操作，面对连接的事务。采用JDBC管理事务的时候，其实是针对connection 开始事务 提交或者 回滚。
> 现在我们更多的是采用ORM框架管理事务，比如JPA hibernate什么的，比如JPA，我们可以使用EntityManager来进行数据的增删查改


 但是需要注意 

        public class TradingServiceImpl {
         @PersistenceContext(unitName="trading") EntityManager em;
          public long insertTrade(TradeData trade) throws Exception {
              em.persist(trade);
              return trade.getTradeId();
         }
        }

 > em.persist(trade)这句代码并不会立即向表中插入一条数据，准确说不会立即去操作数据库！！！(也不会抛出异常。) 这是因为基于ORM的框架需要一个事务来触发对象的缓存与数据库之间的同步。通过事务的提交来生成SQL语句，通过数据库连接传送到数据库端进行增删查改的操作。没有事务就不会触发SQL代码生成和保存更改的真实操作，没事务只会终止操作并且不会抛出异常。
 **使用ORM框架就要使用事务，而不是再通过数管理据库连接**来管理事务**

## Spring @Transactional 注解陷阱

 >我们更多的使用Spring容器来托管事物，其中最常见的就是@Transactional 注解了
 但是并是不我们在方法上加上该注解就万事大吉了，必须配置

        <tx:annotation-driven transaction-manager="transactionManager"/>
>transaction-manager 属性保存一个对在 Spring 配置文件中定义的事务管理器 bean 的引用。这段代码告诉 Spring 在应用事务拦截器时使用 @Transaction 注释。如果没有它，就会忽略 @Transactional 注释，**导致代码不会使用任何事务***

### 多问几个问题？

  1. 单独使用 @Transactional 注释时，事务传播模式被设置成什么呢？（REQUIRED）
  2. 只读标志被设置成什么呢？（只读标志设置为 false）
  3. 事务隔离级别的设置是怎样的？（事务隔离级别设置为 READ_COMMITTED，**而且事务不会针对受控异常（checked exception）回滚**）
  4. 事务应何时回滚工作？
http://www.ibm.com/developerworks/cn/java/j-ts1.html
