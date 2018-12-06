!>在mybatis中,mapper接口中的形参名和xml映射文件中的参数名没有联系，但是我们可以通过@Param("") 来定义参数名

##  单个普通参数

    直接使用#{参数名}进行取值，mybatis没做特殊处理，参数名可以随便写。


## 单个参数-参数为map，和bean

```java
   List<User>selectAllByConditionMap(Map map);
   List<User>selectAllByConditionBean(User user);
```
```xml
  <select id="selectAllByCondition" resultType="User">
  	select * from user where id=#{id} and user_name=#{userName}
  </selec
```
## 单个参数-参数为list，数组的形式
传入list,默认的键是 list
传入数组,默认的键是array
建议使用 @Param()指定建名

---

!> 单个参数可以不使用@Param

##  多个参数
    Mybatis会默认构建一个map，键是param1,param2....，或者0,1...值是对应传入的实参,拿参的时候直接写键名就可以，所以可以使用#{param1}，#{param2}取值
    
    `最优方案：我们可以使用注解(@Param("键名"))指定map的键名：`

```java
   List<User>selectAllByCondition(@Param("id")int id,@Param("userName")String userName);
```
```xml
  <select id="selectAllByCondition" resultType="User">
  	select * from user where id=#{id} and user_name=#{userName}
  </select>
```