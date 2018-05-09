# Mapper XML 文件

> MyBatis 的真正强大在于它的映射语句，也是它的魔力所在。相当于之前的dao层实现类

## 基本结构：

?>namespace为命名空间，一般和接口名同名，不能使用别名，
按规范书写给人的感觉将非常舒适：namespace写类名，下面的id写方法名

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.weixin.dao.UserMapper">
  <select id="selectAll" resultType="user">
    select * from user
  </select>
</mapper>
```

## SQL映射文件还包括如下标签：

- `insert` – 映射插入语句
- `update` – 映射更新语句
- `delete` – 映射删除语句
- `select` – 映射查询语句
- `resultMap` – 是最复杂也是最强大的元素，用来描述如何从数据库结果集中来加载对象
- `sql` – 可被其他语句引用的可重用语句块
- `cache` – 给定命名空间的缓存配置
- `cache-ref` – 其他命名空间缓存配置的引用

> 示例： 查找单个对象

```java
public interface UserMapper {
	public User selectById(int id);
}
```
```xml
<mapper namespace="com.weixin.dao.UserMapper">
  <select id="selectById" resultType="User">
  		select * from user where id=#{id}
  </select>   
</mapper>  
```

!>如果就一个普通参数，上面的id可以换成其他任意字符，多个参数的情况随后讲解

> 示例： 查找多个对象放到List中

```java
public interface UserMapper {
	public List<User> selectAll();
}
```
```xml
<mapper namespace="com.weixin.dao.UserMapper">
   <select id="selectAll" resultType="User">
  	 select * from user
  </select>  
</mapper> 
```

!>总结：在select标签中，`id`,`resultType`为必须属性
当要返回`List<User>`时，resultType的值还应该是User
还有一个重要属性为`resultMap`，随后介绍


> 示例： 增加，删除，修改，(写法基本一致)

```java
public boolean insert(User user);
public boolean deleteById(int id);
public boolean update(User user);
```
或
```java
public int insert(User user);
public int deleteById(int id);
public int update(User user);
```

```xml
<insert id="insert"  useGeneratedKeys="true" keyProperty="id">
	insert into user(user_name,gender,email) values(#{userName},#{gender},#{email})
</insert>
<delete id="deleteById">
  delete from user where id=#{_id}
</delete>
<update id="update">
  update user set user_name=#{userName},gender=#{gender},email=#{email} 
    where id=#{id}
</update>
```


!>总结：增加删除修改标签中，只有id属性是必须的，如果java接口上类型是boolean，那么结果就是true或者false，如果接口上写int，返回结果就是操作行数

## 参数传递

>  单个普通参数：直接使用#{参数名}进行取值，mybatis没做特殊处理，参数名可以随便写。

  ---

>  多个参数：
    Mybatis会默认构建一个map，键是param1,param2....，或者0,1...值是对应传入的实参,拿参的时候直接写键名就可以，所以可以使用#{param1}，#{param2}取值
    
    `最优方案：我们可以使用注解(@Param("键名"))指定map的键名：`

```java
  public List<User>selectAllByCondition(@Param("id")int id,@Param("userName")String userName);
```
```xml
  <select id="selectAllByCondition" resultType="User">
  	select * from user where id=#{id} and user_name=#{userName}
  </select>
```

  ---

> 参数为map，和bean：

```java
  public List<User>selectAllByConditionMap(Map map);
  public List<User>selectAllByConditionBean(User user);
```
```xml
  <select id="selectAllByCondition" resultType="User">
  	select * from user where id=#{id} and user_name=#{userName}
  </selec
```

  ---

> 传入list，数组的形式

## resultMap

有时候普通的resultType满足不了开发需要，不能个性化的定义javabean和列值间的对应关系，我们需要替换成另一个属性resultMap

?> resultMap标签中，`type`和`id`都为必须属性。type为对应的bean类型，id为唯一标示，供`其他select标签`引用。id列对应关系要用`id标签`，其他列都用`result标签`，可以选择需要对应的特殊列进行对应，其他对应还是会遵循默认对应方式

> 示例：先把之前最普通的查询替换为resultMap,可以发现我们不需要开启配置文件的驼峰式配置即可完成映射

```java
public interface UserMapper {
	public User selectById(int id);
}
```
```xml
 <resultMap type="User" id="userResultMap">
  </resultMap>
  <select id="selectById" resultMap="userResultMap">
  		select * from user where id=#{id}
  </select>   
```
下面单独定义对应关系：
```xml
<resultMap type="User" id="userResultMap">
  <id property="id" column="id"/>
  <result property="userName" column="user_name"/>
</resultMap>
```

?> 上面的需求不需要resultMap也可以完成，但是接下来的需求普通的resultType将无法完成

*banji.sql*

```sql
DROP TABLE IF EXISTS `classroom`;
CREATE TABLE `classroom` (
  `c_id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`c_id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of classroom
-- ----------------------------
INSERT INTO `classroom` VALUES ('1', '一班');
INSERT INTO `classroom` VALUES ('2', '二班');
INSERT INTO `classroom` VALUES ('3', '三班');

--修改user表，增加一个外检字段 c_id
```

> 查询用户的同时查询用户所在的班级信息(多对一)

*User.java*

```java
private int id;
private String userName;
private String gender;
private String email;
private ClassRoom classRoom;
```

*UserMapper.java*

```java
public User selectUserClassRoomById(int id);
```
> 写法1 (级联方式)

```xml
  <resultMap type="User" id="userClassRoomResultMap">
  	<id property="id" column="id"/>
  	<result property="userName" column="user_name"/>
  	<result property="gender" column="gender"/>
  	<result property="email" column="email"/>
  	<result property="classRoom.cId" column="c_id"/>
  	<result property="classRoom.name" column="cname"/>
  </resultMap>

  <select id="selectUserClassRoomById" resultMap="userClassRoomResultMap">
  		select user.*,classroom.name cname
  		from user left join classroom
  		on user.c_id=classroom.c_id
  		where id=#{id}
  </select> 
```

> 写法2 (描述单个对象用association)

```xml
 <resultMap type="User" id="userClassRoomResultMap">
  	<id property="id" column="id"/>
  	<result property="userName" column="user_name"/>
  	<result property="gender" column="gender"/>
  	<result property="email" column="email"/>
  	<association property="classRoom" javaType="ClassRoom">
  		<id property="cId" column="c_id"/>
  		<result property="name" column="cname"/>
  	</association>
 </resultMap>

<!-- select标签写法不变  -->
```

> 写法3 (Mybatis特有的分布查询) -- `最优`


