## 1.准备数据库

新建数据库 `mybatis`

新建数据表 `user`

*user.sql*

```sql
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_name` varchar(255) DEFAULT NULL,
  `gender` char(1) DEFAULT NULL,
  `email` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of user
-- ----------------------------
INSERT INTO `user` VALUES ('1', '小明', '0', 'xiaoming@163.com');
INSERT INTO `user` VALUES ('2', '小红', '1', 'xiaohong@163.com');
INSERT INTO `user` VALUES ('3', '小花', '1', 'xiaohua@163.com');
```

## 2.新建工程并导包

新建一个javase工程
```xml
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.4.1</version>
    </dependency>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.14</version>
    </dependency>
```
<!-- 将 [Log4j.xml或log4j.properties](jar/log4j.zip ':ignore') 置与 `classpath` 中 -->

---

## 3.创建Mybatis总配置文件 `mybatis-config.xml`

*mybatis-config.xml(文件名可变)*

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <!--<typeAliases>
      <package name="com.woyuno.vo"/>
  </typeAliases> -->
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/database?useUnicode=true&amp;characterEncoding=UTF-8"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
 <mappers>
    <mapper resource="mapping/UserMapper.xml"/>
  </mappers> 
</configuration>
```

---

## 4.新建User实体类及数据访问层接口

*User.java*

```java
public class User{
  private int id;
  //对应数据库字段 user_name
  private String userName;
  private String gender;
  private String email;
}
```
*UserMapper.java*

```java
interface UserMapper {
	   List<User> selectAll();
}
```
!>mybatis新版本支持`面向接口编程`，程序员`不需要`再写接口的实现类，转而用配置文件代替：

## 5.创建UserMapper的映射文件 UserMapper.xml：

*UserMapper.xml*  (`此文件名可以任意，但是一般和接口名同名,放在classpath目录下的mapper或者mapping文件夹`)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace必须是接口名的全类名 -->
<mapper namespace="com.woyuno.mapper.UserMapper">
<!-- id属性为必须属性，值为接口中的方法名， 需要resultType指明返回类型(默认需要全类名，
虽然可以通过别名设置只写类名，但是不建议)，resultType指返回的类型，返回User，List<User> 返回的类型
都应该是com.woyuno.bean.User-->
  <select id="selectAll" resultType="com.woyuno.bean.User">
    select * from user
  </select>
</mapper>
```

## 6.测试

```java
public class UserTest {
	@Test
	public void selectAll() throws IOException{
    //加载mybatis-config.xml到输入流
		InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    //通过输入流构建SqlSessionFactory对象
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    //通过SqlSessionFactory对象得到一个SqlSession对象，每个数据库操作都依靠SqlSession
		SqlSession sqlSession = sqlSessionFactory.openSession();
    //通过SqlSession对象得到一个mapper的实现对象
		UserMapper mapper = sqlSession.getMapper(UserMapper.class);
		List<user> users = mapper.selectAll();
		System.out.println(users);
	}
}
```

## SQL语句打印
mybatis内部支持SQL语句打印
在主配置文件中：

```xml
<configuration>
    <settings>
        <!-- 打印查询语句 -->
        <setting name="logImpl" value="STDOUT_LOGGING" />
    </settings>
</configuration>
```

本次我们先使用上边的打印方式，mybatis还支持各种日志框架，一般是要和spring整合使用
* SLF4J
* Apache Commons Logging
* Log4j 2
* Log4j
* JDK logging


