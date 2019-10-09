---
title: 统计MySQL数据库中数据表或字段等数量的SQL语句
date: 2019-10-05 16:10:54
category: [Backend]
tags: [Utility,MySQL]
---



##### 1、查询一个表中有多少个字段：

```sql
SELECT COUNT(*) FROM information_schema. COLUMNS
WHERE table_schema = '数据库名'
AND table_name = '表名';
```



##### 2、查询一个数据库中有多少张表：

```sql
SELECT COUNT(*) TABLES, table_schema FROM information_schema.TABLES  
WHERE table_schema = '数据库名' 
GROUP BY table_schema;
```



##### 3、查询一个数据库中一共有多少个字段：

```sql
SELECT COUNT(column_name) FROM information_schema.COLUMNS 
WHERE TABLE_SCHEMA = '数据库名';
```



##### 4、查询一个数据库中的所有表和所有字段、字段类型及注释等信息：

```sql
SELECT TABLE_NAME, column_name, DATA_TYPE, column_comment FROM information_schema.COLUMNS
WHERE TABLE_SCHEMA = '数据库名' ;
```



##### 5、MySQL数据库中统计一个库中的所有表的行数：

```sql
SELECT table_name,table_rows FROM information_schema.tables 
WHERE TABLE_SCHEMA = '数据库名'
ORDER BY table_rows DESC;
```





