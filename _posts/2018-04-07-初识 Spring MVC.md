---
layout: post
title: "初识 Spring MVC"
author: "yen"
categories: tech
tags: [documentation,sample]
image: earth.jpg
---
### MVC 的演变过程
**Model 1**  
![Model 1](http://on-img.com/chart_image/5ac82f6ee4b08996549a29f7.png)  
缺点：  
代码不可重用，重复工作太多，效率低  

**Model 2**  
使用三种技术（ JSP、Servlet、JavaBeans ）  
- JSP - 动态网页，显示
- Servlet - 流程控制，分派请求
- JavaBeans - 业务逻辑，数据持久化

![Model 2](http://on-img.com/chart_image/5acb2c41e4b0899654a13dd1.png)  
优点：  
- 消除了 Model 1 缺点  
- 适合多人合作开发大型 Web 项目
- 各司其职，互不干涉
- 利于开发中的分工
- 利于组件重用

缺点：  
Web 项目开发复杂度大

**框架**  
- 降低开发复杂度
- 提高效率
- 减少重复劳动

### About Spring MVC
Spring MVC 框架是一个轻量级 Web 层框架，实现 Model-View-Controller 模式，将数据、业务与展现进行分离。  
Spring MVC 的设计是围绕 DispatcherServlet 展开的， DispatcherServlet 通过可配置的 handlerMappings、view resolution、locale 以及 theme resolution 来处理请求并且转到对应试图。  
* DispatcherServlet 可理解为前端控制器

### Spring MVC 的工作流程
![MVC工作流程](http://on-img.com/chart_image/5acb2f62e4b09bf96ae6f2ae.png)

**代码示例**  
1. 创建DynamicWebProject  
2. 导入包
![MVC-JAR](http://p6ch8daxu.bkt.clouddn.com/18-4-9/88755459.jpg)
3. 在 Web.xml 中配置 SpringMVC    
![](http://p6ch8daxu.bkt.clouddn.com/18-4-9/28821644.jpg)
4. 配置文件(文件名须是：servlet-name+"-servlet".xml)    
5. 配置控制器    
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

  <!-- 提供一个映射处理器 -->
  <bean id="handlerMapping" class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>      

  <!-- 提供自定义控制器 -->
  <bean  name="/helloWorld.do" class="cn.kgc.controllers.HelloWorldController"/>

  <!-- 提供视图解析器 -->
  <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">

  <!-- 前缀 -->
  <property name="prefix" value="/WEB-INF/jsps/"/>
  <!-- 后缀 -->
  <property name="suffix" value=".jsp"/>
</beans>
~~~

处理方法
~~~java
public class HelloWorldController implements Controller{
	/**
	 * 处理请求
	 */
  @Override
  public ModelAndView handleRequest(HttpServletRequest request,
			HttpServletResponse response) throws Exception {
    //提供一个返回的对象（ModelAndView）
    ModelAndView mav =new ModelAndView();
    //添加页面要显示的数据
    mav.addObject("msg","hello world!!");
    //指定逻辑视图名
    mav.setViewName("hello");
    return mav;
  }
}
~~~

JSP页面
~~~java
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Hello World</title>
</head>
<body>
${msg }
</body>
</html>
~~~
