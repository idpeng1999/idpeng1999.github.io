# Mysql Linux命令行教程

## 常用命令

* 进入:`mysql -u root -p`
* 查看数据库:`show databases;` 注：此处；不可少。
* 进入目标数据库:` use + 表名 + ;`
* 列出所有表:`show tables;`

![演示1](/img/mysql-linux命令行教程/img.png)

## 导入导出

### 导出(在外执行，不需要进入数据库)
* `mysqldump -u 数据库用户名 -p 数据库名称 表> 导出表名称.sql`
* `mysqldump -u root -p test i> i_20230130.sql;`
* 数据库用户名 root
* 数据库名称 test
* 导出表名称 i
* 把表去掉就是导出整个数据库

### 导入(需进入数据库)

* `source /home/i_20230130.sql;` 需进入数据库,并且使用` use + 表名 + ;`进入目标数据库，再进行操作
* 文件路径 /home/i_20230130.sql

## 复制表结构及数据到新表(需进入数据库)

* `CREATE TABLE 新表 SELECT * FROM 旧表;`
* `CREATE TABLE sys_user_bak_20230130 SELECT * FROM sys_user;`

## 修改表名(需进入数据库)

* `rename table sys_user to sys_user_bak;`
* 表名 sys_user
* 修改后的表名 sys_user_bak




