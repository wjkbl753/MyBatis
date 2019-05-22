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

?> 上面的只是引入resultMap，单表的查询我们一般使用resultType即可,一般数据来自多张表的多表联合查询才会使用到resultMap