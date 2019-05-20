接口文件：

```java
   List<User> listSearch(@Param("key") String key,@Param("value") String value);
```

映射xml:

```xml
    select *
    from user where ${key} like concat('%',#{value},'%')
```