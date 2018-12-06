## 一级缓存

MyBatis默认开启一级缓存：

在一个SqlSession中生效：
在一个SqlSession中连续执行两次查询，观察Sql，只查询了一次

一级缓存失效情况：
- 再开启一个SqlSession
- 执行增删改

## 二级缓存(不建议使用)

使用场景，第一个用户查询了一次，第二个用户查询相同的语句，可以直接拿缓存：

开启方式：
在想开启的namespace 标签内添加一行：
`<cache/>`


```java
    InputStream resource = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(resource);

    SqlSession sqlSession = sessionFactory.openSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = mapper.selectOne(1);
    System.out.println(user);
    sqlSession.close();

    SqlSession sqlSession2 = sessionFactory.openSession();
    UserMapper mapper2 = sqlSession2.getMapper(UserMapper.class);
    User user2 = mapper2.selectOne(1);
    System.out.println(user2);
    sqlSession2.close();
```

二级缓存的范围是namespace，在一个namespace中出现insert,delete,update后，二级缓存会刷新
二级缓存缺陷:
例如在UserMapper.xml中有大多数针对user表的操作。但是在一个XXXMapper.xml中，还有针对user单表的操作

缺陷测试：
```java
    InputStream resource = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(resource);

    //查询UserMapper中的数据
    SqlSession sqlSession = sessionFactory.openSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = mapper.selectOne(1);
    System.out.println(user);
    sqlSession.close();
    
    //在ClassRoomMapper中修改user数据
    User user5 = new User();
    user5.setId(1);
    user5.setUserName("ss2");
    SqlSession sqlSession3 = sessionFactory.openSession();
    ClassRoomMapper mapper3 = sqlSession3.getMapper(ClassRoomMapper.class);
    mapper3.update2(user5);
    sqlSession3.commit();

    //再次查询UserMapper中的数据，因为UserMapper的空间内没有增删改，所以缓存还在，所以查出脏数据
    SqlSession sqlSession2 = sessionFactory.openSession();
    UserMapper mapper2 = sqlSession2.getMapper(UserMapper.class);
    User user2 = mapper2.selectOne(1);
    System.out.println(user2);
    sqlSession2.close();
```