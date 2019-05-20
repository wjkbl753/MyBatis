频繁使用的语句片段可以单独拿到外边

```xml
<sql id="all_column">
    select id,user_name,age,birthday
</sql>>


<select id="list" resultType="User">
    select
    <include refid="all_column"/>
    from user
</select>
```