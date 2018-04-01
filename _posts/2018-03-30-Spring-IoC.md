---
layout: post
title: "IoC/DI学习笔记整理"
author: "yen"
categories: tech
tags: [documentation,sample]
image: kenya.jpg
---
## IoC/DI 概念
- IoC：Inversion of Control 控制反转：实例化和依赖变为被动接受
- DI：Dependency Injection 依赖注入： 类依赖IoC创建对象以后的注入到类中
IoC/DI 是 Spring 最基础的组件，是同一个概念的不同角度描述。管理所有的 Java（Bean）类，是一种思想。  

### IoC 容器
- 是一个装bean的容器
- 必须满足两个条件才是IoC容器
> 要有管理的属性   
> IoC和DI缺一不可  

**主从换位思想**
原本由类来主动创建实例，现在由实例来注入到类中


#### 代码演示
创建一个简单的IoC容器显示内容
**创建类**
~~~java
public class B implements API{
  private String msg;
  public B(){
    system.out.println("执行了B的构造方法");
  }
  public void setMsg(String msg){
    this.msg = msg;
  }
  @Override
  public void run(){
    system.out.println("B的信息是："+msg);
  }
}
~~~
**在配置文件中添加<bean>配置**
~~~java
<?xml version="1.0" encoding="UTF-8"?>   
<!--beans:根元素-->  
<beans xmlns="http://www.springframework.org/schema/beans">
<!--Spring面向Bean编程，所有需要它管控的内容，对Spring来说统统都是Bean  -->  
<!--  
  id:bean的唯一标识  
  name:bean的标识   
  class:需要我们管理的内容(即类的全限定名)
-->
<bean id="api" class="com.kgc.ioc.helloword.B">  
  <!--
  property-管理注入属性  
  name-类里需要Spring依赖注入的属性名称   
  value-属性对应的待注入的值
  -->
  <property name="msg" value="Hello Spring!"></property>
</bean>
</beans>
~~~
**测试类**
~~~java
public class Test {
  public static void main(String[] args) {
    // Spring的IOC容器协助我们管理Bean全生命周期-》创建对象
    // 初始化IOC容器【相当于一个bean的工厂】
    ApplicationContext atc = new ClassPathXmlApplicationContext("applicationContext.xml");
    // 面向接口编程
    Api a = (Api)atc.getBean("api");
    System.out.println("是否存在="+atc.containsBean("api2"));
    System.out.println("类型是="+atc.getType("api"));

    //B b = (B)atc.getBean("api");
    a.run();
  }
}
~~~
**注意事项**
1. 待注入的属性必须给予一个 setter（）方法
2. 在使用 Spring 之前，需要对 IoC 容器进行一个初始化
3. 不能使用 new 的对象实例的方法，要交给 Spring 来管理

### IoC容器实例化三种方式
- Resource 方式
- ClassPathResource 方式
- ApplicationContext 方式

### Bean实例化三种方式
1. 构造器实例化
- 即通过在属性文件中增加 factory-method 指定一个静态方法，通过容器调用此静态方法实例化 bean
- 写一个静态工厂方法，供 Spring 调用
2. 在 1 的基础上，添加一个 factory-method
3. 动态工厂方法实例化
- 即通过 factory-bean 中指定另一个 bean，以及通过 factory-method 指定另一个 bean 中一个方法，通过容器调用另一个 bean 的方法来实例化 bean
步骤：
- 写一个工厂类，提供动态的工厂方法调用
- 配置factory-bean和factory-method

### 依赖注入方式
1. Setter 方法注入（通过<property>标签）
2. 构造器注入（通过<constructor-arg>标签）
3. P 命名空间注入(使用属性的形式而不是子元素配置Bean的属性)
> P命名空间语法：
·对于直接量（基本数据类型、字符串）属性：p：属性名=“属性值”
·对于引用Bean的属性：p：属性名-ref =“Bean的id”
·使用前要先要在Spring配置文件中添加P命名空间的声明
  "xmlns:p="http://www.springframework.org/schema/p"


### 注入类型
- 注入直接量
- 引用Bean
- 使用内部Bean，在属性标签里写另一个bean
- 注入null和空字符串值
- 注入集合类型的属性（分别使用<list>，<set>,<map>,<props>标签实现）
- Properties（properties 也是 key-value 的形式，可以使用 map 的方式遍历）

![spring-mind](http://p6ch8daxu.bkt.clouddn.com/18-3-29/82044033.jpg)
