---
title: PG数据库常用操作
date: 2019-01-09 19:36:07
tags: 
  - 数据库
categories: 
  - 解决方案
---

记录一下，在开发过程中接触到的一些PG数据库常用操作，以备不时之需。<!-- more -->

# 1.全量迁移

- 备份数据

```bash
pg_dump -h 172.19.235.145 -U <username> -d <database> > 20180704_dbpe.sql
```

- 正式迁移

首先要修改备份文件*.sql的owner，防止权限出现错误。

```bash
psql -h <ip> -U <username> -d <database> -f 20180704_dbpe.sql
```

【注意点】该迁移操作会覆盖原来的数据库，所以最好创建一个新库。

# 2.列出所有表名和数据库名

```bash
select tablename from pg_tables where schemaname ='public';
```

# 3. PostgreSQL 中 有时候想删除数据库（drop database swiftliveqaapi;），发现提示“ERROR:  database "xxxxxx" is being accessed by other users DETAIL:  There are 30 other sessions using the database.”

```bash
用psql 登录进入， 执行语句：
SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE datname='数据库名' AND pid<>pg_backend_pid();
然后就可以删除数据库了
```

# 4.修改表的序列为id最大值

```bash
SELECT setval('表名_id_seq', (SELECT MAX(id) FROM 表名));
```


