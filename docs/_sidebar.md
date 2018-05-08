* [简介](简介)
* [起步](起步)
* [mybatis-config配置](mybatis-config配置)
* [mapper映射文件](mapper映射文件)


时间    时段    信息类型
1.15     1        车辆
2.20     2        基站
2.50     3        车辆
1.40     10       xx2




时段        告警信息
1             学生
1             学生
2             警察
2             老师




select date_format(时间,'%Y') shiduan,信息类型
from 告警信息表
where 时间 in (条件0点 到 12 点)



select count(*),shiduan,信息类型
from  (select date_format(时间,'%Y') shiduan,信息类型
		from 告警信息表
		where 时间 in (条件0点 到 12 点))temp
group by shiduan,信息类型