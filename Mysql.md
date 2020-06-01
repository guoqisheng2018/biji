# Mysql

## 概念

数据库的好处
1、可以持久化数据到本地
2、结构化查询
数据库的常见概念
1、DB：数据库，存储数据的容器
2、DBMS：数据库管理系统，又称为数据库软件或数据库产品，用于创建或管理DB
3、SQL：结构化查询语言，用于和数据库通信的语言，不是某个数据库软件特有的，而是几乎所有的主流数据库软件通用的语言
数据库存储数据的特点
1、数据存放到表中，然后表再放到库中
2、一个库中可以有多张表，每张表具有唯一的表名用来标识自己
3、表中有一个或多个列，列又称为“字段”，相当于java中“属性” 
4、表中的每一行数据，相当于java中“对象”

mysql的优点

1、开源、免费、成本低

2、性能高、移植性好

3、体积小、便于安装

## 建库建表基本操作

注释

```
#注释文字
-- 注释文字
/* 
注释文字
注释文字
*/
```



查看mysql库

```
show databases
```

查看表名

```
show tables//use后使用
```

```
show tables from 库名
```

查看当前在什么库

```
select database()
```

查看数据库版本

```
select version()
```

创建数据库

```
create database 库名
```

进入库

```
use 库名
```

创建表

```
create table 表名(
字段名 字段类型,
字段名 字段类型
)
```

查看表结构

```
desc 表名
```

查询语句

```
select * from 表名
```

```
select 字段名,字段名 from 表名
```

插入语句

```
insert into 表名(字段名，字段名) values(对应值，对应值)
```

修改语句

```
update 表名 set 字段名=值 where +条件(不加where条件表示全改)
```

删除语句

```
delete from 表名 where +条件(不加where条件表示全删)
```

## DQL语言（查询语句）

### 基础查询

#### 查询列表

1表中的字段

```
select 字段名 from 表名
```

2常量值

```
select 100
```

3表达式

```
select 100*99
```

4函数

```
select now()
```

查询出来的结果是一个虚拟的表格

#### 起别名

好处：1.便于理解  2.如果要查询的字段有重名的情况，可以使用别名区分开（关联查询可用）

```
select 99*100 as 结果//使用as关键字
select 99&*100 结果//直接使用空格
```

注意若取别名想为两个单词的建议用下划线连接

```
select 99*100 as calculation_results
```

若实在想取带空格的使用着重号(`)双引号或单引号扩起来（最好着重号或双引号）

```
select 99*100 as "calculation results"
select 99*100 as `calculation results`
```

#### 去重

使用DISTINCT关键字(大小写都行)

```
select DISTINCT(department_id) from employees
```

#### +号的作用

仅做计算

```
select 100+99
```

如果有字符在其中会转换成数值，再进行运算(转换失败的算为0)，如果有一方值为null计算结果为null

```
select '100'+99//结果199
select 'hello'+99//结果99
select 100+NULL//结果NULL
```

#### 拼接

使用CONCAT关键字进行拼接（其中可传多个字符串或者字段名）

```
select CONCAT('hello','world')
select CONCAT("我是",name,"我",age,"岁")
```

### 条件查询

语法：

```
select 字段名 from 表名 where 条件
```

 执行顺序，先from，然后where，最后select

#### 分类

1按条件表达式筛选

条件运算符：<、>、=、>=、<=、!=或<>(不等于建议使用<>)

2按逻辑表达式筛选

逻辑运算符：&&、||、!或者and、or、not（建议使用and、or、not）

3模糊查询

运算符：like、between ... and ...、in、is not

like

%表示代表任意个字符（0到多个），_表示任意单个字符

```
select * from 表名 where 字段名 like ‘%hello’//现表示匹配任何以hello结尾的
```

若通配符需要转义在通配符前面加\或者使用ESCAPE声明转义字符的方式

```
select * from 表名 where 字段名 like ‘_$_%’ ESCAPE '$'
```

between ... and ...

包含两个临界值

两个临界值不可调换，顺序为>=第一个值，<=第二个值

```
select 字段名 from 表名 where 字段名 between 值 and 值
```

in

```
select 字段名 from 表名 where 字段名 in(值1,值2,值3)
```

is

=或<>不能判断null值

```
select 字段名 from 表名 where 字段名 is Null
```

is not

```
select 字段名 from 表名 where 字段名 is not Null
```

is和is not 后面基本只能配null

安全等于<=>

<=>可以判断null值也可以判断普通的值

```
select 字段名 from 表名 where 字段名 <=> Null
```

IFNULL

下面查询语句表示如果字段为空则返回0，不为空返回本身数值（,后面可以填任何想填的结果）

```
select IFNULL(字段名,0) from 表名
```



## DML语言

## DDL语言

## TCL语言

