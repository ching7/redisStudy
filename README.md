# redisStudy

Redis 是什么：`REmote DIctionary Server(远程字典服务器)`，[Redis官网](http://redis.io/)

是完全开源免费的，用C语言编写的，遵守`BSD`协议，是一个高性能的`(key/value)`分布式内存数据库，基于内存运行，并支持持久化的`NoSQL`数据库，是当前最热门的`NoSql`数据库之一,
也被人们称为`数据结构服务器`

## 1 Nosql简介

NoSQL(NoSQL = Not Only SQL )，意即“不仅仅是SQL”，
泛指非关系型的数据库。随着互联网web2.0网站的兴起，传统的关系数据库在应付web2.0网站，特别是超大规模和高并发的SNS类型的web2.0纯动态网站已经显得力不从心，暴露了很多难以克服的问题，而非关系型的数据库则由于其本身的特点得到了非常迅速的发展。NoSQL数据库的产生就是为了解决大规模数据集合多重数据种类带来的挑战，尤其是大数据应用难题，包括超大规模数据的存储。

### 1.1 Nosql的数据模型

KV键值、Bson、列族、图形

#### 1.1.1 KV键值

* 新浪：BerkeleyDB+Redis 

* 美团：redis+tair

* 阿里、百度：memcache+Redis 

#### 1.1.2 Bson

文档型数据库(bson格式比较多)

* CouchDB

* MongoDB

  ~~~properties
  MongoDB 是一个基于分布式文件存储的数据库。由 C++ 语言编写。旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。
   
  MongoDB 是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。
  ~~~

#### 1.1.3 列存储数据库

* Cassandra, HBase
* 分布式文件系统

#### 1.1.4 图关系数据库

它不是放图形的，放的是关系比如:朋友圈社交网络、广告推荐系统、社交网络，推荐系统等。专注于构建关系图谱

* Neo4J
* InfoGrid

## 2 在分布式数据库中CAP原理CAP+BASE

###  2.1 传统数据库ACID

关系型数据库遵循ACID规则
事务在英文中是transaction，和现实世界中的交易很类似，它有如下四个特性

* A (Atomicity) 原子性

  原子性很容易理解，也就是说事务里的所有操作要么全部做完，要么都不做，事务成功的条件是事务里的所有操作都成功，只要有一个操作失败，整个事务就失败，需要回滚。比如银行转账，从A账户转100元至B账户，分为两个步骤：1）从A账户取100元；2）存入100元至B账户。这两步要么一起完成，要么一起不完成，如果只完成第一步，第二步失败，钱会莫名其妙少了100元。

* C (Consistency) 一致性

  一致性也比较容易理解，也就是说数据库要一直处于一致的状态，事务的运行不会改变数据库原本的一致性约束。

* I (Isolation) 独立性

  所谓的独立性是指并发的事务之间不会互相影响，如果一个事务要访问的数据正在被另外一个事务修改，只要另外一个事务未提交，它所访问的数据就不受未提交事务的影响。比如现有有个交易是从A账户转100元至B账户，在这个交易还未完成的情况下，如果此时B查询自己的账户，是看不到新增加的100元的

* D (Durability) 持久性

  持久性是指一旦事务提交后，它所做的修改将会永久的保存在数据库上，即使出现宕机也不会丢失。

### 2.2 分布式数据库CAP

* C:Consistency（强一致性）
* A:Availability（可用性）
* P:Partition tolerance（分区容错性）

CAP理论就是说在分布式存储系统中，最多只能实现上面的两点。
而由于当前的网络硬件肯定会出现延迟丢包等问题，所以，分区容忍性是我们必须需要实现的。所以我们只能在一致性和可用性之间进行权衡，没有NoSQL系统能同时保证这三点。

* CA 传统Oracle数据库

* AP 大多数网站架构的选择

* CP Redis、Mongodb

注意：分布式架构的时候必须做出取舍。一致性和可用性之间取一个平衡。多余大多数web应用，其实并不需要强一致性。因此牺牲C换取P，这是目前分布式数据库产品的方向

### 2.2 BSAE原理

BASE就是为了解决关系数据库强一致性引起的问题而引起的可用性降低而提出的解决方案。

BASE其实是下面三个术语的缩写：

* 基本可用（Basically Available）
* 软状态（Soft state）
* 最终一致（Eventually consistent）

它的思想是通过让 *系统放松对某一时刻数据一致性的要求来换取系统整体伸缩性和性能上改观* 。为什么这么说呢，缘由就在于大型系统往往由于地域分布和极高性能的要求，不可能采用分布式事务来完成这些指标，要想获得这些指标，我们必须采用另外一种方式来完成，这里BASE就是解决这个问题的办法

### 2.3 分布式和集群

分布式系统（distributed system）由多台计算机和通信的软件组件通过计算机网络连接（本地网络或广域网）组成。分布式系统是建立在网络之上的软件系统。正是因为软件的特性，所以分布式系统具有高度的内聚性和透明性。因此，网络和分布式系统之间的区别更多的在于高层软件（特别是操作系统），而不是硬件。分布式系统可以应用在在不同的平台上如：Pc、工作站、局域网和广域网上等。

* 分布式：不同的多台服务器上面部署不同的服务模块（工程），他们之间通过`Rpc/Rmi`之间通信和调用，对外提供服务和组内协作。

* 集群：不同的多台服务器上面部署相同的服务模块，通过分布式调度软件进行统一的调度，对外提供服务和访问。

## 3 Redis 入门

### 3.1 Redis 特点

Redis 与其他 key - value 缓存产品有以下三个特点

* Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用
* Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储
* Redis支持数据的备份，即master-slave模式的数据备份

### 3.2 Redis 应用

* 内存存储和持久化：Redis 支持异步将内存中的数据写到硬盘上，同时不影响继续服务
* 取最新N个数据的操作，如：可以将最新的10条评论的ID放在Redis的List集合里面
* 模拟类似于HttpSession这种需要设定过期时间的功能
* 发布、订阅消息系统
* 定时器、计数器

### 3.3 Redis 安装

* 官网下载获得redis-5.0.7.tar.gz后将它放入我们的 home/chenyn/redis目录下

  ~~~cmake
  #解压 
  tar -zxvf redis-5.0.7.tar.gz
  # 进入解压目录
  cd redis-5.0.7
  # 编译安装
  make && make install
  ~~~

* 运行make命令时故意出现的错误解析：

  ~~~cmake
  # 出现 gcc未识别 
  yum install gcc-c++
  
  # Jemalloc/jemalloc.h：没有那个文件或目录
  运行make distclean之后再make
  ~~~

* 默认安装目录 /usr/local/bin

  

  ~~~cmake
  # 目录简介
  Redis-benchmark:性能测试工具，可以在自己本子运行，看看自己本子性能如何-服务启动起来后执行
  Redis-check-aof：修复有问题的AOF文件，rdb和aof后面讲
  Redis-check-dump：修复有问题的dump.rdb文件
  Redis-cli：客户端，操作入口
  Redis-sentinel：redis集群使用
  Redis-server：Redis服务器启动命令
  ~~~

  

### 3.4 Redis 启动和HelloWorld

* 修改redis.conf文件将里面的daemonize no 改成 yes，让服务在后台启动

* 将默认的redis.conf备份

  ~~~cmake
  # 启动
  cd /usr/local/bin
  redis-server /home/chenyn/redis/redis-5.0.7/redis.conf
  
  # 测试连通
  redis-cli
  ping 
  ~~~

* HelloWorld

  ![](/image/redis.jpg)

* 关闭redis

  ~~~cmake
  # 单实例关闭
  redis-cli shutdown
  # 多实例关闭，指定端口关闭
  redis-cli -p 6379 shutdown
  ~~~

### 3.5 Redis 拓展知识

* 单进程模型来处理客户端的请求。对读写等事件的响应

  是通过对epoll函数的包装来做到的。Redis的实际处理速度完全依靠主进程的执行效率，

  Epoll是Linux内核为处理大批量文件描述符而作了改进的epoll，是Linux下多路复用IO接口select/poll的增强版本，它能显著提高程序在大量并发连接中只有少量活跃的情况下的系统CPU利用率。

* 默认16个数据库，类似数组下表从零开始，初始默认使用零号库

* Select命令切换数据库

  ~~~cmake
  # 选择库
  Select 0-15 
  ~~~

* Dbsize查看当前数据库的key的数量

  ~~~cmake
  # 查看key数量
  keys *
  ~~~

* Flushdb：清空当前库

* Flushall；通杀全部库

* Redis索引都是从零开始

* 为什么默认端口是6379

## 4  Redis 数据类型

### 4.1 Redis 五种数据类型

* String（字符串）

  * string是redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value。

  * string类型是二进制安全的。意思是redis的string可以包含任何数据。比如jpg图片或者序列化的对象 。

  * string类型是Redis最基本的数据类型，一个redis中字符串value最多可以是512M

* Hash（哈希，类似java里的Map）

  * Redis hash 是一个键值对集合。
  * Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。类似Java里面的Map<String,Object>

* List（列表）

  Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素导列表的头部（左边）或者尾部（右边）。它的底层实际是个链表

* Set（集合）

  Redis的Set是string类型的无序集合。它是通过HashTable实现实现的，

* Zset(sorted set：有序集合)

  Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
  不同的是每个元素都会关联一个double类型的分数。
  redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。

## 5 Redis 常用命令

Redis 常用的命令结合5+1，实际操作

5：五种数据类型，1：Redis 键(key)

### 5.1  Redis 键(key)

~~~cmake
# 查看当前库所有key
keys *
# 判断某个key是否存在
exists key
# 当前库就没有了，被移除了
move key db
# 为给定的key设置过期时间
expire key 秒钟：
# 查看还有多少秒过期，-1表示永不过期，-2表示已过期
ttl key 
# 查看你的key是什么类型
type key 
# 删除某个key
del key
~~~

### 5.2 Redis字符串(String)

单值单value

~~~cmake
# 增/查/删/末尾添加/key长度
set/get/del/append/strlen key
 
# 一定要是数字才能进行加减
Incr/decr/incrby/decrby  key

# getrange:获取指定区间范围内的值，类似between......and的关系
# setrange设置指定区间范围内的值，格式是setrange key值 具体值
getrange/setrange

# setex:设置带过期时间的key，动态设置。
# setex 键 秒值 真实值
# setnx:只有在 key 不存在时设置 key 的值。
setex(set with expire)键秒值/setnx(set if not exist)

# mset:同时设置一个或多个 key-value 对。
# mget:获取所有(一个或多个)给定 key 的值
# msetnx:同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。
mset/mget/msetnx 

#getset:将给定 key 的值设为 value ，并返回 key 的旧值(old value)。
#简单一句话，先get然后立即set
getset
~~~

### 5.3 Redis列表(List)

单值多value

~~~cmake
# 左新增/右新增/查询
lpush/rpush/lrange
# 删除-左出栈-右出栈 
lpop/rpop
# 按照索引下标获得元素(从上到下)
lindex index
# 数组长度
llen key
# 移除列表的最后一个元素，并将该元素添加到另一个列表并返回
rpoplpush 源列表 目的列表
# 从left往right删除2个值等于v1的元素，返回的值为实际删除的数量
# LREM list3 0 值，表示删除全部给定的值。零个就是全部值
lrem key 删N个value
# 替换某个下标对应的值-从左边计数
lset key index value
# 在list某个已有值的前后再添加具体值
linsert key  before/after 值1 值2
~~~

### 5.4 Redis集合(Set)

单值多value

~~~cmake
# 新增/查看/如果成员元素是集合的成员，返回 1 。 如果成员元素不是集合的成员，或 key 不存在，返回 0 。
sadd/smembers/sismember
# 获取集合里面的元素个数
scard
~~~

### 5.5  Redis哈希(Hash)

KV模式不变，但V是一个键值对

参考 [Redis命令](https://www.runoob.com/redis/redis-commands.html)

### 5.6 Redis有序集合Zset(sorted set)

在set基础上，加一个score值。之前set是k1 v1 v2 v3，现在zset是k1 score1 v1 score2 v2

参考 [Redis命令](https://www.runoob.com/redis/redis-commands.html)

## 6 Redis配置文件（redis.conf）

配置文件在解压包根目录

[配置文件解析参考](https://www.runoob.com/redis/redis-conf.html)

## 7 Java连接测试

注意配置文件调整

~~~cmake
# 关闭Redis服务保护模式
protected-mode no
# 注释掉本机,局域网内的所有计算机都能访问.
band localhost   只能本机访问,局域网内计算机不能访问
~~~

测试连通代码

~~~java
public static void main(String[] args) {

        Jedis jedis = new Jedis("192.168.209.128",6379);
        //key
        Set<String> keys = jedis.keys("*");
        for (Iterator iterator = keys.iterator(); iterator.hasNext();) {
            String key = (String) iterator.next();
            System.out.println(key);
        }
        System.out.println("jedis.exists====>"+jedis.exists("k2"));
        System.out.println(jedis.ttl("k1"));
        //String
        //jedis.append("k1","myreids");
        System.out.println(jedis.get("k1"));
        jedis.set("k4","k4_redis");
        System.out.println("----------------------------------------");
        jedis.mset("str1","v1","str2","v2","str3","v3");
        System.out.println(jedis.mget("str1","str2","str3"));
        //list
        System.out.println("----------------------------------------");
        //jedis.lpush("mylist","v1","v2","v3","v4","v5");
        List<String> list = jedis.lrange("mylist",0,-1);
        for (String element : list) {
            System.out.println(element);
        }
        //set
        jedis.sadd("orders","jd001");
        jedis.sadd("orders","jd002");
        jedis.sadd("orders","jd003");
        Set<String> set1 = jedis.smembers("orders");
        for (Iterator iterator = set1.iterator(); iterator.hasNext();) {
            String string = (String) iterator.next();
            System.out.println(string);
        }
        jedis.srem("orders","jd002");
        System.out.println(jedis.smembers("orders").size());
        //hash
        jedis.hset("hash1","userName","chenyn");
        System.out.println(jedis.hget("hash1","userName"));
        Map<String,String> map = new HashMap<String,String>();
        map.put("telphone","13811814763");
        map.put("address","chenyn");
        map.put("email","abc@163.com");
        jedis.hmset("hash2",map);
        List<String> result = jedis.hmget("hash2", "telphone","email");
        for (String element : result) {
            System.out.println(element);
        }
        //zset
        jedis.zadd("zset01",60d,"v1");
        jedis.zadd("zset01",70d,"v2");
        jedis.zadd("zset01",80d,"v3");
        jedis.zadd("zset01",90d,"v4");

        Set<String> s1 = jedis.zrange("zset01",0,-1);
        for (Iterator iterator = s1.iterator(); iterator.hasNext();) {
            String string = (String) iterator.next();
            System.out.println(string);
        }
    }
~~~

