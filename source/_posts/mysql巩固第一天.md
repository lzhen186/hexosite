---
title: MySQL巩固第一天
date: 2021-12-19T04:28:54.016Z
---
1. 数据库操作

   > `create database if not exists school `   --创建数据库
   >
   >
   >
   > `drop database if exists school`    --删除数据库
   >
   >
   > `show databases  `-- 查看所有数据库
   >
   >
   > `use school  `  --使用数据库

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