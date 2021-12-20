---
title: MySQL巩固第二天
date: 2021-12-20T08:48:19.536Z
---
1. 事务

   > 事务就是将一组SQL语句放在同一批次内去执行 
   >
   > 如果一个SQL语句出错,则该批次内的所有SQL都将被取消执行 
   >
   > MySQL事务处理只支持InnoDB和BDB数据表类型

   1. ACID

      * 原子性Atomicity：事务开始后所有操作，要么全部做完，要么全部不做；
      * 一致性Consistency：事务开始前和结束后，数据库的完整性约束没有被破坏 ；
      * 隔离性Isolation：不同的事务之间彼此没有任何干扰；
      * 持久性Durability：事务对数据库的所有更新将被保存到数据库，不能回滚；
   2.    

      ```sql
      --设置不自动提交
      set autocommit = 0;    
      --开始事务
      start transaction;
      --保存点
      savepoint a;
      delete from account where name = 'A';
      savepoint b;
      update account set cash = 0 where name = 'B';
      savepoint c;
      --回滚到指定保存点
      rollback a;
      --设置自动提交
      set autocommit = 1;
      ```
   3. 隔离级别及*读

      ![](images/隔离级别.png)

      `🔗：`<https://www.jianshu.com/p/4e3edbedb9a8>``
2. 索引

   > 提高查询速度\
   > 确保数据的唯一性\
   > 可以加速表和表之间的连接 , 实现表与表之间的参照完整性 使用分组和排序子句进行数据检索时 , 可以显著减少分组和排序的时间 
   >
   > 全文检索字段进行搜索优化.

   1. 分类

      * 主键索引 (Primary Key)
      * 唯一索引 (Unique)

        避免同列数据重复 ，可以有多个
      * 常规索引 (Index/key)

        加在查询条件的字段

        不宜添加太多，影响数据增删改
      * 全文索引 (FullText)

        只能用于MyISAM类型的数据表

        只能用于CHAR , VARCHAR , TEXT数据列类型

        大型数据
   2. 语句

      ```sql
      --创建方法一，建表的时候
      create table 表名(
      ......
      [unique|fulltext|spatial] key|index [索引名] (字段名[(长度)] [ASC | DESC]);
      )

      --创建方法二，建表后
      create [unique|fulltext|spatial] index [索引名] 
      on 表名  (字段名[(长度)] [ASC | DESC]);

      --创建方法三，建表后
      alter table 表名 add [unique|fulltext|spatial] index
      索引名 (字段名[(长度)] [ASC | DESC])

      --删除索引
      DROP INDEX 索引名 ON 表名字

      --删除主键索引
      ALTER TABLE 表名 DROP PRIMARY KEY;

      --显示索引信息: 
      SHOW INDEX FROM student;

      --分析语句
      EXPLAIN SELECT * FROM student WHERE studentno='1000';

      --全文索引
      EXPLAIN SELECT *FROM student 
      WHERE MATCH(studentname) AGAINST('love');
      ```
   3. 准则

      索引不是越多越好

      不要对经常改动的数据加索引

      小数据量的表不建议加索引

      索引一般加在查找条件的字段
   4. 索引的数据结构

      `🔗：`<http://blog.codinglabs.org/articles/theory-of-mysql-index.html>

      分析工具：

      `🔗：`<https://blog.csdn.net/jiadajing267/article/details/81269067>``