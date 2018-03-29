---
layout: post
title: "AOP学习笔记整理"
author: "yen"
categories: tech
tags: [documentation,sample]
image: ColorfulTromso.jpg
---
## AOP概念：  
  AOP (Aspect Oriented Programming)  
  简而言之就是面向切面编程。它所要实现的目标就是解耦合，提高代码的灵活性和可扩展性。

### AOP vs OOP
  OOP (Object Oriented Programming) :面对对象编程  
- OOP是把同一个对象的属性放到一个对象里，把同一类别的类放到一个模块里；
  这里同一的评判标准可以按照业务来划分，也可以通过行为来划分。
- AOP则是提取相同的功能和方法，针对这样的横切面来归类。
![oop](http://p6ch8daxu.bkt.clouddn.com/18-3-29/18591140.jpg)
![aop](http://p6ch8daxu.bkt.clouddn.com/18-3-29/6490650.jpg)

### AOP的应用
打印日志：独立的日志模块，在业务开发后期嵌入到各个业务模块中，在代码里不存在日志相关代码。  
性能监测：时长需要对生命周期函数，view绘制函数监测其运行时长来监测性能。但如果在代码开发
阶段考虑就要，实现每个生命周期函数，在绘制view的函数中加入时间统计代码，这样不仅会导致冗
余代码还会影响方法本身的性能。  
权限检查：使方法功能单一，剥离权限检查部分。  
- 最终的目的就是为了解耦，尽量将业务无关的，且同一类方法，功能中需要做的重复动作，提取出来。

### AOP相关概念
aspect(切面) ：针对切面的模块。也就说独立于业务，需要被插入的部分。  
joinpoint(连接点) ：顾名思义就是连接切面模块和业务模块的地方；也可以理解为就是业务模块中需要被嵌入代码的地方。  
pointcut：理解起来跟joinpoint差不多意思，只不过它可以添加一些附加条件。
advice(处理切面上的逻辑)：表示被插入的代码，以及插入的时机，如：Before，After，Around等。
Target:被代理对象。  
Weave:织入。

### 通配符
 ![tongpeifu](http://p6ch8daxu.bkt.clouddn.com/18-3-29/13692363.jpg)
 e.g.
 - java.*.Date : 可以表示java.sql.Date,也可以表示java.util.Date  
 - java..* : 表示java包中的任意类
 - test(..) : 表示方法名为test，任意的参数，没有参数，有一个，两个都可以匹配
 - test(String,..) : 表示方法名为test，至少有一个参数，第一个类型为String，其他任意
 - test(String ...) : 表示方法名为test，参数个数不定，但必须都是String类型，这里的[...] 不是通配符，而是java中不定参数的意思。

![AOP简述](http://p6ch8daxu.bkt.clouddn.com/18-3-29/33189985.jpg)
![AOP视频脑图](http://p6ch8daxu.bkt.clouddn.com/18-3-29/74148035.jpg)
![AOP设计实战](http://p6ch8daxu.bkt.clouddn.com/18-3-29/46839058.jpg)
