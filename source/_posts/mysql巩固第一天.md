---
title: MySQL巩固第一天
date: 2021-12-19T04:28:54.016Z
---
1. 数据库操作

   > `create database if not exists school`   --创建数据库
   >
   > `drop database if exists school`    --删除数据库
   >
   > `show databases`-- 查看所有数据库
   >
   > `use school`  --使用数据库

   1. 创建数据表

      ```
      create table [if not exists] `表名`(
      '字段名1' 列类型 [属性][索引][注释] 
      )[表类型][表字符集][注释];
      ```

      * 常用列类型：tinyint、int、double、char、varchar、text、datetime
      * 字段属性：unsigned无符号、zerofull用0填充、auto_increment、null、not null、default
      * `show  create语句` ：查看定义
      * `desc语句`：显示表结构
   2. 数据表的类型 engine

      * 支持的引擎：`show engines;`
      * 常见的MyISAM和InnoDB

        MyISAM：全文检索、表空间较小；节约空间及响应速度；

        InnoDB： 事务处理、数据行绑定、外键约束、较大（约2倍）；安全性、事务处理适合多用户操作；
   3. 数据库字符集 `charset=utf8;`(也可以在my.ini文件配置)
   4. 修改数据库

      ```java
      --修改表名
      alter table tb1 rename as tb2;
      --添加字段
      alter table tb2 add age int(4) not null comment '年龄';
      --修改字段
      alter table tb2 modify age int(5) not null comment '年龄';
      --修改字段名和内容
      alter table tb2 change age age2 int(4) not null comment '年龄';
      --删除字段
      alter table tb2 drop age2;
      --删除表
      drop table if exists tb2;
      ```
2. 数据管理

   1. 外键：从表有主表的字段，从表中保存的值的属性称外键（foreign key）

      作用：保持一致性，完整性；约束外键表中的数据只能使用主表的值或空值

      ```sql
      --建表的时候
      KEY `FK_gradeid` (`gradeid`),
      constraint `FK_gradeid` foreign key (`gradeid`) references `grade` (`gradeid`)

      --或者建表之后
      alter table `student`
      add constraint `FK_gradeid` foreign key (`gradeid`) references `grade` (`gradeid`);
      ```

      有外键无法删除主表，需要删除从表的外键

      ```sql
      alter table `student` drop foreign key `FK_gradeid`;
      alter table `student` drop index `FK_gradeid`;  --也要删除外键自带索引
      drop table grade;
      ```
   2. DML语句：insert、update、delete

      ```sql
      insert into grade(gradename) values("大一"),("大二"),("大三"),("大四")

      update subject set subjectname = "python课" where subjectid = "1"

      delete from subject where subjectid="2"

      -- 这种删除不会清空自增值，会有日志 （InnoDB，自增值断电丢失；MyISAM，不会丢失）
      delete from subject
      --这种删除会清空自增值，不会有日志
      truncate table subject
      ```
   3. DQL语句：select

      ```sql
      --拼接
      select concat("姓名：",studentname) as 新名字 from student 
      --去重
      select distinct studentname from student;
      --查看自增数值
      select @@auto_increment_increment
      --查看mysql版本
      select version()
      --计算
      select 100*3-1 as 计算结果；

      --查询范围   等于 idCard>1500 and idCard<1800
      select studentname from student 
      where idCard between 1500 and 1800
      --查（）有的记录
      select studentname from student where idCard in (1500,1800)


      --模糊查询   %前后模糊 ，_后一个字符模糊
      select studentname from student where studentname like '%小%'
      select studentname from student where studentname like '小_' 

      --判断是否为空
      select studentname from student where address is not null

      --左右表都存在
      select * from student s inner 
      join result r on s.studentno=r.studentno
      --左表优先显示
      select s.studentno,s.studentname,r.score from student s 
      left join result r on s.studentno=r.studentno
      --右表优先显示
      select s.studentno,s.studentname,r.score from student s 
      right join result r on s.studentno=r.studentno

      --关联查询在实际开发中用的多--
      --自连接：课程分类里的一级类和二级类--
      select p.categoryname as 父name,s.categoryname as 子name 
      from category p
      INNER join category s on s.pid = p.id

      --学号、姓名、课程、分数；关联了三张表查分页前六条--
      select s.studentno,s.studentname,sb.subjectname,r.score 
      from student s
      inner join subject sb on s.gradeid = sb.gradeid
      inner join result r 
      on s.studentno=r.studentno and r.subjectid = sb.subjectid
      -- where s.studentname = "小周"  --查具体哪个同学
      order by score desc   --排序
      limit 0,6             --分页

      --子查询，从里到外，可以是 = 和 in （）计算后的返回值--
      select score,studentno from result  where subjectid in  (
      	select subjectid from subject where subjectid = (
      		select studentno from student where studentname = "小a周"
      	)
      )
      ```
3. MySQL函数

   ```sql
   --绝对值
   select abs(-9)
   --向上取值
   select ceiling(4.5)
   --向下取值
   select floor(4.5)
   --随机石
   select rand()
   --返回负数-1 正数1 0
   select sign(-1212)

   --字符长度
   select char_length('gsdfeffdssdf')
   --拼接
   select concat('dsf','sdf')
   --插入
   select insert ('324324324',3,4,"或")
   --小写
   select lower('JJIJO')
   --大写
   select upper('kksaf')
   --左边取5个
   select left('hello world',5)
   --右边取5个
   select right('hello world',5)
   --替换
   select replace('hello world','hello','bye')
   --截取
   select substr('hello world',4,6)
   --反转
   select reverse('就是说就是')

   --替换操作
   select replace(studentname,'网','王') as 新名字
   from student where studentname like '网%'

   --当前日期
   select current_date()
   select curdate()
   --当前日期时间
   select now()
   select localtime()
   select sysdate()
   --当前年月日时分秒
   select year(now()) --年月日时分秒

   --软件版本
   select version()
   --当前用户
   select user()

   --在表没有主键时，count(1)比count(*)快
   --有主键时，count（主键）效率最高，
   --若表格只有一个字段，则count(*)效率较高
   select count(studentname) from student
   select count(*) from student
   select count(1) from student

   --总和
   select sum(score) from result
   --平均
   select avg(score) from result
   --最大
   select max(score) from result
   --最小
   select min(score) from result

   --分组后最高分
   select max(score) from result r
   group by r.subjectid

   --求每个课程名称、最高分、最低分、总分、平均分
   -- 先按课程id分组求最高分，再关联课程表求最大最小总和平均分，再having过滤
   select sb.subjectname,max(score),min(score),sum(score),avg(score) as 平均分 
   from result r
   left join subject sb on sb.subjectid = r.subjectid
   group by r.subjectid
   having 平均分>20

   CREATE TABLE `testmd5` (
     `id` INT(4) NOT NULL,
     `name` VARCHAR(20) NOT NULL,
     `pwd` VARCHAR(50) NOT NULL,
     PRIMARY KEY (`id`)
   ) ENGINE=INNODB DEFAULT CHARSET=utf8

   --md5加密函数
   INSERT INTO testmd5 VALUES(3,'kuangshen',md5(123456)),(2,'qinjiang','456789')
   select * from testmd5
   update testmd5 set pwd =md5(pwd)
   ```