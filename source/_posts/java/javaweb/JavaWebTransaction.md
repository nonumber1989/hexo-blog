---
title: JavaWeb 事务
date: 2016-4-10 15:21:55
tags: [java,javaweb,transaction,spring]
categories: javaweb
---

## Spring事务

### 问题起源

> 只要写javaweb，只要跟数据库交互，企业级开发不可能避免的一点就是事务管理。
自己捣鼓SpringBoot的时候，采用了JPA（Hibernate），发现有两个事务注解
``javax.transaction.Transactional``
``org.springframework.transaction.annotation.Transactional``
这一下子懵逼了有木有，到底哪个会起作用，到底用哪个？（毛线，spring web工程了，当然是spring包里的那个啊！）

### 扯点基础
>说到java的事务，我们就先扯一点事务分类，因为事务分类和我们的开发模式息息相关。
Java平台支持三种事务模型：

*  `本地事务模型`——事务由DB资源而不是应用容器或框架管理。开发者管理连接而不是事务(如果你直接对着JDBC拼SQL的话，你就是在用本地事务).
*  `编程式事务模型`——开发者从事务管理器中获取事务，之后需要自己编写事务启动、提交、异常及回滚代码（如果你在代码接触事务管理器并自己管理事务,比如下面这一段）。
        public static void main(String[] args) {
        //      Configuration cfg = new Configuration();
        //      cfg.configure();
        //      SessionFactory sf = cfg.buildSessionFactory();
        //      HibernateUtil类中已经帮我们做掉了上面部分
                Session s = null;
                Transaction tx = null;
                try {
                    Class.forName("com.HibernateUtil");
                    s = HibernateUtil.getSession();
                    tx = s.beginTransaction(); //这里开启了事务
                    //事务中所需的一系列数据库操作。
                    tx.commit();
                } catch (HibernateException e) { //如果出现异常，且事务已经开启，则需要回滚。
                    if (tx != null)
                        tx.rollback();
                    throw e;
                } catch (ClassNotFoundException e) {
                    e.printStackTrace();
                } finally { //无论如何都需要关闭Session。
                    if (s != null)
                        s.close();
                }
                //sf.close();
                System.out.println("end");
            }

*  `声明式事务模型`——最常见的模型，又叫做容器管理事务。在这种模型下，容器管理着事务，开发者定义（声明）事务的行为和参数（好吧，大家Spring的@transactional注解还记得吧）
*  这里我先不聊`XA`

### 回到问题起源
> 编程大法之 [copy and paste from stackoverflow](http://stackoverflow.com/questions/26387399/javax-transaction-transactional-vs-org-springframework-transaction-annotation-tr)
> 这两个注解都是要容器帮助我们管理事务，历史上来说Spring比较早，知道JavaEE7，
> Java为了方便嘛，自己也来一套，当然是给EJB配合的。原理上都是一个注解，用来通知容器管理事务而已。如果要使用Javax的transaction，要配置你应用的``javax.ejb.TransactionAttribute`` (我是小菜，ejb也是仅仅接触过一点，自己查看了下Spring Data JPA的实现里，一统江湖的Spring的transaction)

### 聊聊Spring的事务
>看Spring的事务设计策略就是理解的一个过程，我也偷点别人的总结啦

#### 先来看Spring事务需要配置的参数以及意义
|配置参数|参数意义|
|---|---|
|propagation参数|Propagation类型（枚举），默认值为Propogation.REQUIRED，支持的值有REQUIRED、MANDATORY、NESTED、NEVER、NOT_SUPPORTED、REQUIRE_NEW、SUPPORTS。|
|isolation参数|Isolation类型（枚举），默认值为Isolation.DEFAULT，支持的值有DEFAULT、READ_COMMITTED、READ_UNCOMMITTED、REPEATABLE_READ、SERIALIZABLE。|
|timeout参数|int类型，事务的超时时间，默认值为-1，即不会超时。|
|readOnly参数|boolean类型，true表示事务为只读，默认值为false。|
|rollbackFor参数|Class<? extends Throwable>[]类型，默认为空数组。|
|rollbackForClassName参数|String[]类型，默认为空数组。|
|noRollbackFor参数|Class<? extends Throwable>[]类型，默认为空数组。|
|noRollbackForClassName参数|String[]类型，默认为空数组|

#### 再看看propagation参数的意义

| 传播行为  | 意义  |
|---|---|
| PROPAGATION_MANDATORY  | 表示该方法必须运行在一个事务中。如果当前没有事务正在发生，将抛出一个异常  |
|  PROPAGATION_NESTED |  表示如果当前正有一个事务在进行中，则该方法应当运行在一个嵌套式事务中。被嵌套的事务可以独立于封装事务进行提交或回滚。如果封装事务不存在，行为就像PROPAGATION_REQUIRES一样。 | 
|PROPAGATION_NEVER |表示当前的方法不应该在一个事务中运行。如果一个事务正在进行，则会抛出一个异常 |
|PROPAGATION_NOT_SUPPORTED| 表示该方法不应该在一个事务中运行。如果一个现有事务正在进行中，它将在该方法的运行期间被挂起。|
|PROPAGATION_SUPPORTS|表示当前方法不需要事务性上下文，但是如果有一个事务已经在运行的话，它也可以在这个事务里运行。|
|PROPAGATION_REQUIRES_NEW|表示当前方法必须在它自己的事务里运行。一个新的事务将被启动，而且如果有一个现有事务在运行的话，则将在这个方法运行期间被挂起|
|PROPAGATION_REQUIRED|表示当前方法必须在一个事务中运行。如果一个现有事务正在进行中，该方法将在那个事务中运行，否则就要开始一个新事务。(``最常见的事务策略``)|

#### 小小总结下
* @Transactional 表示支持事务的管理，加在类上对整个类起作用，加在方法上对方法起作用，当然方法会覆盖掉类的作用范围
*  这个玩意儿肯定不能加在接口或者抽象类上，注解是没有继承关系的
*  关于配置参数的后四个，都是和回滚相关的，推荐的是不要用rollbackForClassName和noRollbackForClassName，换成另外两个代替就行了，string类型要写全类路径名字，也是蛮累的。`需要注意的是，我们不配置回滚参数，不代表人家就不回滚啊，DefaultTransactionAttribute配置将会生效，具体的行为是，抛掷任何unchecked Exception都会触发回滚，当然包括所有的RuntimeException`


### 今天先到这儿吧，写多写点再填坑，一入java深似海啊
https://m.oschina.net/blog/56904 先放个坑在这儿