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

```
select a.字段,a.字段,b.字段,b.字段,c.字段,c.字段
from p_payment_method a
join p_payment_order b on a.pay_order_no =b.pay_order_no 
join p_trans_order c on b.rans_order_no=c.rans_order_no
```



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

#### 六大约束

NOT NULL：非空，用于保证该字段的值不能为空

DEFAULT：默认，用于保证该字段有默认值

PRIMARY KEY：主键，用于保证该字段具有唯一性，并且非空

UNIQUE：唯一，用于保证该字段具有唯一性，可以为空

CHECK：检查，mysql不支持

FOREIGN KEY：外键约束，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值（从表添加外键约束，用于引用主表某列的值）

约束分类：

#### 列级约束

六大约束语法上都支持，但是外键约束无效果

直接在字段类型后加

```
create table 表名(
				字段名 字段类型 约束类型，
				字段名 字段类型
)
create table user(
				id int PRIMARY KEY
)
```

#### 表级约束

除了非空和默认其他都支持

```
create table 表名(
				字段名 字段类型，
				字段名 字段类型，
				[CONSTRAINT 约束名] 约束类型（字段名）//[]中的可以不写
)
create table user(
				id int,
				name VARCHAR(20) NOT NULL,
				job_id int,
				CONSTRAINT pk PRIMARY KEY(id),
				CONSTRAINT fk_user_job FOREIGN KEY(job_id) REFERENCES job(id)//外键一般起名为fk_当前表名_主表名
)
```

主键，外键，唯一键会自动生成索引

#### 查询索引的方式

```
show index from 表名//non_uniue 为0代表唯一性
```

#### 主键和唯一的对比

添加联合主键的方式

```
create table user(
				id int,
				name VARCHAR(20),
				job_id int,
				CONSTRAINT pk PRIMARY KEY(id,name),
				CONSTRAINT fk_user_job FOREIGN KEY(job_id) REFERENCES job(id)
)
```

|        | 保证唯一性 | 允许为空 | 一个表中可以有几个约束                                       |
| ------ | ---------- | -------- | ------------------------------------------------------------ |
| 主键   | 是         | 是       | 一个，但允许有联合主键（几个列拼接在一起的值为唯一就行，不再是单个列） |
| 唯一键 | 是         | 是       | 可以有多个，允许有联合唯一键                                 |

#### 外键

要求在从表设置外键关系

从表的外键列的类型和主表的关联列的类型要一致或者兼容

主表的关联列必须是一个key（一般是主键或唯一键）

插入数据时，先插入主表，再插入从表，删除数据时，先删除从表再删除主表

#### 修改约束

```
列级约束
alert table user modify column id int PRIMARY KEY；
表级约束
alert table user add [CONSTRAINT pk] PRIMARY KEY(id)//[]中的可以不写
```

#### 删除表级约束

```
删除主键
alter table 表名 drop PRIMARY KEY
删除唯一键
alter table 表名 drop index 约束名
删除外键
alert table 表名 drop foreign key 约束名
```

注：约束名可通过查询索引的方式查到

#### 标识列

又称自增长列

含义：可以不用手动的插入值，系统提供默认的序列值

特点：

1.标识列必须是在key后面（主键，唯一键，外键）

2.一个表只能有一个标识列

3.标识列的类型只能是数值型

创建时

```
create table job_2(
		id int PRIMARY KEY AUTO_INCREMENT,//自增长
		name VARCHAR(20) NOT NULL
)
```

查看步长和起始值

```
show variables like '%auto_increment%'
```

设置步长

```
set auto_increment_increment=3//步长
auto_increment_offset并不是设置的起始值，若想设置起始值可以手动先插入一条带值的数据，后面再开始自增
```

修改时

```
alert table user modify column id PRIMARY KEY AUTO_INCREMENT
```

## TCL语言(事务控制语言)

### 事务

事务：一个或一组sql语句组成一个执行单元，这个执行单元要没全部执行，要么全部不执行

#### 事务的ACID属性

原子性（Atomicity）：一个事务不可再分割，要么都执行要么都不执行

一致性（Consistency）：一个事务执行会使数据从一个一致状态切换到另一个一致状态

隔离性（Isolation）：一个事务的执行不会受其他事务的干扰

持久性（Durability）：一个事务一旦提交，则会永久的改变数据库的数据

#### 事务的创建

隐式事务：事务没有明确的开启和结束标记

比如insert，update，delete语句

显示事务：事务具有明确的开启和结束标记

前提：必须先设置自动提交功能为禁用

```
查看自动提交
show variables like 'autocommit'
设置自动提交为关闭
set autocommit=0
```

```
步骤1:
set autocommit=0
start tranasction；//可写可不写，set autocommit=0已默认开启
步骤2：编写事务中的sql语句
语句1，
语句2，
……
步骤3:结束事务
commit；//提交事务
rollback；//回滚
```

#### 事务并发

对于同时运行的多个事务，当这些事务访问数据库中相同的数据时，如果没有采取必要的隔离机制，就会导致各种并发问题

**脏读**（针对**更新数据**）：对于两个事务T1，T2，T1读取了已经被T2更新但还没有被提交的字段。之后，若T2回滚，T1读取的内容就是临时且无效的.
**不可重复读**（**更新**的时候）：对于两个事务T1，T2，T1读取了一个字段，然后T2更新了该字段。之后，T1再次读取同一个字段，值就不同了.
**幻读**（针对**插入数据**）：对于两个事务T1，T2，T1从一个表中读取了一个字段，然后T2在该表中插入了一些新的行.之后，如果T1再次读取同一个表，就会多出几行.

#### 事务的隔离级别

|                  | 脏读     | 不可重复读 | 幻读     |
| ---------------- | -------- | ---------- | -------- |
| read-uncommitted | 会出现   | 会出现     | 会出现   |
| read-committed   | 不会出现 | 会出现     | 会出现   |
| repeatable-read  | 不会出现 | 不会出现   | 会出现   |
| serializable     | 不会出现 | 不会出现   | 不会出现 |

serializable会增加类似锁的操作，影响效率

mysql中默认第三个级别 repeatable-read

oracle只有两个级别 （read committed，serializable）默认read-committed

查看隔离级别

```
select @@transaction_isolation
```

设置隔离级别

```
set session transaction isolation level 隔离级别  //当前连接的隔离级别
set global transaction isolation level 隔离级别  //数据库系统的全局的隔离级别
```

#### delete和truncate在事务使用时的区别

对delete有效，对truncate无效

#### 保存点

savepoint

```
set autocommit=0;
start tranasction;
sql语句一;
savepoint a;
sql语句二;
ROOLBACK to a;
//执行结果sql语句一会被执行并保存，sql语句二不会被保存，会被回退
```

### 视图

 5.0.1版本开始提供的功能，是一种虚拟存在的表，只保存了sql逻辑

#### 视图的好处

重用sql语句

简化复杂的sql操作，不必知道它的查询细节

保护数据，提高安全性

#### 视图的创建

```
create view 视图名
as
复杂的查询语句
```

#### 视图的修改

```
//方式一
create or replace view 视图名
as
查询语句
//方式二
alert 视图名 
as
查询语句
```

#### 删除视图

```
drop view 视图名，视图名，……
```

#### 查看视图

```
desc 视图名
show create view 视图名
```

#### 视图的更新

插入

```
insert into 视图名 values()//与表一致，原始表会一并更改了
```

修改

```
uopdate 视图名 set 字段名=值//与表一致，原始表会一并更改了
```

删除

```
delete from 视图名 where 条件
```

若视图具有一下特征则不能更新

包含以下关键字的sql语句，分组函数，distinct，group by，having，union或者union all

常量视图

select中包含子查询

join

from一个不能更新的视图

where子句的子查询引用了from子句中的表
用户不具备修改视图的权限

### 变量

#### 系统变量

变量由系统提供，不是用户定义的，属于服务器层面

使用的语法

查看所有的系统变量

```
show global variables//全局变量
show [session] variables//会话变量
```

查看满足条件的部分系统变量

```
show global｜[session] variables like ‘%%’
```

查看指定的某个系统变量的值

```
select @@global.｜[session.]系统变量名
```

为某个系统变量赋值

```
set global｜[session] 系统变量名=值
set @@global.｜[session.]系统变量名=值
```

##### 全局变量

作用域：服务器每次启动将为所有的全局变量赋于初始值，针对于所有的会话（连接）有效，但不能跨重启

##### 会话变量

作用域：仅仅针对于当前的会话（连接）有效

#### 自定义变量

##### 用户变量

作用域：针对于当前会话（连接）有效，同于会话变量的作用域

应用在任何地方，也就是begin end里面或begin end外面

变量时用户自定义的，不是由系统提供的

赋值的操作符： =或：=

1.声明并初始化

```
set @用户变量名=值；或
set @用户变量名 ：=值；或
select @用户变量名 ：=值；
```

2.赋值

```
方式一：通过set或select
set @用户变量名=值；或
set @用户变量名 ：=值；或
select @用户变量名 ：=值；
方式二：通过SELECT INTO
select 字段 into 变量名
from 表；
select count(1) INTO @count
FROM employees
```

3.使用（查看用户变量的值）

```
select @用户变量名
```

##### 局部变量

作用域：仅仅在定义它的begin end中有效

应用在begin and中的第一句话

1.声明

```
DELARE 变量名 类型；
DELARE 变量名 类型 DEFAULT 值；
```

2.赋值

```
方式一：通过set或select
set 局部变量名=值；或
set 局部变量名 ：=值；或
select @局部变量名 ：=值；
方式二：通过SELECT INTO
select 字段 into 局部变量名
from 表；
```

3.使用

```
select 局部变量名
```

##### 对比用户变量和局部变量

|          | 作用域          | 定义和使用的位置              | 语法                          |
| -------- | --------------- | ----------------------------- | ----------------------------- |
| 用户变量 | 当前会话        | 会话中的任何地方              | 需要加上@符号，不用限定类型   |
| 局部变量 | begin and重化工 | 只能在begin end中，且为第一句 | 一般不用加@符号，需要限定类型 |

### 存储过程

好处

1.提高代码的重用性

2.简化操作

3.减少了编译次数并且减少了和数据库的连接次数，提高了效率

含义：一组预先编译好的sql语句的集合，理解成批处理语句

#### 参数模式

IN：该参数可以作为输入，也就是该参数需要调用方传入值

OUT：该参数可以作为输出，也就是该参数可以作为返回值

INOUT：该参数既可以作为输入又可以作为输出，也就是该参数需要传入值，又可以返回值

#### 创建语法

```
create procedure 存储过程名（参数列表）
Begin
存储过程体（一组合法的sql语句）
end
```

注意⚠️：参数列表包含三部分

参数模式，参数名，参数类型

举例 IN stunma varchar（20）

如果存储过程体仅有一句话，begin end可以省略

**存储过程体中每条sql语句的结尾要求必须加分号**

**存储过程的结尾可以使用DELIMITER重新设置**

**语法**

**DELIMITER 结束标记**

```
无参
DELIMITER $
CREATE procedure myp1()
BEGIN
INSERT INTO admin(username,`password`)
VALUES('jaoh1','000000'),('jaoh2','000000'),('jaoh3','000000'),('jaoh4','000000'),('jaoh5','000000');
END $
带in和out模式
DELIMITER $
create PROCEDURE myp2(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20),OUT userCP INT)
BEGIN
SELECT bo.boyName,bo.userCP INTO boyName,userCP
FROM boys bo
RIGHT JOIN beauty b ON bo.id=b.boyfriend_id
where b.name=beautyName;
END $
```

#### 调用语法

```
CALL 存储过程名（实参列表）

call myp2('name',@bname,@userCP)//声明一个用户变量接受out返回的参数值
```

#### 删除存储过程

```
drop procedure 存储过程名
```

#### 查看存储过程的信息

```
show create procedure 存储过程名
```

### 函数

好处

1.提高代码的重用性

2.简化操作

3.减少了编译次数并且减少了和数据库的连接次数，提高了效率

含义：一组预先编译好的sql语句的集合，理解成批处理语句

#### 和存储过程的区别

存储过程：可以有0个返回，也可以有多个返回，适合做批量插入，批量更新

函数：有且只有一个返回，适合做处理数据后返回一个结果

#### 创建语法

```
create function 函数名（参数列表）returns 返回类型
Begin
函数体
end

函数体当中需要声明一个自定义变量，接受返回值，并且用return返回
```

参数列表包含两部分

参数名 参数类型

函数体肯定会有return语句，如果没有会报错

如果函数体仅有一句话，begin end可以省略

使用DELIMITER设置结束标记

#### 调用语法

```
select 函数名（参数列表）
```

#### 查看函数的信息

```
show create function 函数名
```

#### 删除函数

```
drop function 函数名
```

### 流程控制结构

顺序结构：程序从上往下依次执行

分支结构：程序从两条或多条路径中选择一条去执行

循环结构：程序在满足一定条件的基础上，重复执行一段代码

#### 分支结构

1.if函数

功能：实现简单的双分支

语法：

```
if(表达式1,表达式2,表达式3)
```

执行顺序

如果表达式1成立，则if函数返回表达式2的值，否则返回表达式3的值

应用在任何地方

2.case结构

情况1：类似于java中的switch语句，一般用于实现等值判断
语法：

```
CASE 变量|表达式|字段
WHEN 要判断的值 THEN 返回的值1
WHEN 要判断的值 THEN 返回的值2
……
ELSE 要返回的值n
END
```

```
CASE 变量|表达式|字段
WHEN 要判断的值 THEN 返回的语句1;
WHEN 要判断的值 THEN 返回的语句2;
……
ELSE 要返回的语句n;
END CASE
```

情况2：类似于java中的多重IF语句，一般用于实现区间判断
语法：

```
CASE
WHEN 要判断的条件1 THEN 返回的值1
WHEN 要判断的条件2 THEN 返回的值2
……
ELSE 要返回的值n
END
```

```
CASE
WHEN 要判断的条件1 THEN 返回的语句1;
WHEN 要判断的条件2 THEN 返回的语句2;
……
ELSE 要返回的语句n;
END CASE
```

特点：

1.可以作为表达式，嵌套在其他语句中使用，可以放在任何地方，BEGIN END中或BEGIN END外

2.可以作为独立的语句去使用，只能放在BEGIN END中使用

3.如果WHEN中的值满足或条件成立，则执行对应的THEN后面的语句，并且结束CASE如果都不满足，则执行ELSE中的语句或值
4.ELSE可以省略，如果ELSE省略了，并且所有WHEN条件都不满足，则返回NULL

3.if结构

功能：实现多重分支
语法：

```
if 条件1 then 语句1;
elseif 条件2 then 语句2；
[else 语句n;]
end if;
```

应用在begin end中

### 循环结构

分类：
while、loop、repeat
循环控制：
iterate类似于 continue，继续，结束本次循环，继续下一次
leave 类似于 break，跳出，结束当前所在的循环

应用在begin end中

#### while

语法

```
[标签：] while 循环条件 do
循环体；
end while [标签];
```

#### loop

语法

```
[标签：] loop
循环体；
end loop [标签]；
```

可以用来模拟简单的死循环

#### repeat

语法

```
[标签：] repeat
循环体；
until 结束循环的条件
end repeat [标签]；
```

例子

```
DELIMITER $
CREATE PROCEDURE test_whilel(IN insertCount INT)
BEGIN
DECLARE i INT DEFAULT 1;
a:WHILE i<=insertCount DO
INSERT INTO admin(username,`password`) VALUES (CONCAT ('xiaohua', i),'0000');
IF i>=20 THEN LEAVE a;
END IF;
SET i=i+1;
END WHILE a;
END $
```

