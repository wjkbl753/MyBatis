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
根据用户id查询这个用户所在班的班级信息

```xml
<select id="selectByUserId" resultType="com.woyuno.bean.Classroom">
	select classroom.*
	from classroom
	left join user
	on classroom.id = user.classroom_id
	where user.id=#{id}
</select>
```

例:
差询班级名是'二班'的班级的所有学生的信息

```xml
<select id="selectByClassroomName" resultType="com.woyuno.bean.User">
	select user.*
	from user
	left join classroom
	on user.classroom_id=classroom.id
	where classroom.name=#{name}
</select>
```

> 上面的案例虽然都是多表联合查询，但是数据来自其中一张表，我们可以使用resultType，那如果需要查询的信息来自2张表呢？