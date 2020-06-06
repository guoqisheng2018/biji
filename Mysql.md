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

### 排序查询

```
select 字段名 from 表名 order by 字段名 desc//倒叙
select 字段名 from 表名 order by 字段名 asc//升序
select 字段名 from 表名 order by 字段名 //升序
```

按函数排序

```
select 字段名 from 表名 order by lenth(字段名)
```

按多个字段进行排序

```
select 字段名 from 表名 order by 字段名1 asc,字段名2 desc//先按字段名1排序，字段名1相同再按字段名2排
```

### 常见函数

分类：

#### 单行函数（做处理）

传一个值返回一个值，如：concat、length、ifnull等

##### 字符函数

length（获取参数值的字节个数）中文utf8占3字节，gbk占2字节

```
select LENGTH('你好world')
```

concat（拼接字符串）

```
select CONCAT('hello','_','world')
```

upper、lower（转大写，小写）

```
select upper('hello')
```

substr、substring（截取字符）sql中索引从1开始

```
select substr("HelloWorld",6)
select substr("HelloWorld",索引开始值)
select substr("HelloWorld",索引开始值,长度)
```

instr 返回子串第一次出现的索引，若没有查到返回0

```
select instr("helloworldWorld",World)
```

trim 去掉首尾空格

```
select TRIM("   world     ")
select TRIM('a'  from  'aaaaaaWoraaaaaldaaaaaa')   //去掉两端的a
```

lpad用指定的字符左填充到指定长度（若指定长度小于本身长度，则从左开始截取指定长度的字符）

rpad用指定的字符右填充到指定长度（若指定长度小于本身长度，则从左开始截取指定长度的字符）

```
select LPAD('hello',10,'*')
```

replace替换. 用后一个字符串替换前一个字符串（若有多个会一起替换掉）

```
select REPLACE("你好world","你好","Hello")
```

##### 数字函数

round 四舍五入,默认取整

```
select round(1.45)
select round(1.457,2)//小数点后保留两位
```

ceil 向上取整,返回>=该参数的最小整数

```
select ceil(1.3)
```

floor 向下取整，返回<=该参数的最大整数

```
select floor(1.2)
```

truncate 截断

```
select truncate(1.65,1)
```

mod 取模(取余)    公式等于java中的 a-a/b*b（除法会自动舍去位数，例如10/3=3）

```
select mod(10,3)
select 10%3//这两一致
```

##### 日期函数

now 返回当年系统日期+时间

```
select now()
```

curdate 返回当前系统日期，不包含时间

```
select curdate()
```

curtime 返回当前系统时间，不包含日期

```
select curtime()
```

获取指定的部分

```
select YEAR(now())//里面放日期格式
select MONTH('1996-05-18')
select MONTHNAME('1996-05-18')
select DAY(字段名)
select HOUR(now())
select MINUTE(now())
select SECOND(now())
```

str_to_date :将日期格式的字符串转换成指定格式的日期

```
select STR_TO_DATE('5-18-1996','%c-%d-%Y')
```

| 序号 | 格式符 | 功能                 |
| ---- | ------ | -------------------- |
| 1    | %Y     | 四位的年份           |
| 2    | %y     | 两位的年份           |
| 3    | %m     | 月份（01，02，03……） |
| 4    | %c     | 月份（1，2，3……）    |
| 5    | %d     | 日（01，02，03……）   |
| 6    | %H     | 小时（24小时制）     |
| 7    | %h     | 小时（12小时制）     |
| 8    | %i     | 分钟（00，01，02……） |
| 9    | %s     | 秒（01，02，03……）   |

Date_format 将日期转换成字符

```
select DATE_FORMAT(now(),'%Y年%m月%d日')
```

##### 其他函数

多为系统函数，不常用

```
select version()//查看mysql版本号
select database()//查看数据库名
select user()//查看当前用户
```

##### 流程控制函数

if函数  类似于三目运算

```
select if(10>5,'大于','小于')
```

case 类似于java的switch case

语法格式一

case 要判断的字段或表达式

when 常量1 then 要显示的值1或者语句1

when 常量2 then 要显示的值2或者语句2

。。。。。

else 要显示的值n或者语句n

end

```
SELECT salary,department_id,
CASE department_id
WHEN 30 THEN salary*1.1
WHEN 40 THEN salary*1.2
WHEN 50 THEN salary*1.3
ELSE salary
END as newSalary
FROM employees
```

 语法格式二

case 

when 条件1 then 要显示的值1或者语句1

when 条件2 then 要显示的值2或者语句2

。。。。。

else 要显示的值n或者语句n

end

```
SELECT salary,department_id,
CASE 
WHEN salary>20000 THEN 'A'
WHEN salary>15000 THEN 'B'
WHEN salary>10000 THEN 'C'
ELSE 'D'
END AS salaryLevel
FROM employees
```

#### 分组函数（统计）

传一组值返回一个值

sum 求和，avg 平均值，max 最大值，min 最小值，count 计算个数

```
select sum(salary),avg(salary),max(salary),min(salary),count(salary) from employees
```

参数支持的类型只有数值型

max，min，count支持所有类型，count计算的是非空的个数

是否忽略null值

sum和avg没有计算null值（因为null+任何值为null，avg使用sum出来的值分别除非null的个数和总个数比较，得出结论avg也不计算null值）

max，min和count也是忽略计算null值的

可以和distinct组合使用

count的效率问题

MYISAM存储引擎下，count(*)效率最高，因为内部有个计数器，直接返回了个数

INNODB存储引擎下，count(*)和count(1)的效率差不多，比count(字段)要高一些

group by

```
select 字段名 from 表名 where 字段名=条件 group by 字段名 order by 字段名 排序方式
```

having(分组后的筛选)

```
select COUNT(1),department_id from employees GROUP BY department_id having count(1)>2
```



## DML语言

## DDL语言

## TCL语言

