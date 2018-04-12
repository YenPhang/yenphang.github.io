---
layout: post
title: "Controller"
author: "yen"
categories: tech
tags: [documentation,sample]
image: mountains.jpg
---
### 概念
控制器是 MVC 中的 C   
- 控制逻辑（前端控制器）
- 功能处理（自定义控制器）  

**功能处理（自定义控制器）的作用**  
1. 收集、验证请求参数并绑定到相应的对象
2. 处理业务获得模型数据
3. 返回视图

### 常见的Controller
#### 一、CommandController(命令控制器)
把请求参数绑定到一个命令对象，并提供验证功能。  
**封装**   
>  构造方法：this.setCommandCIass(User.class)  
  依赖注入：<property name="commandCIass" value="cn.kgc.domain.User"/>    

**验证**  
>  提供一个校验器：cn.kgc.validator.UserVaIidator  
  构造方法：this.setVaIidator(newUserVaIidator())

**配置文件**   
~~~java
<bean name="/myCommand.do" class="cn.kgc.controllers.MyCommandController"/>
~~~

**命令控制器代码演示**
~~~java
public class MyCommandController extends AbstractCommandController{
  //无参构造方法
  public MyCommandController(){
    //把UserModel模型绑定到命令对象command上
    this.setCommandClass(UserModel.class);
    //简单的数据校验器，校验的结果为errors,校验规则是由其实例对象中确定
    this.setValidator(new UserValidator());
  }

  /**
   * 处理方法
	 */
  @Override
  protected ModelAndView handle(HttpServletRequest request,
			HttpServletResponse response, Object command, BindException errors)
			throws Exception {
    //提供一个返回的模型视图对象
    ModelAndView mav=new ModelAndView();
    //组装要在页面上显示的数据
    UserModel user=(UserModel) command;
    mav.addObject("user", user);
    //指定逻辑视图名
    mav.setViewName("command");
    //校验信息org.springframework.validation.BeanPropertyBindingResult: 0 errors
    System.out.println("校验信息"+errors.getMessage());
    return mav;
	}
}
~~~
~~~java
public class UserValidator implements Validator {
  /**
   * 验证数据模型是否绑定到命令对象了
   */
  public boolean supports(Class<?> clazz) {
    return UserModel.class.equals(clazz);
  }

  /**
   * 进行数据验证
   */
  public void validate(Object obj, Errors errors) {
    ValidationUtils.rejectIfEmpty(errors, "userName", "用户名为空了，不OK");

    UserModel user=(UserModel) obj;
    if(null==user.getPassword()||"".equals(user.getPassword())){
      errors.rejectValue("password", "密码为空，异常！");
    }
  }
}
~~~

#### 二、FormController(表单控制器)
良好的表单支持：
- 将数据展示到表单
- 处理表单提交的数据

实现步骤：  
1. 继承SimpleFormController类
2. 重写要实现的方法  
 (1) formBackingObject（）：通过commandName表单展示数据  
 (2) referenceData（）：展示表单时需要的数据  
 (3) doSubmitAction()：处理表单提交请求

~~~java
//进行表单数据回显
  @Override
  protected Object formBackingObject(HttpServletRequest request)
			throws Exception {
    //组装页面要展示的数据
    UserModel user=new UserModel();
    user.setUserName("张三");
    return user;
	}

  //提供表单需要的其他数据
  @Override
  protected Map referenceData(HttpServletRequest request) throws Exception {
      //提供返回对象
      Map<String,String> resultMap=new HashMap<String, String>();
      resultMap.put("age", "20");
    return resultMap;
  }

  //处理表单提交请求
  @Override
  protected void doSubmitAction(Object command) throws Exception {
    UserModel user=(UserModel) command;
    System.out.println(user.toString());
  }
~~~

3. 构造方法注册命令类和命令名称
~~~java
 publicMyFormControlIer(){
    setCommandClass(User.class);//命令对象实现类
		setCommandName("user"）；//命令对象的名字
 }
~~~

4. 配置文件		
~~~java
<bean name="/myform.do" class="cn.kgc.controllers.MyFormControIIer">
  <property name="formView" value="form"/>
	<propert yname="successView"  value="success"/>
</bean>
~~~

#### 三、MultiActionController(多功能控制器)
- 支持在一个控制器里面添加多个功能处理方法
- 可以通过方法名解析器或委托对象模式将多个请求的处理方法放置到一个控制器里
- 默认使用InternalPathMethodNameResolver解析URL和方法名的映射

实现步骤：  
1. 继承MultiActionController
2. 提供增删改查的方法（模拟）
3. 配置文件
~~~java
<bean name="/user/*" class="cn.kgc.controllers.MyMultiController"/>
~~~
**多功能控制器代码演示**
~~~java
public class MyMultiController extends MultiActionController {
  //模拟一个数据库
  private Map<String,Object> dataMap=new HashMap<String, Object>();

  //增加
  public ModelAndView add(HttpServletRequest request,
			HttpServletResponse response, UserModel user) {
    // 提供一个返回对象
    ModelAndView mav = new ModelAndView();
    // 如果是get请求，返回到添加页面
    if ("GET".equals(request.getMethod())) {
      mav.setViewName("add");
      return mav;
    }
    if ("POST".equals(request.getMethod())) {
      // 如果是post请求，添加数据到dataMap
      dataMap.put(user.getUserName(), user);
      return list(request,response,user);
    }
    return null;
  }

  //删除
  public ModelAndView delete(HttpServletRequest request,
			HttpServletResponse response, UserModel user){
    //删除数据
    dataMap.remove(user.getUserName());
    return list(request,response,user);
  }

  //修改
  public ModelAndView update(HttpServletRequest request,
			HttpServletResponse response, UserModel user){
    ModelAndView mav=new ModelAndView();
    //如果是get请求 那么把要修改的数据显示到修改页面（获取）
    if("GET".equals(request.getMethod())){
    	//把要修改的user对象拿出来
      UserModel targetUser = (UserModel) dataMap.get(user.getUserName());
      mav.addObject("targetUser", targetUser);
      mav.setViewName("update");
      return mav;
    }
    //如果是post请求，将目标数据进行修改，然后调用查询方法进行展示
    if("POST".equals(request.getMethod())){
      UserModel updateUser = (UserModel) dataMap.get(user.getUserName());
      updateUser.setPassword(user.getPassword());
      //将更新后的数据存到map中
      dataMap.put(user.getUserName(), updateUser);
      return list(request,response,user);
    }
    return null;
  }

  //查询
  public ModelAndView list(HttpServletRequest request,
			HttpServletResponse response,UserModel user){
    // 提供一个返回对象
    ModelAndView mav = new ModelAndView();
    mav.addObject("dataMap", dataMap);
    mav.setViewName("list");
		return mav;
  }
}
~~~
