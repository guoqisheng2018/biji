# redis

## CAP理论

Consistency（强一致性）

Availability（可用性）

Partition tolerance（分区容错性）

注意⚠️cap只能三选二（无法三个都满足）

因此，根据 CAP 原理将 NoSQL 数据库分成了满足 CA 原则、满足 CP 原则和满足 AP 原则三 大类：

CA - 单点集群，满足一致性，可用性的系统，通常在可扩展性上不太强大。

CP - 满足一致性，分区容忍必的系统，通常性能不是特别高。

AP - 满足可用性，分区容忍性的系统，通常可能对一致性要求低一些。

CA 传统Oracle数据库

AP 大多数网站架构的选择

CP Redis、Mongodb

nosql必须实现分区容错性

##  操作redis

插入数据

```
set k1 v1
```

获得数据

```
get k1
```

切换库

```
select 库的下标
```

查看所有key

```
keys *
```

删除

```
FLUSHDB //删除当前库
FLUSHALL//删除全部库
```

## 常用的五大类型

[redis命令参考](http://redisdoc.com/)

String（字符串）：一个二进制安全的String，最大512M

Hash（哈希）：类似java里的Map<String,Object>

List（列表）：是一个了链表

Set（集合）：String类型的无序集合。它是通过HashTable实现实现的

Zset（有序集合）：zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。

### Key常用

判断某个key是否存在

```
exists key的名字
```

移动key到别的库

```
move key db
move key 1
注意：当前库就没有了，被移除了 
```

为给定的key设置过期时间

```
expire key的名字 秒钟
```

 查看还有多少秒过期

```
ttl key的名字
-1表示永不过期，-2表示已过期
过期自动删除
```

查看你的key是什么类型

```
type key的名字
```

### String常用

设置/获取/删除/长度

```
set/get/del/strlen key的名字
```

追加

```
append key的名字 追加的字符串
```

递增/递减

```
Incr/decr key的名字
一定要是数字才能进行加减
```

多步递增/多步递减

```
incrby/decrby key的名字 步长
一定要是数字才能进行加减
```

获取下表/设置下表开始的

```
getrange key的名字 开始下标 结束下标（-1表示全部）/setrange key的名字 开始下标  替换的字符
setrange会把下标开始后的替换字符长度的字符覆盖掉
```

创建key的时候直接设置过期时间

```
setex key的名字 秒钟 value的值
```

创建的时候先判断是否存在，不存在，再创建

```
setnx key的名字 value的值
```

 设置多个值/获得多个值/判断后再设置

```
mset key的名字1 value的值1 key的名字2 value的值2 key的名字3 value的值3
mget key的名字1 key的名字2 key的名字2
msetnx key的名字1 value的值1 key的名字2 value的值2 key的名字3 value的值3//有一个不成功，一起不成功
```

### List常用

从左添加/从右添加/从左读起

```
lpush/rpush key的名字 value1 value2……
lrange key的名字 起始下标 结束下标 
```

栈顶先出/栈底先出

```
lpop/rpop key的名字
```

按照索引下标获得元素(从上到下)

```
lindex key的名字 下标
```

list的长度

```
llen key的名字
```

删N个value

```
lrem key的名字 几个 什么值
例子： lrem list 2 3//删除list里面的2个3（从左往右开始）
```

 截取指定范围的值后再赋值给key

```
ltrim key的名字 开始下标 结束下标
```

 从源列表的右边取一个放到目的列表的左边

```
rpoplpush 源列表 目的列表
```

将数组中的下标的值替换为新值

```
lset key的名字 下标 值
```

在列表中的值的前面/后面插入新值

```
linsert key  before/after 列表中的值 插入的值
```

性能总结

它是一个字符串链表，left、right都可以插入添加；

如果键不存在，创建新的链表；

如果键已存在，新增内容；

如果值全移除，对应的键也就消失了。

链表的操作无论是头和尾效率都极高，但假如是对中间元素进行操作，效率就很低了。

### Set常用

 sadd/smembers/sismember

```
sadd key的名称 值 值 值  //会自动去重
smembers key的名称//读取值
sismember key的名称 值//判断是否存在值
```

获取集合里面的元素个数

```
scard key的名字
```

 删除集合中指定的元素

```
srem key的名字 value
```

 随机出几个数

```
srandmember key的名字 几个
```

随机出栈

```
spop key的名字 
```

 将key1里的某个值赋给key2

```
smove key1 key2 在key1里某个值 
```

key2比key1少的元素（差集）

```
sdiff key1的名字 key2的名字
```

交集

```
sinter key1的名字 key2的名字
```

并集

```
sunion key1的名字 key2的名字
```

### Hash常用

  **hset/hget/hmset/hmget/hgetall/hdel**

```
hset key的名字 对象的key的名字 对象的值//创建
hget key的名字 对象的key的名字//获取
hmset key的名字 对象的key的名字1 对象的值1 对象的key的名字2 对象的值2……
hmget key的名字 对象的key的名字1 对象的key的名字2……
hgetall key的名字//一次性获得所有对象的值
hdel key的名字//删除
```

key里面的对象有几个key值

```
hlen key的名字
```

判断key的对象里有没有某个对象key

```
hexists key的名字 某个对象key
```

**得到对象的所有key/得到对象的所有value**

```
hkeys/hvals key的名字
```

给对象的某个key增加

```
hincrby key的名字 对象的key 增长幅度（整数）/hincrbyfloat key的名字 对象的key 增长幅度（小数）
```

判断对象里是否有某个值，没有则添加

```
hsetnx key的名字 对象的key 对象的值
```

### Zset常用

 zadd/zrange

```
zadd key的名称 分数 值1 分数 值2……//添加
zrange key的名称 开始下标 结束下标//只查看值
zrange key的名称 开始下标 结束下标 withscores//包含所有的分数和值
```

 分数范围内的值

```
zrangebyscore key的名字 开始score 结束score
zrangebyscore key的名字 开始score 结束score withscores//包含所有的分数和值
zrangebyscore key的名字 （开始score （结束score//‘（’表示不包含
zrangebyscore key的名字 开始score 结束score limit 开始下标 几个//在查出来的结果内在截取
```

删除元素

```
 zrem key 某score下对应的value值
```

 统计个数/获取下标/获取分数

```
zcard key的名字//结果是只有值的个数，不包含分数
zcount key的名字 开始分数 结束分数//结果是只有值的个数，不包含分数
zrank key的名字 values值//获取values值对应的下标
zscore key的名字 values的值//获得对应的分数
```

 逆序获得下标值

```
zrevrank key values值
```

逆序读取全部值

```
zrevrange key的名字
```

逆序获得分数范围内的值

```
zrevrangebyscore  key 结束score 开始score
```

