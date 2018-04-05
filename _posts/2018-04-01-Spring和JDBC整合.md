---
layout: post
title: "Spring 和 JDBC 整合"
author: "yen"
categories: tech
tags: [documentation,sample]
image: spools.jpg
---
### Spring 对数据层（持久化技术）的支持
- 数据层：指 DAO 层，主要解决与数据库的部分
- 提供了一致的异常
- 一致的DAO抽象类
**JDBC 对应的模板类：org.springframework.jdbc.core.JdbcTemplate**  

### 引入 Spring 框架后的实现步骤
1. 创建数据源的 bean
2. 注入数据源
3. 创建 JdbcTemplate
4. 执行 SQL 语句

#### 代码示例(添加数据)
**创建实体类和接口**
~~~java
public class User {
	private String uid;
	private String name;
	private int age;

	public String getUid() {
		return uid;
	}
	public void setUid(String uid) {
		this.uid = uid;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}

	@Override
	public String toString() {
		return "User [uid=" + uid + ", name=" + name + ", age=" + age+ "]";
	}
}
~~~
~~~java
public interface UserDao {
	public void addUser(User user);
	public List<User> getUsers();

}
~~~

**使用 JdbcTemplate 实现与数据库的交互（ CRUD ）**
~~~java
public class UserDaoImpl implements UserDao{
	// 获取数据源
	private DataSource dataSource;
	public void setDataSource(DataSource dataSource) {
		this.dataSource = dataSource;
	}

	@Override
	public void addUser(final User user) {
		// 直接使用JdbcTemplate
    String sql = "insert into user(uid,name,age) " +
                 "values('"+user.getUid()+"','"+user.getName()+"',"+user.getAge()+")";
    // 创建一个JDBC帮助模板
    JdbcTemplate jt = new JdbcTemplate(dataSource);
    jt.execute(sql);
~~~

**配置文件**
~~~java
<!-- 数据库源连接池 -->
  <bean id="UserDaoImpl" class="com.kgc.user.dao.UserDaoImpl">
		<property name="dataSource" ref="dataSource"></property>
	</bean>

	<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
		<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
		<property name="url" value="jdbc:mysql://localhost/kgc"></property>
		<property name="username" value="root"></property>
		<property name="password" value="1234"></property>
	</bean>
~~~

**测试类**
public class Test {
	public static void main(String[] args) {
		ApplicationContext atc = new ClassPathXmlApplicationContext("applicationContext.xml");

		UserDao dao = (UserDao)atc.getBean("UserDaoImpl");

		User user = new User();
		user.setUid("001");
		user.setName("0001");
		user.setAge(18);

		dao.addUser(user);
    //System.out.println(dao.getUsers());
	}
}
