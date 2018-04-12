---
layout: post
title: "注解驱动 Annotation"
author: "yen"
categories: tech
tags: [documentation,sample]
image: forest.jpg
---
### 常用注解
- @Controller : 用于标识是处理器类
- @RequestMapping : 请求到处理器功能方法的映射规则（指定访问路径），既可用在类上，也可用在方法上

搭建项目步骤：
1. 引包
> com.springsource.javax.annotation-1.0.0.jar

2. 配置文件
~~~java
<!--支持注解-->
<context:annotation-config/>
<!--自动扫描包路经-->
<context:component-scan base-package="cn.kgc"/>
~~~

**代码演示**  
~~~java
<!-- 更改DispatcherServlet路径 -->
<!-- springMVC 核心servlet -->
  <servlet>
    <servlet-name>golfing</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:resource/springMVC.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>

  <servlet-mapping>
    <servlet-name>golfing</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
~~~

~~~java
@Controller("helloController")
@RequestMapping(value="/hello") //请求路径：localhost:8080/项目名/hello
public class MyAnotationController {
  //打印hell kgc
  @RequestMapping(value="/sayHello.do")//请求路径：localhost:8080/项目名/hello/sayHello.do
  public ModelAndView sayHello(ModelAndView mav){
    //组装页面要显示的数据
    mav.addObject("msg", "hell kgc");
    //指定逻辑视图名
    mav.setViewName("hello");
    return mav;
  }

  @RequestMapping(value="/helloKgc.do")//请求路径：localhost:8080/项目名/hello/helloKgc.do
  public String helloKgc(Model model){
    //model 用来装页面要显示的数据
    model.addAttribute("msg", "你好，课工场");
    //返回值如果是字符串的话就是逻辑视图名
    return "kgc";
  }
}
~~~
