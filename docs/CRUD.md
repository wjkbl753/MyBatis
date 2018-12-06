## 示例： 查找单个对象

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

!>如果就一个普通参数，上面的id可以换成其他`任意字符`，多个参数的情况随后讲解

## 示例： 查找多个对象放到List中

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

!>总结：在select标签中，`id`为必须属性,`resultType`和`resultMap`必须二选一
当要返回`User`时，时，`resultType`的值应该是com.weixin.bean.User 当要返回`List<User>`时，resultType的值还应该是com.weiixn.bean.User
还有一个重要属性为`resultMap`，随后介绍


## 示例： 增加，删除，修改，(写法基本一致)

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
```java
//mybatis默认开启事务，执行完毕执行需要提交事务
session.commit();
```

!>主键自增操作中，如果新增加的id想回填到user对象，需要加2个属性   useGeneratedKeys="true" keyProperty="id"

!>总结：增加删除修改标签中，只有id属性是必须的，如果java接口上类型是boolean，那么结果就是true或者false，如果接口上写int，返回结果就是操作行数