# Mysql基础知识

## 数据增删改查


### 创建 USER表

* `PRIMARY KEY` 主键
* `AUTO_INCREMENT` AUTO_INCREMENT就可以从小到大自动生成
* `UNIQUE` 约束唯一标识数据库表中的每条记录
* `ENGINE = InnoDB` 存储引擎是innodb
* `DEFAULT CHARSET = utf8mb4` 修改字符集
* `COLLATE = utf8mb4_unicode_ci` 排序的规则

```mysql
CREATE TABLE USER
(
    ID         BIGINT PRIMARY KEY AUTO_INCREMENT,
    NAME       VARCHAR(100),
    TEL        VARCHAR(20) UNIQUE,
    AVATAR_URL VARCHAR(1024),
    ADDRESS    VARCHAR(1024),
    CREATED_AT TIMESTAMP NOT NULL DEFAULT NOW(),
    UPDATED_AT TIMESTAMP NOT NULL DEFAULT NOW()
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4
  COLLATE = utf8mb4_unicode_ci;
```


### **增**

```mysql
INSERT [INTO] 表名 [(字段列表)] VALUES (值列表)[, (值列表), ...]
-- 如果要插入的值列表包含所有字段并且顺序一致，则可以省略字段列表。
-- 可同时插入多条数据记录！
REPLACE 与 INSERT 完全一样，可互换。
INSERT [INTO] 表名 SET 字段名=值[, 字段名=值, ...]
```
例子：
```mysql
insert into person(id,name,age,phone,address)
values (1,'yang',22,'123232323','中国上海');
```

### **删**

```mysql
DELETE FROM 表名[ 删除条件子句]
        没有条件子句，则会删除全部
```
例子：
```mysql
delete from person where id = 1;
```

### **改**

```mysql
UPDATE 表名 SET 字段名=新值[, 字段名=新值] [更新条件]
```
例子：
```mysql
update person set address='浙江杭州';
```

### **查**

```mysql
SELECT 字段列表 FROM 表名[ 其他子句]
        -- 可来自多个表的多个字段
        -- 其他子句可以不使用
        -- 字段列表可以用*代替，表示所有字段
```
例子：
```mysql
select * FROM person;
```

## Drop、delete区别？
 
**用法不同**

* `drop`(丢弃数据):`drop table`表名 ，直接将表都删除掉，在删除表的时候使用。
* `delete`（删除数据）:`delete from 表名 where 列名=值`，删除某一列的数据

---

* 不带`where`子句的`delete`、以及`drop`都会删除表内的数据
* 但是`delete`只删除数据不删除表的结构(定义)
* 执行`drop`语句，此表的结构也会删除，也就是执行`drop`之后对应的表不复存在。

## 数据库主键和外键的区别

* 主键(主码) ：主键用于唯一标识一个元组，不能有重复，不允许为空。一个表只能有一个主键。
* 外键(外码) ：外键用来和其他表建立联系用，外键是另一表的主键，外键是可以有重复的，可以是空值。一个表可以有多个外键。

## 乐观锁和悲观锁的理解以及它的一下实现的一些方式？

悲观锁顾名思义，我先一个个介绍，悲观锁就是很悲观，我们每次都在假设说我们在拿到这个数据的之前，有可能改过了我们这个数据，那就拿到不是我们想要的，
所以在每次拿到数据的之前的时候，他都会上一次锁，这样别人如果想要去改完们这个数据，
然后我们这个数据了他就会被阻塞到直到说他拿拿到这个锁，就我们传统的一些关系型数据库里面，其实用到很多，
像这种锁机制，比如说我们的行(hang)锁，我们的表锁,读锁啊写锁呀，都是在操作之前，要先给他上一个锁，
再比如我们Java的synchronized这个关键字，其实它的实现也是一个悲观锁，这是我对悲观锁的一个见解，
然后乐观锁，其实就是比较乐观，就是每次拿数据的时候呢，都觉得说别人不会在这个时候去改我们的数据，所以他不会去上一个锁，
那他不会上锁的话，但是我们在更新数据的时候，我们会去判断一下说这个期间内，别人有没有动过我们这个数据，有没有更新过我们这个数据，
所以乐观锁他可以提高我们的吞吐量，就像我们数据库里面他是有提供一个类，类似于我们的一个write_condition这么一个机制，其实就是提供一个乐观锁。


## 事务 以及 MySQL数据结构

![事务](/img/Mysql基础知识/事务.png)




