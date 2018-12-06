## 准备数据库

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

## 新建工程并导包

新建一个javase工程
```xml
  <!--
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
     -->
    <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.4.1</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.14</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-log4j12 -->
   <!-- <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>1.7.25</version>
      <scope>test</scope>
    </dependency>-->

    <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
  <!-- <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.0</version>
      <scope>provided</scope>
  </dependency> -->

```
将 [Log4j.xml或log4j.properties](jar/log4j.zip ':ignore') 置与 `classpath` 中

---

## 创建Mybatis总配置文件 `mybatis-config.xml`

*mybatis-config.xml(文件名可变)*

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <!--<typeAliases>
      <package name="com.weixin.vo"/>
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
<!-- <mappers>
    <mapper resource="UserMapper.xml"/>
  </mappers> -->
</configuration>
```

---

## 新建User实体类及dao文件

*User.java*

```java
public class User{
  private int id;
  private String userName;
  private String gender;
  private String email;
}
```

!> 上面实体类中`userName`对应数据表中的`user_name`字段

*UserMapper.java*

```java
public interface UserMapper {
	   List<User> selectAll();
}
```
!>mybatis新版本支持`面向接口编程`，程序员`不需要`再写接口的实现类，转而用配置文件代替：

## 创建UserMapper的映射文件 UserMapper.xml：

*UserMapper.xml*  (`此文件需要和dao文件同名`)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.weixin.mapper.UserMapper">
  <select id="selectAll" resultType="com.weixin.bean.User">
    select * from user
  </select>
</mapper>
```

## 测试

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
