# PostgreSQL 基础

## 创建role和授权

```sql
CREATE ROLE ywuser LOGIN PASSWORD 'password'; -- create role
GRANT ALL ON DATABASE ywdb TO ywuser; -- authorization
ALTER ROLE ywuser PASSWORD 'password'; -- change password
ALTER ROLE "yhuser" CREATEDB NOCREATEROLE LOGIN; -- 允许role创建DB
```

## 查询所有role

```sql
SELECT * FROM pg_roles;
```

## 查看当前连接数

```sql
select "count"(*) from pg_stat_activity;
select COUNT(*) from pg_stat_activity where datname = 'uat_yhdb' and application_name = 'NAVICAT'; -- 条件匹配
```

## 查看实例最大连接数限制

```sql
show max_connections;
```

## 导入和导出

```bash
pg_restore -h smpt-prod.pg.rds.aliyuncs.com -p 3433 -U simpletour -d backdb -W /tmp/simpletour_2018-06-04_05_00_01.dump # 导入
pg_dump  -Usimpletour  -p5432 -d  simpletour -F c -f backup_20180604.dump #导出
```

> 具体参考 --help

## 数据库复制

```sql
-- 断开当前数据库session连接
SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE pid <> pg_backend_pid() AND datname = 'dbname';
-- db rename
alter database simpletour_uat1 rename to simpletour_uat;
-- copy db
CREATE DATABASE yhdb_tmp_new with template yhdb owner yhuser;
```

## 授权ROLE特定的权限

**[Reference](https://stackoverflow.com/questions/760210/how-do-you-create-a-read-only-user-in-postgresql)**

```sql
grant select,update on all tables in schema public to xxx;
```
