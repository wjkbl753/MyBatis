首先再创建一张表 classroom

*classroom.sql*

```sql
DROP TABLE IF EXISTS classroom;
CREATE TABLE classroom (
  id int(11) NOT NULL AUTO_INCREMENT,
  name varchar(255) DEFAULT NULL,
  PRIMARY KEY (id)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of classroom
-- ----------------------------
INSERT INTO `classroom` VALUES ('1', '一班');
INSERT INTO `classroom` VALUES ('2', '二班');
INSERT INTO `classroom` VALUES ('3', '三班');

```
user表添加一个外键classroom_id

例:
根据id查询用户所在的班级名:

```xml
<select id="getClassRoomName" resultType="java.lang.String">
    select classroom.name
    from user
    left join classroom on user.classroom_id=classroom.id
    where user.id=#{id}
</select>
```
例:
根据id 查询班级信息

```xml
<select id="getClassRoom" resultType="com.weixin.bean.ClassRoom">
    select classroom.*
    from user
    left join classroom on user.classroom_id=classroom.id
    where user.id=#{id}
</select>
```

> 上面的案例虽然都是多表联合查询，但是数据来自其中一张表，我们可以使用resultType，那如果需要查询的信息来自2张表呢？