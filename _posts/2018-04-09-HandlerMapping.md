---
layout: post
title: "映射处理器 HandlerMapping"
author: "yen"
categories: tech
tags: [documentation,sample]
image: cards.jpg
---
### 概念
映射处理器  
管理 URL 和 Controller 映射关系的处理器  

### HandlerMapping 类型
- BeanNameUrlHandler  默认的 bean name 映射器
- ControllerClassNameHandlerMapping   类名映射器
- SimpleUrlHandlerMapping  简单 url 处理器映射器

**bean name映射器代码示例**  
~~~java
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx-3.1.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context-3.1.xsd
	http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop-3.1.xsd
	http://www.springframework.org/schema/mvc
	http://www.springframework.org/schema/mvc/spring-mvc-3.1.xsd
	">

  <!-- 配置映射处理器 -->
  <bean id="BeanNameUrlHandlerMapping" class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>  

  <!-- 配置自定义控制器 -->
  <bean  name="/*welcome*.do" class="cn.kgc.controllers.WelcomeController"/>

  <!-- 配置视图解析器 -->
  <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver"/>

  <!-- 前缀 -->
  <property name="prefix" value="/WEB-INF/jsps/"/>
  <!-- 后缀 -->
  <property name="suffix" value=".jsp"/>
</beans>
~~~

处理方法
~~~java
/**
 * 验证 BeanNameUrlHandlerMapping 的控制器
 */
public class WelcomeController extends AbstractController{

  @Override
  public ModelAndView handleRequest(HttpServletRequest request,
			HttpServletResponse response) throws Exception {
    //提供一个返回的对象（ModelAndView）
    ModelAndView mav =new ModelAndView();
    //添加页面要显示的数据
    mav.addObject("msg","BeanNameUrlHandlerMapping");
    //指定逻辑视图名
    mav.setViewName("welcome");
    return mav;
  }
}
~~~

**类名映射器代码示例**
~~~java
<!-- 类名映射器 -->
<bean id="classNameUrlHandlerMapping" class="org.springframework.web.servlet.mvc.support.ControllerClassNameHandlerMapping"/>  

<!-- 验证 classNameUrlHandlerMapping 控制器 -->
<bean class="cn.kgc.controllers.ClassNameController"/>

<!-- 配置视图解析器 -->
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver"/>

<!-- 前缀 -->
<property name="prefix" value="/WEB-INF/jsps/"/>
<!-- 后缀 -->
<property name="suffix" value=".jsp"/>
~~~

处理方法
~~~java
/**
 * 验证 classNameUrlHandlerMapping 的控制器
 */
public class ClassNameController extends AbstractController{

  @Override
  public ModelAndView handleRequest(HttpServletRequest request,
			HttpServletResponse response) throws Exception {
    //提供一个返回的对象（ModelAndView）
    ModelAndView mav =new ModelAndView();
    //添加页面要显示的数据
    mav.addObject("msg","classNameUrlHandlerMapping");
    //指定逻辑视图名
    mav.setViewName("className");
    return mav;
  }
}
~~~

**简单 url 处理器映射器代码示例**
~~~java
<!-- 简单 URL 映射器 1 -->
<bean id="SimpleUrlHandlerMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">  
  <property name="mappings">
    <props>
    <!-- key 就是 url;value 就是控制器名字 -->
      <prop key="/SimpleUrl.do">SimpleUrlController</prop>
    </props>
  </property>
</bean>  

<!--  
<!-- 简单 URL 映射器 2 -->
<bean id="SimpleUrlHandlerMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">  
  <property name="mappings">
    <value>
      simpleValue.do=SimpleUrlController
    </value>
  </property>
</bean>  
-->  

<!--  
<!-- 简单 URL 映射器 3 -->
<bean id="SimpleUrlHandlerMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">  
  <property name="urlMap">
    <map>
      <entry key="/urlMap.do" value-ref="SimpleUrlController"></entry>
    </map>
  </property>
</bean>  
-->  

<!-- 验证 简单 URL 映射器的控制器 -->
<bean id="SimpleUrl" class="cn.kgc.controllers.SimpleUrlController"/>

<!-- 配置视图解析器 -->
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver"/>

<!-- 前缀 -->
<property name="prefix" value="/WEB-INF/jsps/"/>
<!-- 后缀 -->
<property name="suffix" value=".jsp"/>
~~~

处理方法
~~~java
/**
 * 验证 SimpleUrlHandlerMapping 的控制器
 */
public class SimpleUrlController extends AbstractController{

  @Override
  public ModelAndView handleRequest(HttpServletRequest request,
			HttpServletResponse response) throws Exception {
    //提供一个返回的对象（ModelAndView）
    ModelAndView mav =new ModelAndView();
    //添加页面要显示的数据
    mav.addObject("msg","SimpleUrlHandlerMapping");
    //指定逻辑视图名
    mav.setViewName("SimpleUrl");
    return mav;
  }
}
~~~
