---
title: 常用sql语句
date: 2022-03-31T08:12:33.514Z
---
* Mysql根据两列进行去重

```sql
select distinct a.* from sys_patriarch_student a where a.lastUpdateDateTime = (
  select max(lastUpdateDateTime) from sys_patriarch_student b where b.patriarch = a.patriarch 
  and b.studentId = a.studentId
)
```