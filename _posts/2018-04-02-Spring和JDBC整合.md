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

### Spring 抽象框架
- Core模块 - 核心处理模块
- Datasource模块 - 简化DataSource访问的工具类
- Object模块 - 封装了查询、更新以及存储过程的类组成
- Support模块 - 提供了异常转换和相关的工具类

### JdbcTemplate 概念
- JdbcTemplate 是 core 包的核心类
- 主要作用是完成资源创建与释放等工作
- JdbcTemplate 提供了PreparedStatementCallback回掉函数供使用

### 其他工具类
![工具类](http://p6ch8daxu.bkt.clouddn.com/18-4-5/95623828.jpg)


### 引入 Spring 框架后的实现步骤
1. 创建数据源的 bean
2. 注入数据源
3. 创建 JdbcTemplate
4. 执行 SQL 语句

**代码示例**
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
    //String sql = "insert into user(uid,name,age) " +
    //    "values('"+user.getUid()+"','"+user.getName()+"',"+user.getAge()+")";
    // 创建一个JDBC帮助模板
    //JdbcTemplate jt = new JdbcTemplate(dataSource);
    //jt.execute(sql);

    // 演示PreparedStatementCallback回调函数
    String sql = "insert into user(uid,name,age) values(?,?,?)";
    // 定义一个PreparedStatementCallback的内部类
    PreparedStatementCallback pscb = new PreparedStatementCallback(){
      @Override
      public Object doInPreparedStatement(PreparedStatement pstmt) throws SQLException, DataAccessException {
          pstmt.setString(1, user.getUuid());
          pstmt.setString(2, user.getName());
          pstmt.setInt(3, user.getAge());
      // 执行pstmt
          boolean flag = pstmt.execute();
          return flag;
      }
    }

    // 调用该内部类
    JdbcTemplate jt = new JdbcTemplate(dataSource);
    jt.execute(sql, pscb);
  }

   //查询数据
   @Override
    public List<User> getUsers() {
      List<User> users = new ArrayList<User>();
      String sql = "select * from user";

    // 创建了一个JDBC帮助模板
      JdbcTemplate jtQ = new JdbcTemplate(dataSource);
    // 取得查询结果
      SqlRowSet set = jtQ.queryForRowSet(sql);
    // 根据结果条数进行遍历
      while(set.next()){
    // 将结果组装成对象
        User user = new User();
        user.setUid(set.getString(1));
        user.setName(set.getString(2));
        user.setAge(set.getInt(3));
    // 将对象放入返回集合
        users.add(user);
      }
      return users;
    }
~~~
~~~java
// 演示jdbcDAOSupport
public class UserDAOImpl2 extends JdbcDaoSupport implements UserDAO{

  @Override
  public void addUser(User user) {
    System.out.println("我是实现2");
    String sql = "insert into user(uid,name,age) " + "values('"+user.getUuid()+"','"+user.getName()+"',"+user.getAge()+")";
    getJdbcTemplate().execute(sql);
  }

  @Override
  public List<User> getUsers() {
    return null;
  }
}
~~~

**配置文件**
~~~java
<!-- 数据库源连接池 -->
 <bean id="UserDaoImpl" class="com.kgc.user.dao.UserDaoImpl">
   <property name="dataSource" ref="dataSource"></property>
 </bean>
 <bean id="UserDaoImpl2" class="com.kgc.user.dao.UserDaoImpl2">
   <property name="dataSource" ref="dataSource"></property>
 </bean>

 <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
   <property name="driverClassName" value="com.mysql.jdbc.Driver">
   </property>
	 <property name="url" value="jdbc:mysql://localhost/kgc">
   </property>
	 <property name="username" value="root"></property>
	 <property name="password" value="1234"></property>
 </bean>
~~~

**测试类**
~~~java
public class Test {
  public static void main(String[] args) {
    ApplicationContext atc = new ClassPathXmlApplicationContext("applicationContext.xml");
    //UserDao dao = (UserDao)atc.getBean("UserDaoImpl");
    UserDao dap = (UserDao)atc.getBean("UserDaoImpl2");

    User user = new User();
    user.setUid("001");
    user.setName("0001");
    user.setAge(18);

    dao.addUser(user);
    System.out.println(dao.getUsers());
	}
}
~~~
