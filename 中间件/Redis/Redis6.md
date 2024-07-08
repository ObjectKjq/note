## NoSQL数据库简介

关闭服务

net stop redis

启动服务

net start redis

### NOSQL数据库

![](./图片/Snipaste_2022-04-04_18-17-37.png)

![](./图片/Snipaste_2022-04-04_18-25-52.png)

1、onsql能减小cpu和io的压力

![](./图片/Snipaste_2022-04-04_18-27-06.png)

2、作为缓存使用增加访问速度

NoSQL不支持原子性，一致性，隔离性，持久性ACID，远超sql性能

。对数据高并发的读取

。海量数据的读取

。对数据高可扩展性的

不适应的场景

。需要事务支持

。基于sql的结构化查询存储，处理复杂的关系

![](./图片/Snipaste_2022-04-04_19-06-01.png)

### 行式存储数据库

![](./图片/Snipaste_2022-04-04_19-07-36.png)

![](./图片/Snipaste_2022-04-04_19-08-07.png)

## Redis6概述和安装

Redis是一个开源的key-value存储系统。

这些数据类型都支持push/pop, add/remove及取交集并集和差集极丰富的操作，而这些操作都是原子性的

redis会周期性的把更新的数据写入到磁盘中

可以做session的共享到不同的服务器

![](./图片/Snipaste_2022-04-04_19-16-36.png)

Resis的安装

https://redis.io/download/在官网中下载redis

安装以后里面的文件功能

![](./图片/Snipaste_2022-04-04_19-41-04.png)

redis-cli.exe:启动redis数据库

默认端口：6379。从何而来的

默认右16个系统数据库，类似数组下标从0开始，初始默认使用0号数据库

1、redis与memcached区别

、redis能够持久化存储

、redis能够存储的数据类型比较多

、redis式单线程+多路IO复用（举例：当同时获取数据时，线程不会等待，会继续执行下面的操作。当获取到数据时才执行现在的操作）。memcache是多线程+锁

## 常用五大数据类型

redis键key的操作

![](./图片/Snipaste_2022-04-04_20-18-44.png)

![](./图片/Snipaste_2022-04-04_20-19-26.png)

### redis字符串String

![](./图片/Snipaste_2022-04-04_20-27-33.png)

![](./图片/Snipaste_2022-04-04_20-28-00.png)

![](./图片/Snipaste_2022-04-04_20-29-45.png)

当set设置相同的key，就会覆盖掉上一个value。setnx当key存在就会设置失败。



![](./图片/Snipaste_2022-04-04_20-33-14.png)

![](./图片/Snipaste_2022-04-04_20-34-28.png)

原子性操作：就是指不会被线程调度机制打断的操作。通俗讲就是线程之间操作不受影响，就好像是加上了锁

![](./图片/Snipaste_2022-04-04_20-55-20.png)

![](./图片/Snipaste_2022-04-04_20-56-30.png)

![](./图片/Snipaste_2022-04-04_20-58-45.png)

内部结构类似于java中的ArraList，最大不能超过512mb

### redis列表list

是一个单键多值

![](./图片/Snipaste_2022-04-04_21-06-13.png)

常用的命令操作

![](./图片/Snipaste_2022-04-04_21-07-29.png)

取值lrange <key> <start> <stop>按照索引小标获得元素(从左到右), 如果是lrange mylist 0 -1 0左边第一个，-1右边第一个（表示获取所有）

![](./图片/Snipaste_2022-04-04_21-17-31.png)

**List底层的数据结构**

quickList快速链表

![](./图片/Snipaste_2022-04-04_21-25-19.png)

### redis集合set

与List主要的区别是，无序的并且不可重复

![](./图片/Snipaste_2022-04-04_21-28-07.png)

常见操作

![](./图片/Snipaste_2022-04-04_21-29-30.png)

![](./图片/Snipaste_2022-04-04_21-32-58.png)

### redis哈希Hash

![](./图片/Snipaste_2022-04-04_21-36-25.png)

多用于存储对象

key			value

user		field	value

​				id		1

​				name	张三

![](./图片/Snipaste_2022-04-04_21-43-24.png)

  常用的命令

![](./图片/Snipaste_2022-04-04_21-43-52.png)

 ```redis
hset user id value
hset user name zhangsan
 ```

![](./图片/Snipaste_2022-04-04_21-50-08.png)

### redis有序集合Zset 

![](./图片/Snipaste_2022-04-05_08-05-30.png)

zset常用的命令

![](./图片/Snipaste_2022-04-05_08-06-58.png)

![](./图片/Snipaste_2022-04-05_08-11-23.png)

![](./图片/Snipaste_2022-04-05_08-12-32.png)

底层结构原理

hash			field		value

​					java		100

​					php		200

![](./图片/Snipaste_2022-04-05_08-15-43.png)

![](./图片/Snipaste_2022-04-05_08-18-02.png)

![](./图片/Snipaste_2022-04-05_08-18-20.png)

## Redis6配置文件详解

INCLUDES :表示包含的意思

NETWORK：网络配置

​	bind 127.0.0.1------表示只能通过本地访问

​	protected-mode yes开启保护模式，表示只能通过本机访问

​	port 6379：表示端口号

​	tcp-backlog 511：设置tcp的backlog，backlog是一个连接队列，backlog队列和=未完成三次握手队列+已经完成三次握手

​	timeout 0：设值连接的超时时间，0表示永不超时

​	tcp-keepalive 0：每隔300秒检测一次连接是否活着，如果活着进行服务

 GENERAL ：一般的配置

​	daemonize yes：是否为后台进程，设置yes

​	pidfile /var/run/redis.pid：每次操作有一个进程号，把每次操作的进程号设置到文件中

​	loglevel notice：表示日志的级别

​		debug (a lot of information, useful for development/testing)

​		verbose (many rarely useful info, but not a mess like the debug level)

​		notice (moderately verbose, what you want in production probably)

​		warning (only very important / critical messages are logged)

​	databases 16：表示一共有16个数据库

SECURITY：安全的配置

​	 requirepass foobared：给数据库设置一个密码

LIMITS 

​	maxclients 10000：设置最大连接数

​	maxmemory <bytes>：内存最大能占多大

![](./图片/Snipaste_2022-04-05_09-06-21.png)

## Redis6的发布和订阅

**是一种消息的通信模式**

### 什么是发布和订阅

redis发布订阅（pub/sub）是一种消息通信模式：发布者（pub）发送消息，订阅者（sub）接收消息

![](./图片/Snipaste_2022-04-05_09-14-27.png)

### 发布订阅命令实现

![](./图片/Snipaste_2022-04-05_09-18-49.png)

## Redis6新数据类型

### Bitmaps

是一个位操作,只能存放0和1

![](./图片/Snipaste_2022-04-05_09-21-56.png)

![](./图片/Snipaste_2022-04-05_09-22-40.png)

例子

![](./图片/Snipaste_2022-04-05_09-26-02.png)

很多用户的id以指数开头（如10000），直接将id和Bitmaps的偏移量对应势必会造成一定的浪费

在一次初始化时，假如偏移量非常大，那么整个初始化会比较慢

去出值

![](./图片/Snipaste_2022-04-05_09-31-07.png)

统计被设置一的数量

![](./图片/Snipaste_2022-04-05_09-32-25.png)

![](./图片/Snipaste_2022-04-05_09-35-23.png)

判断两天都访问的数量

![](./图片/Snipaste_2022-04-05_09-36-38.png)

![](./图片/Snipaste_2022-04-05_09-39-47.png)

### HyperLogLog

统计页面的访问量，基数计算的操作

命令

![](./图片/Snipaste_2022-04-05_09-50-09.png)

![](./图片/Snipaste_2022-04-05_09-52-50.png)

对两个集合合并，放到第三个集合中

![](./图片/Snipaste_2022-04-05_09-53-57.png)

### Geospatial

通过对地理信息的操作

范围-85.05112878到85.05112878

![](./图片/Snipaste_2022-04-05_09-57-36.png)

![](./图片/Snipaste_2022-04-05_09-59-05.png)

![](./图片/Snipaste_2022-04-05_10-01-05.png)

![](./图片/Snipaste_2022-04-05_10-02-29.png)

## Jedis操作Redis6

也可以使用Lettuce驱动操作数据库，springboot默认使用的是Lettuce

java对redis进行操作。连接的时候需要注入，redis的配置文件，和防火墙是否关闭

```java
//创建Jedis对象
Jedis jedis = new Jedis("127.0.0.1", 6379);
//测试
String ping = jedis.ping();
System.out.println(ping);
jedis.close();
```

```java
//创建Jedis对象
Jedis jedis = new Jedis("127.0.0.1", 6379);
//添加
jedis.set("name", "lucy");
//获取
String name = jedis.get("name");
System.out.println(name);
jedis.close();
```

模拟验证码的发送

![](./图片/Snipaste_2022-04-05_11-29-15.png)

```java
@Test
public void test05() {
    //生成6位的随机数
    String code = "";
    Random random = new Random();
    for (int i = 0; i < 6; i++) {
        int i1 = random.nextInt(10);
        code += i1;
    }
    //每个手机每天只能发送三次，设置过期时间
    Jedis jedis = new Jedis("127.0.0.1", 6379);
    //手机发送次数的key，保存验证码的key
    String phoneKey = "12345678901";
    String codeKey = phoneKey + "VerifyCode";
    String s = jedis.get(phoneKey);
    if (s == null){
        System.out.println("第一次发送");
        jedis.setex(phoneKey, 24*60*60, "1");
    }else if (Integer.parseInt(s) <= 2){
        jedis.incr(phoneKey);
    }else if (Integer.parseInt(s) > 2){
        System.out.println("今天的发送次数过多");
    }
    jedis.setex(codeKey, 120, code);
    jedis.close();
}
```

## Redis6与Spring Boot整合

## Redis6事务操作

### redis的事务定义

事务是把各种操作串联到一个直线上，可以防止别的命令插队，是顺序执行的

### Multi、Exec、discard

![](./图片/Snipaste_2022-04-05_14-17-45.png)

### 事务的错误处理

![](./图片/Snipaste_2022-04-05_14-22-24.png)

![](./图片/Snipaste_2022-04-05_14-22-50.png)

![](./图片/Snipaste_2022-04-05_14-23-08.png)

### 事务冲突的问题

![](./图片/Snipaste_2022-04-05_15-35-07.png)

 ![](./图片/Snipaste_2022-04-05_15-36-34.png)

![](./图片/Snipaste_2022-04-05_15-40-22.png)

![](./图片/Snipaste_2022-04-05_15-42-10.png)

乐观锁具体操作

```sql
watch [key] # 去监视某个key
multi # 开启事务
incrty [key] 10 # 给key加10操作，当另一个也加10就会执行失败
```

### Redis事务三特性

![](./图片/Snipaste_2022-04-05_15-50-33.png)

### redis事务秒杀案例

![](./图片/Snipaste_2022-04-05_16-03-07.png)

1、乐观锁会解决多买问题，

2、连接池会解决连接超时问题，

3、可以是用Lua小巧的脚本语言解决商品遗留问题

![](./图片/Snipaste_2022-04-05_16-56-03.png)

![](./图片/Snipaste_2022-04-05_16-56-24.png)

```java
// 使用jedis中的方法去加载LUA脚本字符串
String sha1 = jedis.scriptLoad(secKillScript);
// 传入参数，让lua脚本去执行
OBject result = jedis.evalsha(shal, 2, userid, prodid);
```

## Redis6持久化之RDB

在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的Snapshot快照，它回复时是将快照文件直接读到内存里

![](./图片/Snipaste_2022-04-05_17-13-56.png)

持久化存储是存储到dump.rdb文件中去

SNAPSHOTTING：此配置文件专门负责配置持久化的

![](./图片/Snipaste_2022-04-05_17-31-16.png)

![](./图片/Snipaste_2022-04-05_17-33-41.png)

对数据进行备份

![](./图片/Snipaste_2022-04-05_17-39-09.png)

## Redis6持久化之AOF

![](./图片/Snipaste_2022-04-05_17-40-21.png)

AOF默认是不开启的

appendonly yes：开启

![](./图片/Snipaste_2022-04-05_17-58-04.png)

![](./图片/Snipaste_2022-04-05_18-00-42.png)

no-appendfsync-on-rewrite:

重写压缩操作只有文件大于64mb才触发，

![](./图片/Snipaste_2022-04-05_18-03-38.png)

![](./图片/Snipaste_2022-04-05_18-05-16.png)



![](./图片/Snipaste_2022-04-05_18-06-11.png)

存在不管，造成回复不能



![](./图片/Snipaste_2022-04-05_18-07-49.png)

## Redis6的主从复制

主机数据更新后根据配置和策略，自动同步到备机的master/slaver机制，Master以写为主，Slaver以读为主

![](./图片/Snipaste_2022-04-05_18-13-05.png)

读写分离：是主服务器只负责写的操作，从服务器只负责读的操作

容灾的快速恢复，比如第一台从服务器挂掉，就会切换其他的从服务器。（如果一台主服务器挂掉，可以切换其他集群中的主服务器，主服务器只能有一个）

**创建一主两从的服务器**

![](./图片/Snipaste_2022-04-05_18-34-03.png)

![](./图片/Snipaste_2022-04-05_18-32-34.png)

从机中不能做写操作

如果某台从服务器关掉：

​	在主服务器做添加操作，再次启动关闭的从服务器（这是是一个主服务器，再让他变成从服务器），会把主服务器中的所有数据都复制下来。

如果主服务器关闭：

​	从服务器不会改变，重新启动主服务器，还是一个主服务器

**主从复制的原理**

![](./图片/Snipaste_2022-04-05_18-46-08.png)



**星火相传**

![](./图片/Snipaste_2022-04-05_18-49-36.png)

**反客为主**

slaveof no one，将从机变成主机

如果最大的服务器挂掉，下面的小的主服务器就会变成大哥



**哨兵模式**自动般的反客为主

能够后台监控主机是否故障，如果故障了根据通票数自动将从库转换为主库

一主而从配置哨兵模式：

需要先创建一个配置文件sentinel.conf

在配置文件中只写一句话sentinel monitor mymaster 127.0.0.1 6379 1

其中mymaster为监控对象起的服务名称，1为至少有多少个哨兵同意迁移的数量

启动哨兵：redis-sentinel sentinel.conf

![](./图片/Snipaste_2022-04-05_19-37-38.png)

当主服务挂掉之后，就会再从两个从服务器上选一个当成主服务器，原来的主服务器就会变成从服务器

复制延时：

![](./图片/Snipaste_2022-04-05_19-46-17.png)

选举的规则：

1、优先级高的先选举：在配置文件。新版本已经改变了k-v：replica-priority![](./图片/Snipaste_2022-04-05_19-47-14.png)

2、选择偏移量最大的（偏移量就是谁给主服务器数据同步高）

3、选择runid最小的从服务器![](./图片/Snipaste_2022-04-05_19-52-14.png)

## Redis6集群

### 集群的介绍

当容量不够，redis如何进行扩容

并发写操作，redis如何分离

代理主机：

![](./图片/Snipaste_2022-04-06_07-34-12.png)

无中心化集群

![](./图片/Snipaste_2022-04-06_07-36-31.png)

![](./图片/Snipaste_2022-04-06_07-38-32.png)

### 用无中心化集群搭建

创建6个配置文件：6379，6380，6381，6389，6380，6381

加上这三个配置：cluster-enabled yes 打开集群模式

​							cluster-config-file nodes-6379.conf 设定节点配置文件名

​							cluster-node-timeout 15000 设置节点失联时间，超过改时间（毫秒），集群自动进行主从切换

启动六个节点，把六个节点合成一个集群（注意：老的版本需要装一个环境，新版本不需要装环境）

​	先进入安装目录的src下

![](./图片/Snipaste_2022-04-06_07-55-13.png)

​	-replicas 1 采用最简单的方式配置集群，一台主机，一台从机，正好三组

​	此处不要用127.0.0.1，要用真实的ip地址

用集群方式连接：redis-cli -c -p 6379，用cluster nodes命令查看集群的信息

### 集群操作和故障分配

分配原则

![](./图片/Snipaste_2022-04-06_08-07-56.png)

16384 slots coverred.：slots是什么

![](./图片/Snipaste_2022-04-06_08-09-59.png)

![](./图片/Snipaste_2022-04-06_08-11-39.png)

set k1 value1: 他会根据k1计算k1所在插槽。没法添加多个值，可以通过组添加多个值![](./图片/Snipaste_2022-04-06_08-18-07.png)

常用的命令：

查看key在那个插槽，查看插槽中有对少值，在某个插槽中返回10个值

![](./图片/Snipaste_2022-04-06_08-19-31.png)



**故障恢复**

一台主服务器停止，会自动恢复，挂掉的主机再次启动，会变为从机

![](./图片/Snipaste_2022-04-06_08-27-37.png)

### 集群的Jedis开发

```java
@Test
public void test06() {
    HostAndPort hostAndPort = new HostAndPort("192.168.44.168", 6378);
    //通过6379连上集群
    JedisCluster jedisCluster = new JedisCluster(hostAndPort);
    //进行操作
    jedisCluster.set("b1", "value1");
    String b1 = jedisCluster.get("b1");
    jedisCluster.close();
}
```

集群的好处：实现扩容，分摊压力，无中心配置相对简单

![](./图片/Snipaste_2022-04-06_08-35-07.png)

## Redis6应用问题解决

### 缓存穿透。

遭到黑客攻击，让服务器瘫痪

1、应用服务器压力变大了。2、redis命中率降低。3、一直查询数据库

1、redis查询不到数据库。2、出现很多非正常的url

![](./图片/Snipaste_2022-04-06_09-14-37.png)

常见的四种解决方案

![](./图片/Snipaste_2022-04-06_09-15-59.png)

![](./图片/Snipaste_2022-04-06_09-16-23.png)

![](./图片/Snipaste_2022-04-06_09-17-37.png)

![](./图片/Snipaste_2022-04-06_09-18-32.png)

### 缓存击穿

1、数据库访问压力瞬时增加。2、redis里面没有出现大量key过期3、redis正常运行

1、redis某个key过期，大量访问使用这个key

解决方案

![](./图片/Snipaste_2022-04-06_09-22-31.png)

### 缓存雪崩

1、数据库压力变大，服务器崩溃

![](./图片/Snipaste_2022-04-06_09-29-38.png)

![](./图片/Snipaste_2022-04-06_09-30-53.png)

### 分布式锁

是一个共享锁

锁的类型：1、基于数据库实现分布式锁。2、基于缓存。3、基于Zookeeper每个分布式锁解决方案都有各自的优缺点

1、redis性能高2、zookeeper可靠性高

基于redis实现分布式锁

setnx users 10，就加了一把锁。别人要设置首先要释放，del users删除就释放了，可以设置过期时间，自动释放（最好上锁的时候就设置过期时间set users 10 nx ex 12）



![](./图片/Snipaste_2022-04-06_09-48-21.png)

![](./图片/Snipaste_2022-04-06_09-55-53.png)

使用uuid。set lock uuid nx ex 10。使用uuid表示的不同的操作

释放锁的时候，首先判断当前uuid和要释放锁uuid是否一样



使用lua脚本实现原子性，使用事务

## Redis6新功能
