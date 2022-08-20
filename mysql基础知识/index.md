# Mysql基础知识

## 数据增删改查


创建 USER表

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


**增**

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

**删**

```mysql
DELETE FROM 表名[ 删除条件子句]
        没有条件子句，则会删除全部
```
例子：
```mysql
delete from person where id = 1;
```

**改**

```mysql
UPDATE 表名 SET 字段名=新值[, 字段名=新值] [更新条件]
```
例子：
```mysql
update person set address='浙江杭州';
```

**查**

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


## 数据库主键和外键的区别

* 主键(主码) ：主键用于唯一标识一个元组，不能有重复，不允许为空。一个表只能有一个主键。
* 外键(外码) ：外键用来和其他表建立联系用，外键是另一表的主键，外键是可以有重复的，可以是空值。一个表可以有多个外键。

## 




