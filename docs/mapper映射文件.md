# Mapper XML 文件

MyBatis 的真正强大在于它的映射语句，也是它的魔力所在。相当于之前的dao层实现类

## 基本结构：

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

### mapper
namespace-命名空间，一般和接口名同名，不能使用别名，

按规范书写给人的感觉将非常舒适：namespace写类名，下面的id写方法名

?>示例： 查找单个对象

*java代码*

```java
public interface UserMapper {
	public User selectById(int id);
}
```

*对应的xml*

```xml
<mapper namespace="com.weixin.dao.UserMapper">
  <select id="selectById" resultType="User">
  		select * from user where id=#{id}
  </select>   
</mapper>  
```

!>如果就一个普通参数，上面的id可以换成其他任意字符

?>示例： 查找多个对象放到List中

*java代码*

```java
public interface UserMapper {
	public List<User> selectAll();
}
```

*对应的xml*

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


?>示例： 增加

*java代码*

```java
public boolean insert(User user);
```

*对应的xml*

```xml
<insert id="insert">
	insert into user values(#{id},#{userName},#{gender},#{email})
</insert>
```

!>insert标签中，