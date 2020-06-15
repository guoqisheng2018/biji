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

## 获取config的配置的信息

```
config get config文件里的变量名
# The working directory.
#
# The DB will be written inside this directory, with the filename specified
# above using the 'dbfilename' configuration directive.
#
# The Append Only File will also be created inside this directory.
#
# Note that you must specify a directory here, not a file name.
dir ./
config get dir//获取启动的config的路径
```

## 配置文件

### Units单位

```
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes
#
# units are case insensitive so 1GB 1Gb 1gB are all the same.//大小写不敏感
```

### 以后台进程运行

```
# By default Redis does not run as a daemon. Use 'yes' if you need it.
# Note that Redis will write a pid file in /var/run/redis.pid when daemonized.
daemonize no    改为yes
```

### 写入pid文件地址

```
# If a pid file is specified, Redis writes it where specified at startup
# and removes it at exit.
#
# When the server runs non daemonized, no pid file is created if none is
# specified in the configuration. When the server is daemonized, the pid file
# is used even if not specified, defaulting to "/var/run/redis.pid".
#
# Creating a pid file is best effort: if Redis is not able to create it
# nothing bad happens, the server will start and run normally.
pidfile /var/run/redis_6379.pid//当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis_6379.pid文件
```

### 更改端口号

```
# Accept connections on the specified port, default is 6379 (IANA #815344).
# If port 0 is specified Redis will not listen on a TCP socket.
port 6379
```

### 设置tcp的backlog

backlog其实是一个连接队列，backlog队列总和=未完成三次握手队列 + 已经完成三次握手队列。

在高并发环境下你需要一个高backlog值来避免慢客户端连接问题。注意Linux内核会将这个值减小到/proc/sys/net/core/somaxconn的值，所以需要确认增大somaxconn和tcp_max_syn_backlog两个值

来达到想要的效果

```
# TCP listen() backlog.
#
# In high requests-per-second environments you need an high backlog in order
# to avoid slow clients connections issues. Note that the Linux kernel
# will silently truncate it to the value of /proc/sys/net/core/somaxconn so
# make sure to raise both the value of somaxconn and tcp_max_syn_backlog
# in order to get the desired effect.
tcp-backlog 511  一般保持不动
```

### 端口的绑定

```
# By default, if no "bind" configuration directive is specified, Redis listens
# for connections from all the network interfaces available on the server.
# It is possible to listen to just one or multiple selected interfaces using
# the "bind" configuration directive, followed by one or more IP addresses.
#
# Examples:
#
# bind 192.168.1.100 10.0.0.1
# bind 127.0.0.1 ::1
bind 0.0.0.0
```

### 设置过期时间

```
# Close the connection after a client is idle for N seconds (0 to disable)
timeout 0  //0代表不关闭
```

### 设置keepalive检测

```
# TCP keepalive.
#
# If non-zero, use SO_KEEPALIVE to send TCP ACKs to clients in absence
# of communication. This is useful for two reasons:
#
# 1) Detect dead peers.
# 2) Take the connection alive from the point of view of network
#    equipment in the middle.
#
# On Linux, the specified value (in seconds) is the period used to send ACKs.
# Note that to close the connection the double of the time is needed.
# On other kernels the period depends on the kernel configuration.
#
# A reasonable value for this option is 300 seconds, which is the new
# Redis default starting with Redis 3.2.1.
tcp-keepalive 300 //单位为秒，如果设置为o则不进行keepalive检测，建议设置成60秒
```

### 设置日志级别

```
# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
loglevel notice //根据开发阶段来调，从上到下，级别越来越高，日志越来越少
```

### 设置日志的名字

```
# Specify the log file name. Also the empty string can be used to force
# Redis to log on the standard output. Note that if you use standard
# output for logging but daemonize, logs will be sent to /dev/null
logfile ""
```

### 系统日志的开关

```
# To enable logging to the system logger, just set 'syslog-enabled' to yes,
# and optionally update the other syslog parameters to suit your needs.
# syslog-enabled no  //是否把日志输出到syslog中

# Specify the syslog identity.
# syslog-ident redis//指定syslog里的日志标志

# Specify the syslog facility. Must be USER or between LOCAL0-LOCAL7.
# syslog-facility local0//指定syslog设备，值可以是USER或LOCAL0-LOCAL7
```

### 设置一共有多少个库

```
# Set the number of databases. The default database is DB 0, you can select
# a different one on a per-connection basis using SELECT <dbid> where
# dbid is a number between 0 and 'databases'-1
databases 16
```

### 指定多久内满足条件更新

```
# Save the DB on disk:
#
#   save <seconds> <changes>
#
#   Will save the DB if both the given number of seconds and the given
#   number of write operations against the DB occurred.
#
#   In the example below the behaviour will be to save:
#   after 900 sec (15 min) if at least 1 key changed
#   after 300 sec (5 min) if at least 10 keys changed
#   after 60 sec if at least 10000 keys changed
#
#   Note: you can disable saving completely by commenting out all "save" lines.
#
#   It is also possible to remove all the previously configured save
#   points by adding a save directive with a single empty string argument
#   like in the following example:
#
#   save ""//禁用

save 900 1 //900秒内有1条更新就保存
save 300 10 //300秒内有10条更新就保存
save 60 10000//60秒内有10000条更新就保存
```

### 存储至本地数据库时是否压缩数据

```
# Compress string objects using LZF when dump .rdb databases?
# For default that's set to 'yes' as it's almost always a win.
# If you want to save some CPU in the saving child set it to 'no' but
# the dataset will likely be bigger if you have compressible values or keys.
rdbcompression yes //指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
```

### 压缩校验

```
# Since version 5 of RDB a CRC64 checksum is placed at the end of the file.
# This makes the format more resistant to corruption but there is a performance
# hit to pay (around 10%) when saving and loading RDB files, so you can disable it
# for maximum performances.
#
# RDB files created with checksum disabled have a checksum of zero that will
# tell the loading code to skip the check.
rdbchecksum yes  //使用CRC64算法校验
```

### 读取的保存在本地数据的文件名

```
# The filename where to dump the DB
dbfilename dump.rdb  //shutdown服务的时候会自动保存rdb文件
//如果设置了一个比较重要的值需要立马保存到rdb文件中可以调用save命令或者bgsave
//Save：save时只管保存，其它不管，全部阻塞
//BGSAVE：Redis会在后台异步进行快照操作，快照同时还可以响应客户端请求。可以通过lastsave命令获取最后一次成功执行快照的时间
```

### 设置密码

```
# Require clients to issue AUTH <PASSWORD> before processing any other
# commands.  This might be useful in environments in which you do not trust
# others with access to the host running redis-server.
#
# This should stay commented out for backward compatibility and because most
# people do not need auth (e.g. they run their own servers).
#
# Warning: since Redis is pretty fast an outside user can try up to
# 150k passwords per second against a good box. This means that you should
# use a very strong password otherwise it will be very easy to break.
#
# requirepass foobared //更改密码的值，如果要访问需要先输入AUTH PASSWORD的值

# Command renaming.
#
# It is possible to change the name of dangerous commands in a shared
# environment. For instance the CONFIG command may be renamed into something
# hard to guess so that it will still be available for internal-use tools
# but not available for general clients.
#
# Example:
#
# rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52
#
# It is also possible to completely kill a command by renaming it into
# an empty string:
#
# rename-command CONFIG ""
#
# Please note that changing the name of commands that are logged into the
# AOF file or transmitted to replicas may cause problems.
```

### 最大连接数

```
# Set the max number of connected clients at the same time. By default
# this limit is set to 10000 clients, however if the Redis server is not
# able to configure the process file limit to allow for the specified limit
# the max number of allowed clients is set to the current file limit
# minus 32 (as Redis reserves a few file descriptors for internal uses).
#
# Once the limit is reached Redis will close all the new connections sending
# an error 'max number of clients reached'.
#
# maxclients 10000  //设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
```

### 最大缓存

```
# WARNING: If you have replicas attached to an instance with maxmemory on,
# the size of the output buffers needed to feed the replicas are subtracted
# from the used memory count, so that network problems / resyncs will
# not trigger a loop where keys are evicted, and in turn the output
# buffer of replicas is full with DELs of keys evicted triggering the deletion
# of more keys, and so forth until the database is completely emptied.
#
# In short... if you have replicas attached it is suggested that you set a lower
# limit for maxmemory so that there is some free RAM on the system for replica
# output buffers (but this is not needed if the policy is 'noeviction').
#
# maxmemory <bytes> 设置最大字节数
```

### 缓存的移除策略

```
# MAXMEMORY POLICY: how Redis will select what to remove when maxmemory
# is reached. You can select among five behaviors:
#
# volatile-lru -> Evict using approximated LRU among the keys with an expire set.//使用LRU算法移除key，只对设置了过期时间的键
# allkeys-lru -> Evict any key using approximated LRU.//使用LRU算法移除key
# volatile-lfu -> Evict using approximated LFU among the keys with an expire set.////使用LFU算法移除key，只对设置了过期时间的键
# allkeys-lfu -> Evict any key using approximated LFU.//使用LFU算法移除key
# volatile-random -> Remove a random key among the ones with an expire set.//在过期集合中移除随机的key，只对设置了过期时间的键
# allkeys-random -> Remove a random key, any key.//移除随机的key
# volatile-ttl -> Remove the key with the nearest expire time (minor TTL)//移除那些TTL值最小的key，即那些最近要过期的key
# noeviction -> Don't evict anything, just return an error on write operations.//不进行移除。针对写操作，只是返回错误信息
#
# LRU means Least Recently Used//最近最少使用
# LFU means Least Frequently Used//最少使用
#
# Both LRU, LFU and volatile-ttl are implemented using approximated
# randomized algorithms.
#
# Note: with any of the above policies, Redis will return an error on write
#       operations, when there are no suitable keys for eviction.
#
#       At the date of writing these commands are: set setnx setex append
#       incr decr rpush lpush rpushx lpushx linsert lset rpoplpush sadd
#       sinter sinterstore sunion sunionstore sdiff sdiffstore zadd zincrby
#       zunionstore zinterstore hset hsetnx hmset hincrby incrby decrby
#       getset mset msetnx exec sort
#
# The default is:
#
# maxmemory-policy noeviction//默认永不过期
```

### 设置样本数量

```
# The default of 5 produces good enough results. 10 Approximates very closely
# true LRU but costs more CPU. 3 is faster but not very accurate.
#
# maxmemory-samples 5  //默认5个，选3个会快但是精确度下降
```

### AOF的配置策略

```
# AOF and RDB persistence can be enabled at the same time without problems.
# If the AOF is enabled on startup Redis will load the AOF, that is the file
# with the better durability guarantees.
#
# Please check http://redis.io/topics/persistence for more information.

appendonly no  //yes打开aof的持久化

# The name of the append only file (default: "appendonly.aof")

appendfilename "appendonly.aof"//默认的名字，一般不改

# The fsync() call tells the Operating System to actually write data on disk
# instead of waiting for more data in the output buffer. Some OS will really flush
# data on disk, some other OS will just try to do it ASAP.
#
# Redis supports three different modes:
#
# no: don't fsync, just let the OS flush the data when it wants. Faster.
# always: fsync after every write to the append only log. Slow, Safest.
# everysec: fsync only one time every second. Compromise.
#
# The default is "everysec", as that's usually the right compromise between
# speed and data safety. It's up to you to understand if you can relax this to
# "no" that will let the operating system flush the output buffer when
# it wants, for better performances (but if you can live with the idea of
# some data loss consider the default persistence mode that's snapshotting),
# or on the contrary, use "always" that's very slow but a bit safer than
# everysec.
#
# More details please check the following article:
# http://antirez.com/post/redis-persistence-demystified.html
#
# If unsure, use "everysec".

# appendfsync always //同步持久化 每次发生数据变更会被立即记录到磁盘  性能较差但数据完整性比较好
appendfsync everysec //出厂默认推荐，异步操作，每秒记录   如果一秒内宕机，有数据丢失
# appendfsync no  //一般不用


```



## 持久化

### RDB

#### rdb文件损坏修复

```
使用redis-check-dump进行修复
redis-check-dump --fix dump.rdb
```

是一个临时的数据文件（前一次保存的备份）

#### 优势：

1对数据完整性和一致性要求不高

2对数据完整性和一致性要求不高

#### 劣势：

1在一定间隔时间做一次备份，所以如果redis意外down掉的话，就会丢失最后一次快照后的所有修改

2.fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑

### AOF

是一个日志文件记载了redis的写命令，只可追加不可改写

网络等丢包问题导致aof文件损坏的修补办法

```
使用redis-check-aof进行修复
redis-check-aof --fix appendonly.aof
```

注意：aof和rdb可以同时存在，当aof和rdb共同存在时，redis重启的时候会优先载入AOF文件来恢复原始的数据，因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整

#### 优势

1.每修改同步：appendfsync always   同步持久化 每次发生数据变更会被立即记录到磁盘  性能较差但数据完整性比较好

2.每秒同步：appendfsync everysec    异步操作，每秒记录   如果一秒内宕机，有数据丢失

3.不同步：appendfsync no   从不同步

#### 劣势

1.相同数据集的数据而言aof文件要远大于rdb文件，恢复速度慢于rdb

2.aof运行效率要慢于rdb,每秒同步策略效率较好，不同步效率和rdb相同

#### 重写

触发机制：Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发

```
# If you have latency problems turn this to "yes". Otherwise leave it as
# "no" that is the safest pick from the point of view of durability.

no-appendfsync-on-rewrite no//重写时是否可以运用Appendfsync，用默认no即可，保证数据安全性。

# Automatic rewrite of the append only file.
# Redis is able to automatically rewrite the log file implicitly calling
# BGREWRITEAOF when the AOF log size grows by the specified percentage.
#
# This is how it works: Redis remembers the size of the AOF file after the
# latest rewrite (if no rewrite has happened since the restart, the size of
# the AOF at startup is used).
#
# This base size is compared to the current size. If the current size is
# bigger than the specified percentage, the rewrite is triggered. Also
# you need to specify a minimal size for the AOF file to be rewritten, this
# is useful to avoid rewriting the AOF file even if the percentage increase
# is reached but it is still pretty small.
#
# Specify a percentage of zero in order to disable the automatic AOF
# rewrite feature.

auto-aof-rewrite-percentage 100//一倍（百分比）
auto-aof-rewrite-min-size 64mb
```

## 事务

redis对事务是部分支持

### 常用命令

multi 标记一个事务块的开始

discard 取消事务

exec 执行所有事务块的命令

unwatch 取消watch命令对所有key的监视

watch  key [key key……] 监视一个或多个key，如果执行之前这个或这些key被其他命令所改动，那么事务被打断

### 运行结果

1.正常运行  先用multi标记事务开始，再用exec提交事务 结果：全部提交成功

2.放弃事务 先用multi标记事务开始，再用discard取消事务 结果：取消事务后没执行

3.全体连坐 先用multi标记事务开始，中间有个编译错误（例：不正确的命令），再用exec提交事务 结果：有一个执行错误，则全部提交失败

4.冤头债主 先用multi标记事务开始，中间有个运行错误（例：将字符用Incr/decr递增递减），再用exec提交事务 结果：执行错误的提交失败，其他提交成功

5.watch监控（疑问：有人先上锁，后另一人解锁，因为解锁是取消所有key的监视）

悲观锁：类似于表锁

乐观锁：实际上并不会上锁，但是会有个版本号，比对版本号不同则回退，重新修改

先用watch key给key上锁（类似于乐观锁），然后用multi标记事务开始，然后修改要改的内容，再用exec提交事务

结果一：已经被改过，使用unwatch解锁后，重新上锁，重新改值

结果二：执行成功

## 消息订阅（一般不用）

### 常用命令

psubscribe pattern[pattern pattern……] 订阅一个或多个符合给定模式的频道

pubsub subcommand [argument[argument……]]  查看订阅与发布系统状态

publish channel message  将消息发送到指定的频道

punsubscribe [pattern[pattern pattern……]] 退订所给定模式的频道

subscribe channel[channel……] 订阅给定的一个或多个频道的信息

unsubscribe [channel[channel……]]. 指退订给定的频道

## 主从复制

查看权限

```
info replication
```

从库配置命令

```
slaveof 主库ip 主库端口
每次与master断开之后，都需要重新连接，除非配置进redis.conf文件
```

一主二从

```
从机会直接复制主机的全部数据
只有主机可以写，从机只可以读
主机断开后，从机原地待命，主机断开后重新连接，从机可以续接上，从机挂了，无法与主机续接上，需要重新连接
```

薪火相传（避免中心化太严重）

```
从机挂从机，显示的身份还是从机
传递可以完成
可以减轻主机压力
```

反客为主

```
从机执行成为新主机的命令
SLAVEOF no one
```

### 复制的原理

1.slave启动成功连接到master后会发送一个sync命令

2.Master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行完毕之后，master将传送整个数据文件到slave,以完成一次完全同步

3.全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。

4.增量复制：Master继续将新的所有收集到的修改命令依次传给slave,完成同步

5.但是只要是重新连接master,一次完全同步（全量复制)将被自动执行

### 哨兵模式

反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库

1./myredis目录下新建sentinel.conf文件

2.在文件里编写

```
 sentinel monitor 被监控数据库名字(自己起名字) 主机ip 主机端口 1
 sentinel monitor host6379 127.0.0.1 6379 1
 上面最后一个数字1，表示主机挂掉后salve投票看让谁接替成为主机，得票数多少后成为主机
```

3.启动哨兵

```
redis-sentinel /myredis/sentinel.conf 
```

注意：原主机回来会变成从机

### 复制的缺点

由于所有的写操作都是先在Master上操作，然后同步更新到Slave上，所以从Master同步到Slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，Slave机器数量的增加也会使这个问题更加严重。

