# MySQL数据库关键字

## where(条件)

```mysql
select * from person
 where name='yang'&& age=22;
```

## like(搜索查询)

搜索查询，或者说模糊匹配，表达式主要涉及到两个符号：

* 百分号 %：匹配任意多个字符 
* 下划线 _：匹配固定一个字符

例如：

查询所有的数据，找到其中 name 字段以字符「ang」结尾的数据记录集合：

```mysql
select * from person
 where name like '%ang';
```

查询所有的数据，找到其中 name 字段以字符「ang」结尾，并且前面还有一个任意字符的数据记录集合：

```mysql
select * from person
 where name like '_ang';
```

## in(集合)

in 关键字也是使用在 where 子句的条件表达式中，它限制的是一个集合，只要字段的值在集合中即符合条件

例如：

查询出来所有年龄是 22,30,23 的人数据记录
```mysql
select * from person
where age in (22,30,23);
```

not in 反向限制

## order by(排列)

* `order by`子句根据一列或者多列的值，按照升序或者降序排列数据。 
* `asc`表示数据结果集按升序排序，`desc`表示数据结果集按降序排序。

例子：

数据参照 id 列，倒序排序
```mysql
select * from person
order by id desc;
```

## group by(分组)

`group by`子句用于将查询返回的结果集进行一个分组，并展示各个分组中排在第一个的记录，将分组中其余成员隐藏。

例如：

按照姓名对结果集进行分组,分组之后，只展示每个分组下排序第一的记录，其余成员隐藏。
```mysql
select * from person
group by name;
```

## having(排序)

`having`子句在我看来就是一个高配版的where子句，无论是我们的分组或是排序，
都是基于以返回的结果集，也就是说where子句的筛选已经结束。

例如：

对排序、分组后的数据集依然有筛选需求，就用到我们的`having`子句
```mysql
select avg(age) as vage from person
group by name
having vage>23;
```

## between(两个数值之间)

在where中使用between表示一个数在两个数值之间取值

## distinct(表示将distinct后的属性去重)

去掉重复的关键字

```mysql
select distinct 列名 from 表名
-- order by ：排序
-- desc 降序(默认升序)
-- asc  升序
select 列名 from 表名 order by 列名1 desc，列名2 asc
```

## as(别名)

## avg(平均)

## min(最小值)

## max(最大值)

## sum(总和)

## count(计数)



