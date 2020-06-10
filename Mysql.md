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

## DQL语言（数据查询语言）

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

注意⚠️若取别名想为两个单词的建议用下划线连接

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

#### 常见函数

分类：

##### 单行函数（做处理）

传一个值返回一个值，如：concat、length、ifnull等

###### 字符函数

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

###### 数字函数

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

###### 日期函数

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

###### 其他函数

多为系统函数，不常用

```
select version()//查看mysql版本号
select database()//查看数据库名
select user()//查看当前用户
```

###### 流程控制函数

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

##### 分组函数（统计）

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

按多个字段分组(group by后面的都有一致才算一组)

```
select avg(salary),department_id,job_id from employees group by department_id,job_id
```

### 连接查询

分类：

按年代分类

​		sql92标准（1992年推行）仅仅支持内连接

​		sql99标准【推荐】（1999年推行）支持内连接+外连接（左外和右外）+交叉连接

按功能分类

​		内连接

​				等值连接

​				非等值连接

​				自连接

​		外连接

​				左外连接

​				右外连接

​				全外连接

​		交叉连接

注意⚠️如果为表起了别名，无法再使用原表名限定字段，因为先执行的是from，已经为表起了别名，便不再认原表名了

#### sql92标准

##### 等值连接

多表等值连接的结果为多表的交集部分

n表连接，至少需要n-1个连接条件

多表的顺序无要求

一般需要为表取别名，以缩短表名

可以搭配所有子句使用，例如排序，分组，筛选

```
select name,boyname from beauty,boys where beauty.boyfriend_id=boys.id
```

##### 非等值连接

```
select e.salary,j.grade_level
from employees e,job_grades j
where e.salary between j.lowest_sal and j.highest_sal
```

##### 自连接

```
select a.last_name,b.last_name
from employees a,employees b
where a.manager_id=b.employee_id
```

#### sql99标准

##### 等值连接

语法

select 查询列表

from 表1 别名 连接类型【inner（内连接），left outer（左外），right outer（右外）full outer（全外），cross（全外）】

join 表2 别名

on 连接条件

where 筛选条件

```
select last_name,department_name
from employees a
INNER JOIN departments b
ON a.department_id=b.department_id//inner可以省略不写
```

注意⚠️如果要大于3张表进行连接，就再多写一遍join…… on……

select 查询列表

from 表1 别名 

连接类型 join 表2 别名

on 连接条件

连接类型 join 表3 别名

on 连接条件

where 筛选条件

##### 非等值连接

```
select e.salary,j.grade_level
from employees e 
join job_grades j
on e.salary between j.lowest_sal and j.highest_sal
```

##### 自连接

```
select a.last_name,b.last_name
from employees a 
join employees b
on a.manager_id=b.employee_id
```

##### 外连接

特点：
1、外连接的查询结果为主表中的所有记录
		如果从表中有和它匹配的，则显示匹配的值
		如果从表中没有和它匹配的，则显示null
		外连接查询结果=内连接结果+主表中有而从表没有的记录
2、左外连接，left join左边的是主表
右外连接，right join右边的是主表
3、左外和右外交换两个表的顺序，可以实现同样的效果

```
select NAME
FROM beauty a left JOIN boys b
on a.boyfriend_id= b.id
where b.id is null 
```

##### 交叉连接

等于笛卡尔积

```
select b.*,bo.*
from beauty b
cross join boys bo
```

### 子查询

出现在其他语句中select语句，称为子查询或内查询

外部的查询语句称为主查询或外查询

按子查询的位置：

select后：仅仅支持标量子查询，

from后：支持表子查询，

where或having后：支持标量子查询或者列子查询或者行子查询，

exists后（相关子查询）：支持表子查询

按子查询的功能：

标量子查询/单行子查询（结果集只有一行一列）

列子查询/多行子查询（结果集只有一列多行）

行子查询（结果集只有一行多列）

表子查询（结果集一般为多行多列）

特点

子查询放在小括号内

子查询一般放在条件的右侧

where或having后

标量子查询，一般搭配着单行操作符使用（<,>,>=,<=,=,<>,<=>）

```
SELECT * 
FROM employees
WHERE salary >(
SELECT salary
FROM employees
WHERE last_name='Abel'
)
```

列子查询，一般搭配着多行操作符使用（in，any/some，all）

```
SELECT * 
FROM employees
WHERE salary in(
select salary
from employees
WHERE manager_id=103
)
```

行子查询（一般不用，限制比较多，符号必须一致，可以使用其他代替）

```
select * 
FROM employees
where (employee_id,salary)=(
SELECT min(employee_id),max(salary)
from employees
)
```

select后(一般不用，使用外连接或者内连接可以做)

```
select d.*,(
select count(*)
from employees e
where e.department_id=d.department_id
)from departments d
```

from后

将子查询作为表必须取个别名

```
select a.*,j.grade_level
FROM(
select avg(salary) ag,department_id
from employees
GROUP BY department_id
) a join job_grades j
on a.ag BETWEEN j.lowest_sal and j.highest_sal
```

exists后面

查询结果为0表示没有查到数据，查询结果为1表示查到数据了

```
select EXISTS( select salary from employees where salary=300000)
```

```
select department_name
from departments d
where EXISTS(
select 1 
from employees e
where e.department_id=d.department_id
)
//可以使用in解决
select department_name
from departments d
where department_id 
IN(select department_id from employees)
```

### 分页查询

语法

select 查询列表

from 表

[join type] join 表2

on 连接条件

where 筛选条件

group by 分组字段

having 分组后的筛选

order by 排序的字段】

limit offset,size; 

offset要显示条目的起始索引（起始索引从0开始）

size要显示的条目个数

特点：limit放在最后，执行顺序也在最后

公式 limit (page-1)*size,size[page表示页码，size表示每页显示的条数]

```
select * from employees limit 0,10
```

### 联合查询

union联合 合并：将多条查询语句的结果合并成一个结果

应用场景：要查询的结果来自于多个表，且多个表没有直接的连接关系，但查询的信息一致

特点：

要求多条查询语句的查询列数是一致的

要求多条查询语句的查询的每一列的类型和顺序最好一致

只使用union关键字会去重，若不想去重使用union all

```
select * from employees where email like '%a%'
UNION
SELECT * from employees where department_id>90
```

## DML语言（数据操作语言）

数据操作语言

插入：insert

语法一：

```
insert into 表名 (列名，……) values(值1，……)
```

列名可以打乱顺序，但是值的顺序要与列名顺序一致。

列数与值的个数和类型必须匹配

可以省略列名，默认为全部列，顺序按表中顺序，不填值的使用null。

语法二

```
insert into 表名 set 列名1=值1,列名2=值2……
```

语法一支持一次性插入多行，语法二不支持

```
insert into 表名 values(值1,值2,……),(值1,值2,……)
```

语法一支持子查询，方式二不支持

```
insert into 表名(字段名1,字段名2,……)
select 字段名 from 表名
```

修改：update

修改单表语法：

```
update 表名 set 列名=新值,列名=新值 where 筛选条件
```

修改多表语法

```
update 表1 别名,表2 别名
set 列名=值,……
where 连接条件 and 筛选条件
或者
update 表1 别名 
连接类型 join 表2 别名
on 连接条件
set 列名=值,……
where 筛选条件
```

删除：delete/truncate

语法一：delete

删除单表的语法

```
delete from 表名 where 筛选条件
```

删除多表的语法

```
delete 表1的别名,表2的别名 
from 表1 别名,表2 别名
where 连接条件 and 筛选条件
或者
delete 表1的别名,表2的别名 
from 表1 别名 
连接类型 join 表2 别名
on 连接条件
where 筛选条件
```

语法二：

truncate table 表名 

truncate语法无法加where条件，一删即为全删

区别：

delete可以加where条件，truncate不能加

truncate删除，效率高

假如要删除的表中有自增长列，如果用delete删除后，再插入数据，自增长列的值从断点开始，而truncate删除后，再插入数据，自增长列的值从1开始

truncate删除没有返回值，delete删除有返回值

truncate删除不能回滚，delete删除可以回滚

## DDL语言（数据定义语言）

### 创建：create

#### 库的创建

```
create database 数据库名
create database if not exists 数据库名//容错性处理，如果库存在则不创建
```

#### 表的创建

```
create table 表名(
列名 列的类型【（长度）约束】,
列名 列的类型【（长度）约束】,
……
)
```

### 修改：alert

#### 库更改字符集

```
alert database 数据库名 character set 字符集
```

#### 表的修改

> ##### 修改列名

```
alter table 表名 change column 老列名 新列名 列的类型 //column可不加，一般都加，因为其他修改必须加
```

##### 修改列的类型或约束

```
alter table 表名 modify column 列名 列的类型
```

##### 添加新列

```
alert table 表名 add column 新列名 列的类型
```

##### 删除列

```
alert table 表名 drop column 列名
```

##### 修改表名

```
alert table 老表名 rename to 新表名
```

### 删除：drop

#### 库的删除

```
drop database 库名
drop database if exists 库名 //存在则删除
```

#### 表的删除

```
drop table 表名
drop table if exists 表名 //存在则删除
```

### 表的复制

#### 仅复制表结构

```
create table 复制后表的名字 like 复制来源表的名字
```

#### 复制表结构加数据

```
create table 复制后表的名字
select * from 复制来源表的名字
```

#### 只复制部分数据

```
create table 复制后表的名字
select 字段名，字段名 from 复制来源表的名字
where 条件
```

#### 只复制部分结构没有数据

```
create table 复制后表的名字
select 字段名，字段名 from 复制来源表的名字
where 1=2//让条件不成立
```

### 常见的数据类型

原则：所选择的类型越简单越好，能保存数值的类型越小越好

数值型

​		整型

​			设置有符号和无符号

```
create table tab_int(
				t1 INT,//有符号
				t2 INT UNSIGNED//无符号
)
```

​			特点：如果不设置无符号还是有符号，默认是有符号的，如果想设置无符号，需要添加unsigned关键字

​						如果不设置长度会有默认的长度，长度代表了显示的最大宽度，如果不够会在左边用0填充，但必须搭配zerofill使用，若加上了zerofill则自动变为无符号	

​			类型：			

| 整型类型     | 字节 | 范围                                                         |
| ------------ | ---- | ------------------------------------------------------------ |
| Tinyint      | 1    | 有符号：-128～127<br />无符号：0～255                        |
| Smallint     | 2    | 有符号：-32768～32767<br />无符号：0～65535                  |
| Mediumint    | 3    | 有符号：-8388608～8388607<br />无符号：0～1677215            |
| Int、integer | 4    | 有符号：-2147483648～2147483647<br />有符号：0～4294967295   |
| Bigint       | 8    | 有符号：-9223372036854775808～9223372036854775807<br />无符号：0～9223372036854775807*2+1 |

​		浮点型

​				定点数

​				浮点数

​					特点：M代表整数部位+小数部位的位数，D代表小数部位的位数

​								float和double默认无精度，decimal默认为（10，0）

​								定点型的精确度较高，如果要求插入数值的精度较高如货币运算则考虑使用

| 浮点数类型                         | 字节 | 范围                                                         |
| ---------------------------------- | ---- | ------------------------------------------------------------ |
| float(M,D)                         | 4    | ±1.75494351E-38～±3.402823466E+38                            |
| double(M,D)                        | 8    | ±2.2250738585072014E-308～±1.7976931348623157E+308           |
| 定点数类型                         | 字节 | 范围                                                         |
| DEC(M,D)（简写）<br />DECIMAL(M,D) | M+2  | 最大取值范围与double相同，给定decimal的有效取值范围由M和D决定 |

字符型

​		较短的文本：char、varchar

​		较长的文本：text、blob（较大的二进制）

| 字符串类型             | 字符数             | 描述及存储需求                                               | 特点                                             | 空间占用 | 性能 |
| ---------------------- | ------------------ | :----------------------------------------------------------- | ------------------------------------------------ | -------- | ---- |
| char(M)                | M，可以省略默认为1 | M为0～255之间的整数                                          | 固定长度                                         | 比较耗费 | 高   |
| varchar(M)             | M，不可省略        | M为0～65535之间的整数                                        | 可变长度                                         | 比较节省 | 低   |
| Enum(枚举值，枚举值……) |                    | 1～255个需要1个字节存储<br />255到65535个需要2个字节存储     | 只可以存一开始定义的枚举值（单个），不区分大小写 |          |      |
| Set(成员，成员……)      |                    | 最多可放64个成员<br />成员数1～8  1个字节<br />成员数9～16 2个字节<br />成员数17～24 3个字节<br />成员数25～32 4个字节<br />成员数33～64 8个字节<br /> | 只可以存一开始定义的值（多个），不区分大小写     |          |      |

日期型

timestamp受时区影响，例如东八区插入的时间在东九区读出来会晚一小时

| 日期和时间类型 | 字节 | 最小值              | 最大值              |
| -------------- | ---- | ------------------- | ------------------- |
| date           | 4    | 1000-01-01          | 9999-12-31          |
| datetime       | 8    | 1000-01-01 00:00:00 | 9999-12-31 23:59:59 |
| timestamp      | 4    | 19700101080001      | 2038年的某个时刻    |
| time           | 3    | -838:59:59          | 838:59:59           |
| year           | 1    | 1901                | 2155                |

### 常见约束	



## TCL语言

