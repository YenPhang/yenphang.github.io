---
layout: post
title: "AOP学习笔记整理"
author: "yen"
categories: tech
tags: [documentation,sample]
image: ColorfulTromso.jpg
---
## AOP概念：  
  AOP (Aspect Oriented Programming) 是一种编程规范形式【思想】。从另一角度考虑程序结构，完善面向对象编程思想。
  AOP 简而言之就是面向切面编程。它所要实现的目标就是解耦，提高代码的灵活性和可扩展性。  
  面向切面编程指的是在原来代码的基础上，加上增强的部分，生成一个代理的对象。也就是说，在不修改源代码的情况下，实现对目标类的增强，其原理就是将一个被AOP动态增强的类，通过Java动态代理模式生成一个代理类。AOP 主要是利用代理模式的技术来实现的。

### AOP vs OOP
  OOP (Object Oriented Programming) :面对对象编程  
- OOP是把同一个对象的属性放到一个对象里，把同一类别的类放到一个模块里；
  这里同一的评判标准可以按照业务来划分，也可以通过行为来划分。
- AOP则是提取相同的功能和方法，针对这样的横切面来归类。
![oop](http://p6ch8daxu.bkt.clouddn.com/18-3-29/18591140.jpg)
![aop](http://p6ch8daxu.bkt.clouddn.com/18-3-29/6490650.jpg)

### AOP的应用
打印日志：独立的日志模块，在业务开发后期嵌入到各个业务模块中，在代码里不存在日志相关代码;  
性能监测;  
权限检查：使方法功能单一，剥离权限检查部分。  
- 最终的目的就是为了解耦，尽量将业务无关的，且同一类方法，功能中需要做的重复动作，提取出来。

### AOP相关概念
Aspect(切面) ：指的是切面的类。也就说独立于业务，需要被插入的部分。  
Joinpoint(连接点) ：顾名思义就是连接切面模块和业务模块的地方；也可以理解为就是业务模块中需要被嵌入代码的地方。  
Pointcut：指要对哪些 Joinpoint 进行拦截的定义。 或理解成切入点的集合。
Advice(增强处理/处理切面上的逻辑)：指拦截到 Joinpoint 之后所要做的事情就是通知。通知分为前置通知,后置通知,异常通知,最终通知,环绕通知，如：Before，After，Around 等。  
Target:目标对象，被代理对象。  
Weave:织入。

### 通知【增强类型】
AspectJ 支持 5 种类型的通知注解:
@Before: 前置通知, 在方法执行之前执行
@After: 后置通知, 在方法执行之后执行
@AfterRunning: 返回通知, 在方法返回结果之后执行
@AfterThrowing: 异常通知, 在方法抛出异常之后
@Around: 环绕通知, 围绕着方法执行，即切面要完成的功能

### 通配符
 ![tongpeifu](http://p6ch8daxu.bkt.clouddn.com/18-3-29/13692363.jpg)
 e.g.
 - java.*.Date : 可以表示java.sql.Date,也可以表示java.util.Date  
 - java..* : 表示 java 包中的任意类
 - test(..) : 表示方法名为 test，任意的参数，没有参数，有一个，两个都可以匹配
 - test(String,..) : 表示方法名为 test，至少有一个参数，第一个类型为 String，其他任意
 - test(String ...) : 表示方法名为 test，参数个数不定，但必须都是 String 类型，这里的[...] 不是通配符，而是 java 中不定参数的意思。

### AspectJ
是注解形式的 AOP。Java 社区里最完整最流行的 AOP 框架。
在 AspectJ 注解中, 切面只是一个带有 @Aspect 注解的 Java 类。通知是标注有某种注解的简单的 Java 方法。

### AspectJ语法
1. execution(public * *(..))   任何类的任何返回值的任何方法
2. execution(* set*(..))       任何类的set开头的方法
3. execution(* com.xyz.service.AccountService.*(..))         任何返回值的规定类里面的方法
4. execution(* com.xyz.service...(..))      任何返回值的，规定包或者规定包子包的任何类任何方法

![AOP-mind](http://p6ch8daxu.bkt.clouddn.com/18-4-1/6101874.jpg)
