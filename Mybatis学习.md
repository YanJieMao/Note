Mybatis学习

---

环境：

       - jdk1.8
       - mysql 5.7
       - maven 3.6.1
       - IDEA

知识基础：

- jdbc
- Mysql
- java基础
- Maven
- Junit

---

[官方文档](https://mybatis.org/mybatis-3/zh/configuration.html#)  : https://mybatis.org/mybatis-3/zh/configuration.html#

[Github地址](https://github.com/mybatis/mybatis-3):https://github.com/mybatis/mybatis-3

[Maven仓库](https://mvnrepository.com/artifact/org.mybatis/mybatis/):https://mvnrepository.com/artifact/org.mybatis/mybatis/

~~~xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.5</version>
</dependency>

~~~



---



## 1.简介

### 1.1 什么是Mybatis

![MyBatis logo](img/mybatis-logo.png)



---

- 一款优秀的**持久层框架**
- 支持定制化sql、存储过程以及高级映射
- 避免了几乎所有的jdbc代码和手动设置参数以及获取结果集
- MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。
- MyBatis 本是[apache](https://baike.baidu.com/item/apache/6265)的一个开源项目[iBatis](https://baike.baidu.com/item/iBatis), 2010年这个项目由apache software foundation 迁移到了google code，并且改名为MyBatis 。
- 2013年11月迁移到Github。

### 1.2 持久化

数据持久化

- 将程序的数据从顺时状态到持久状态的转化过程
- 数据库，io文件持久化

### 1.3持久层

Dao层，Service层，Controller层

- 完成持久化工作的代码块



### 1.4为什么需要Mybatis

- 方便

- 传统的Jdbc太复杂。简化。自动化

- 帮助程序员将数据存储到数据库

- 不用它也可以，用它更方便

- 优点：

  ​     简单易学
  ​     灵活：
  ​     sql和代码的分离，提高了可维护性。
  ​     提供映射标签，支持对象与数据库的orm字段关系映射
  ​     提供对象关系映射标签，支持对象关系组建维护
  ​     提供xml标签，支持编写动态sql。

- 最重要的是用的人多，公司都用这个

  

## 2.第一个程序

### 2.1 搭建环境

搭建数据库

~~~sql

CREATE DATABASE test;
USE test;
CREATE TABLE `user` (
  `id` int(11) NOT NULL DEFAULT '0',
  `name` varchar(30) DEFAULT NULL,
  `pwd` varchar(30) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

INSERT INTO `user` VALUES (1,'郝大胖','1234'),(2,'贾大胖','12345'),(3,'李大胖','123456'),(4,'王大胖','1234567');


~~~

新建项目

1. 新建一个普通的maven项目
2. 删除src
3. 导入依赖

```xml
<!-- 导入依赖-->
    
    <dependencies>
        <!--mysql驱动-->

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.44</version>
        </dependency>

        <!--mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.2</version>
        </dependency>

        <!--junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>


    </dependencies>

```

### 2.2创建一个模块

- 编写mybatis核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=UTC&amp;useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="1997"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="UserMapper.xml"/>
    </mappers>
</configuration>

```

- 编写mybatis工具类

```java
 //sqlsessionFactory --> sqlSesssion
public class MybatisUtils {

    private static SqlSessionFactory sqlSessionFactory;

    static{
        try{
            //获取sqlsessionFactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory  = new SqlSessionFactoryBuilder().build(inputStream);
        }catch (IOException e){
            e.printStackTrace();
        }

    }

    public static SqlSession getSession(){
        return sqlSessionFactory.openSession();

    }

}

```

### 2.3 编写代码

- 实体类

```java
package com.hao.pojo;

public class User {
    private int id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}

```



- Dao接口

```java
package com.hao.dao;

import com.hao.pojo.User;

import java.util.List;

public interface UserMapper {
    List<User> selectUser();
}

```



- 接口实现类由原来的UserDaoImpl转变为一个配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace=绑定一个对应的Dao/Mapper接口-->
<mapper namespace="com.hao.dao.UserMapper">

    <select id="selectUser" resultType="com.hao.pojo.User">
        select*from user
    </select>

</mapper>
```



### 2.4测试

> MapperRegistry是什么?

核心配置文件中注册mappers

- junit测试
- 测试类

```java
public class UserMapperTest {

    @Test
    public void test(){
        //第一布,获取SqlSession对象
        SqlSession Session = MybatisUtils.getSession();
        //执行sql
        UserMapper Mapper = Session.getMapper(UserMapper.class);
        List<User> users = Mapper.selectUser();


        for (User user: users ){
            System.out.println(user);

        }

        //关闭SqlSession
        Session.close();

    }
}
```



> ps: Maven静态资源过滤问题解决方案

```xml

<resources>
   <resource>
       <directory>src/main/java</directory>
       <includes>
           <include>**/*.properties</include>
           <include>**/*.xml</include>
       </includes>
       <filtering>false</filtering>
   </resource>
   <resource>
       <directory>src/main/resources</directory>
       <includes>
           <include>**/*.properties</include>
           <include>**/*.xml</include>
       </includes>
       <filtering>false</filtering>
   </resource>
</resources>

```



##  3.CRUD

> 注意

1. namespqce中的包名要和Dao/mapper 接口的包名一致!
2. mapper 中的id 就是 namespace中的方法名
3. resultType: sql语句执行的返回值!
4. parameterType: 传入的参数类型
5. 增删改需要提交事物



### 3.1 select

- select标签是mybatis中最常用的标签之一

- select语句有很多属性可以详细配置每一条SQL语句

- - SQL语句返回值类型。【完整的类名或者别名】
  - 传入SQL语句的参数类型 。【万能的Map，可以多尝试使用】
  - 命名空间中唯一的标识符
  - 接口中的方法名与映射文件中的SQL语句ID 一一对应
  - id
  - parameterType
  - resultType



> 根据id查找用户

1.在UserMapper中添加对应方法


 ```java
  public interface UserMapper {
      //查询全部用户
      List<User> selectUser();
      //根据id查询用户
      User selectUserById(int id);
  }
 ```

2.在UserMapper.xml中添加Select语句

```xml
<!-- 根据id查找用户-->
    <select id="selectUserById" resultType="com.hao.pojo.User">
        select*from user where id = #{id}
    </select>
```

3.测试类中测试

```java
 @Test
    public void getUserById(){
        SqlSession session = MybatisUtils.getSession();

        UserMapper mapper = session.getMapper(UserMapper.class);

        User user = mapper.selectUserById(1);
        System.out.println(user);

        session.close();
    }
```



### 3.2 insert

一般使用insert标签进行插入操作，它的配置和select标签差不多！

> 增加一个用户

1.在UserMapper中添加对应方法

```java
 //插入一个用户
    int addUser(User user);
```

2.在UserMapper.xml中添加insert语句

```xml
 <!-- 插入用户用户-->
    <insert id="addUser" parameterType="com.hao.pojo.User" >
        insert into user (id,name,pwd) value (#{id},#{name},#{pwd})
    </insert>
```



3.测试类中测试

```java
 //增删改需要提交事物
    @Test
    public void addUser(){
        SqlSession session = MybatisUtils.getSession();

        UserMapper mapper = session.getMapper(UserMapper.class);

        int res = mapper.addUser(new User(5,"旺旺","666"));
        if(res>0){
            System.out.println("插入成功!");
        }

        //提交事务
        session.commit();
        session.close();
    }
```



### 3.3 update

一般使用update标签进行更新操作，它的配置和select标签差不多！

> 修改用户信息

1.在UserMapper中添加对应方法

```java
 //修改一个用户
    int updateUser(User user);
```



2.在UserMapper.xml中添加update语句

```xml
 <!--修改用户信息-->
    <update id="updateUser" parameterType="com.hao.pojo.User">
        update user  set name=#{name},pwd=#{pwd}  where  id = #{id}
    </update>
```



3.测试类中测试

```java

    @Test
    public void updateUser(){
        SqlSession session = MybatisUtils.getSession();

        UserMapper mapper = session.getMapper(UserMapper.class);

        int res = mapper.updateUser(new User(4,"啦啦","555"));
        if(res>0){
            System.out.println("修改成功!");
        }

        //提交事务
        session.commit();
        session.close();

    }
```



### 3.4delete

一般使用delete标签进行更新操作，它的配置和select标签差不多！

> 删除一个用户

1.在UserMapper中添加对应方法

```java
  //删除一个用户
    int deleteUserById(int id);
```



2.在UserMapper.xml中添加delete语句

```xml
 <!--通过id删除用户-->
    <delete id="deleteUserById" parameterType="int">
        delete from user where id = #{id}
    </delete>
```

3.测试类中测试

```java

 @Test
    public void deleteUserById(){
        SqlSession session = MybatisUtils.getSession();

        UserMapper mapper = session.getMapper(UserMapper.class);

        int res = mapper.deleteUserById(5);
        if(res>0){
            System.out.println("删除成功!");
        }

        //提交事务
        session.commit();
        session.close();

    }
```



### 3.5万能Map

> 如果我们的实体类,或者数据库中的表,字段过多,我们应当考虑使用map

使用万能的Map

1、在接口方法中，参数直接传递Map；

```java
User selectUserByNP2(Map<String,Object> map);
```

2、编写sql语句的时候，需要传递参数类型，参数类型为map

```xml
<select id="selectUserByNP2" parameterType="map" resultType="com.kuang.pojo.User">
select * from user where name = #{username} and pwd = #{pwd}
</select>
```

3、在使用方法的时候，Map的 key 为 sql中取的值即可，没有顺序要求！

```java
Map<String, Object> map = new HashMap<String, Object>();
map.put("username","小明");
map.put("pwd","123456");
User user = mapper.selectUserByNP2(map);
```

## 4.配置解析

