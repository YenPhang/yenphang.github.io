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
  面向切面编程指的是在原来代码的基础上，加上增强的部分，生成一个代理的对象。也就是说，在不修改源代码的情况下，实现对目标类的增强，其原理就是将一个被AOP动态增强的类，通过 Java 动态代理模式生成一个代理类。AOP 主要是利用代理模式的技术来实现的。

### AOP vs OOP
  OOP (Object Oriented Programming) :面对对象编程  
- OOP 是把同一个对象的属性放到一个对象里，把同一类别的类放到一个模块里；
  这里同一的评判标准可以按照业务来划分，也可以通过行为来划分。
- AOP 则是提取相同的功能和方法，针对这样的横切面来归类。
![oop](http://p6ch8daxu.bkt.clouddn.com/18-3-29/18591140.jpg)
![aop](http://p6ch8daxu.bkt.clouddn.com/18-3-29/6490650.jpg)

### AOP的应用
- 打印日志
- 性能监测
- 权限检查
- 最终的目的就是为了解耦，尽量将业务无关的，且同一类方法，功能中需要做的重复动作，提取出来。

### AOP相关概念
Aspect(切面) ：指的是切面的类。也就说独立于业务，需要被插入的部分。  
Joinpoint(连接点) ：顾名思义就是连接切面模块和业务模块的地方；也可以理解为就是业务模块中需要被嵌入代码的地方。  
Pointcut：指要对哪些 Joinpoint 进行拦截的定义。 或理解成切入点的集合。  
Advice(增强处理/处理切面上的逻辑)：指拦截到 Joinpoint 之后所要做的事情就是通知。通知分为前置通知,后置通知,异常通知,最终通知,环绕通知。    
Target:目标对象，被代理对象。  
Weave:织入。

### AspectJ
是注解形式的 AOP。Java 社区里最完整流行的 AOP 框架。
在 AspectJ 注解中, 切面只是一个带有 @Aspect 注解的 Java 类。通知是标注有某种注解的简单的 Java 方法。

### AspectJ 常用语法
在 AOP 注解中可以使用的属性。  
**execution** 用于匹配方法执行的连接点  
~~~java
execution(* yen.*.*(..))
~~~
- 指定在执行 yen 包中任意类的任意方法之前执行方法增强
- 第一个 * 号表示返回值不限  
- 第二个 * 号表示类名不限  
- 第三个 * 号表示方法名不限  
- 小括号中的 .. 表示任意个数、类型不限的形参

### 通知【增强类型】
AspectJ 支持 5 种类型的通知注解:  
@Before: 前置通知, 在方法执行之前执行  
@After: 后置通知, 在方法执行之后执行  
@AfterRunning: 返回通知, 在方法返回结果之后执行  
@AfterThrowing: 异常通知, 在方法抛出异常之后  
@Around: 环绕通知, 围绕着方法执行，即切面要完成的功能  
**通知的执行顺序**
1. 同一种通知类型，按照先后顺序执行（配置文件中的配置顺序，先进后出）
2. 不同通知类型，执行顺序不确定
3. 可以通过org.springframework.core.Odered接口自定义执行顺序

### 通配符
 ![tongpeifu](http://p6ch8daxu.bkt.clouddn.com/18-3-29/13692363.jpg)
 e.g.
 - java.*.Date : 可以表示java.sql.Date,也可以表示java.util.Date  
 - java..* : 表示 java 包中的任意类
 - test(..) : 表示方法名为 test，任意的参数，没有参数，有一个，两个都可以匹配
 - test(String,..) : 表示方法名为 test，至少有一个参数，第一个类型为 String，其他任意
 - test(String ...) : 表示方法名为 test，参数个数不定，但必须都是 String 类型，这里的[...] 不是通配符，而是 java 中不定参数的意思。

### AOP设计实践
- 尽量拦截方法，而不是字段
- 应遵循如下原则：
>在大部分或全部模块都需要使用的通用功能上使用
>预计未来功能扩展的可能比较大的地方

- 应遵循正交原则：先进后出，避免交叉
- 尽量对粗粒度对象使用AOP

![AOP-mind](http://p6ch8daxu.bkt.clouddn.com/18-4-1/6101874.jpg)
