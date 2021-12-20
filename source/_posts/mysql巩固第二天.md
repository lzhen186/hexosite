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

   * ACID

     * 原子性Atomicity：事务开始后所有操作，要么全部做完，要么全部不做；
     * 一致性Consistency：事务开始前和结束后，数据库的完整性约束没有被破坏 ；
     * 隔离性Isolation：不同的事务之间彼此没有任何干扰；
     * 持久性Durability：事务对数据库的所有更新将被保存到数据库，不能回滚；
   * ```sql
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
   * 隔离级别及*读

     ![](images/隔离级别.png)