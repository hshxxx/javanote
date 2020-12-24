# Nosql概述

## 1. 数据库发展历史

1. 单机MySQL模式

   互联网初期用户少，访问量小，服务器无压力，读写请求直接访问数据库即可。但随着互联网发展，新用户日益增长造成信息越来越多，服务器的压力也日益增长，直接访问数据库的单机模式已经无法应对。

2. **缓存中间件**和**读写分离（垂直拆分）**

   既然一个数据库不能应对海量请求，那么我就多来几个数据库，而且有的数据库只用来执行写操作，有的数据库只来执行读操作，对于写操作的数据库只需要将修改后的数据同步到其他数据库中即可。而且还可以优化数据库底层操作的算法，同样也可以提高访问速度。

   这虽然能一定程度缓解高访问的场景，但是还有许多优化空间。比如a和b两个人都查询id为1的用户信息，那么需要访问两次数据库。很明显第二次访问是多余的，我们可以将用户信息在第一次访问的时候就放到缓存中，那么第二次访问时直接从缓存中取就好了。直接从缓存中取的时间明显要比访问数据库获得信息要快得多。

   <img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201214185912365.png" alt="image-20201214185912365" style="zoom:60%;" />

3. **分库分表+MySQL集群（水平拆分）**

   MySQL推出了表分区和集群

   

<img src="G:\google下载\未命名文件 (2).png" alt="未命名文件 (2)" style="zoom: 67%;" />

4. 前几年

   数据量太多导致MySQL关系型数据库不够用了，比如一个文章里面存储文本，图片，视频，把他们都存在MySQL中会出现表太大，导致效率很低，所以要将数据分类存储，所以诞生了专门用来存储图片，视频的数据库。

5. 大型互联网公司后台架构

   细化数据库的分类，设置专用的数据库来存储同类数据。

   ![image-20201214195749710](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201214195749710.png)

   

## 2. 为什么用Nosql

前面提到现在的大型互联网公司在采用各种专用的数据库来存储同类数据。而当今用户的个人信息，地理位置，社交网络等不需要固定格式存储起来的数据爆发式增长，同理也需要Nosql这种非关系型数据库来存储。

## 3. 什么是Nosql

Nosql全称 not only sql，泛称非关系数据库。主要以Map<>为基础存取数据。

1. Nosql 特点

   - 方便扩展：数据之间没有关系，很好扩展。

   - 大数据高性能：redis一秒可以写8万次，读11万次

   - 数据类型是多样的：不需要设计数据库表，随取随用。

     

2. 关系型数据库 和 Nosql比较

   关系型数据库：

   - 结构化组织，数据和关系都存在表中，耦合度高，不宜扩展。
   - 需要sql语言存取数据。
   - 严格一致性：在任何时刻所有的用户或者进程查询到的都是最近一次成功更新的数据。
   - 基础的事务

   **nosql**

   - 以Map为基础存取数据，没有固定的语言
   - 有键值对存储，列存储，文档存储（mongdb），图形数据库（社交关系的拓扑图）多个类型
   - 最终一致性：在某一时刻用户或者进程查询到的数据可能都不同，但是最终成功更新的数据都会被所有用户或者进程查询到。
   - CAP理论和BASE理论
   - 高性能，高可用，高可扩

3. CAP和BASE理论

   - CAP是Consistency、Availablity和Partition-tolerance的缩写，分别指一下三点，**CAP理论指出：CAP三者只能取其二，不可兼得。**
     - 一致性（Consistency）：每次读操作都能保证返回的是最新数据；
     - 可用性（Availablity）：任何一个没有发生故障的节点，会在合理的时间内返回一个正常的结果；
     - 分区容忍性（Partition-torlerance）：当节点间出现网络分区，照样可以提供服务。
   - BASE理论是对CAP中一致性和可用性权衡的结果。核心思想是即使无法做到强一致性，但每个应用都可以根据自身业务特点，才用适当的方式来使系统打到最终一致性。

4. 3V+3高

   3V 是海量Velume，多样Variety，实时Velocity

   3高 是高并发，高可扩，高性能

## 4. 淘宝商品页面

一个商品页面中各类数据的存储：不是简单的MySQL存储一切

```shell
# 1.商品的基本信息
	名称，价格，商家信息等，直接用MySQL就可以
# 2. 商品的描述，评论
	文档型数据库，如Mongdb
# 3. 图片
	分布式文件系统 FastDFS
# 4.商品的关键字（搜索）
	搜索引擎elasticsearch
# 5.商品的热门波段信息
	redis，Tair
# 6.商品的交易，外部的接口
	第三方
```



## 5.Nosql四大分类

|         **分类**         |                    **Examples举例**                    |                         典型应用场景                         |                      数据模型                      |                             优点                             |                             缺点                             |
| :----------------------: | :----------------------------------------------------: | :----------------------------------------------------------: | :------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| **键值（key-value）[3]** | Tokyo Cabinet/Tyrant, **Redis,** Voldemort, Oracle BDB | 内容缓存，主要用于处理大量数据的高访问负载，也用于一些日志系统等等。[3] | Key 指向 Value 的键值对，通常用hash table来实现[3] |                          查找速度快                          |      数据无结构化，通常只被当作字符串或者二进制数据[3]       |
|   **列存储数据库[3]**    |               Cassandra, **HBase**, Riak               |                       分布式的文件系统                       |         以列簇式存储，将同一列数据存在一起         |         查找速度快，可扩展性强，更容易进行分布式扩展         |                         功能相对局限                         |
|   **文档型数据库[3]**    |                  CouchDB, **MongoDb**                  | Web应用（与Key-Value类似，Value是结构化的，不同的是数据库能够了解Value的内容） |      Key-Value对应的键值对，Value为结构化数据      | 数据结构要求不严格，表结构可变，不需要像关系型数据库一样需要预先定义表结构 |            查询性能不高，而且缺乏统一的查询语法。            |
| **图形(Graph)数据库[3]** |          **Neo4J**, InfoGrid, Infinite Graph           |           社交网络，推荐系统等。专注于构建关系图谱           |                       图结构                       |     利用图结构相关算法。比如最短路径寻址，N度关系查找等      | 很多时候需要对整个图做计算才能得出需要的信息，而且这种结构不太好做分布式的集群方案。[3] |

# redis基本知识

redis是单线程。redis既然是单线程为什么还那么快？

- redis使用C编写的
- redis是基于内存操作，它的性能瓶颈不取决于cpu，而取决于机器内存和网络带宽。
- 多线程未必比单线程快。CPU处理多线程需要频繁的切换上下文，而单线程必须要。

# key常用命令

```shell
# 获取所有的key
keys * 
# 选择第一个库
select 0 
# 将当前的数据库key移动到某个数据库,目标库有，则不能移动
move myString 1 
# 清除指当前数据库库
flushdb      
# 随机获取数据库中一个key
randomkey     
# 获取key的类型
type key      
# 设置key
set key1 value1 
# 获取key
get key1    
# 设置多个key
mset key1 value1 key2 value2 key3 value3
# 获取多个key
mget key1 key2 key3
# 删除key
del key1   
# 判断是否存在key
exists key     
#  设置key的生存时间，以秒为单位，10秒后自动删除，
expire key 10  
# 设置key的生存时间，以毫秒为单位，1秒后自动删除
pexpire key 1000 
# 移出除key上的生存时间使之变为永久生存
persist key     
```



# 五大数据类型

## 1、String

```shell
# 设置value
  	set name cxx
# 获取value
    get name
# 获取子串
getrange name 0 5        
# 设置新值，返回旧值
getset name new_cxx       
# 批量设置
mset key1 key2            
# 批量获取
mget key1 key2            
# 不存在就插入（not exists）
setnx key value           
# 设置生存时间（类似于expire）
setex key time value      
# 从index开始替换为value
setrange key index value  
# 若age为1，递增2；若age不存在,创建并设置为1
incr age        
# age增加10
incrby age 10   
# age递减
decr age        
# age减10
decrby age 10   
# 若num为1.2，递增2.4；若num不存在,创建并设置为1.2
incrbyfloat num 1.2   
# 追加key的值
append key value         
# key长度
strlen key      
# 位操作
getbit/setbit/bitcount/bitop    
    
```

应用场景：计数器，统计多单位的数量，粉丝数，对象信息缓存存储。

## 2、Hash

```shell
# 将hash1中name键的值设置为tom
hset hash1 name tom
# 获取hash1中name键的值    
hget hash1 name
# 批处理
hmset hash1 name cxx age 25 note "i am notes"
hmget hash1 name age note   
# 获取hash1所有的键值对
hgetall hash1               
# hash1中是否存在name键
hexists hash1 name          
# 当score不存在时，设置它的值为100
hsetnx hash1 score 100      
# 递增1
hincrby hash1 id 1          
# 删除
hdel hash1 name             
# 只取hash1的key
hkeys hash1                 
# 只取hash1的value
hvals hash1                 
# hash1的长度
hlen hash1                  
```

使用场景：存储容易变动的数据。hash更适合对象的存储，string更适合字符串存储。

## 3、List

```shell
# 从左插入
lpush list1 a b c  
# 从右插入
rpush list1 x y z  
# 展示list1下标从0到5的值
lrange list1 0 5 
# 从左弹出元素，list1中该元素也被删除
lpop list1  
# 从右弹出元素，list1中该元素也被删除
rpop list1  
# list1的长度
llen list1 
# 根据参数 count 的值，移除list1列表中与参数 value 相等的元素。count 的值可以是以下几种：
	#count > 0 : 从表头开始向表尾搜索，移除与 value 相等的元素，数量为 count 。
	#count < 0 : 从表尾开始向表头搜索，移除与 value 相等的元素，数量为 count 的绝对值。
	#count = 0 : 移除表中所有与 value 相等的值。
	lrem list1 count value  
# 返回指定索引的值
lindex list1 2          
# 用索引设值
lset list1 2 n          
# list1只保留指定区间内的元素，区间外的元素删除
ltrim list1 0 4         
# 在list1中元素a之前插入1
linsert list1 before a 1
# 在list1中元素a之后插入2
linsert list1 after a 2 
# 弹出list1的尾元素返回到控制台，然后将其压进list2队头，可以指定pop和push的方向。
rpoplpush list1 list2  
```

使用场景：可以模拟队列和栈

## 4、Set

```shell
# 给myset中添加值
sadd myset 1 
# 查看myset中的所有值
    smembers myset  
#  删除myset中的值
    srem myset set1    
# 判断元素set1是否在myset集合中
    sismember myset set1 
# 返回myset中元素个数
    scard myset   
# 集合间运算：差集 | 交集 | 并集
    sdiff | sinter | sunion 操作：
	# 返回 set1 - Set2 之后的set1剩余的元素
	sdiff set1 set2 
# 随机获取集合中的元素
    srandmember  
# 从myset集合中弹出一个元素
    spop myset             
```

使用场景：共同好友（取交集）

## 5、ZSet有序集合

score 必须是整数值或双精度浮点数。

![image-20201215121242614](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201215121242614.png)

```shell
# 给zset中添加元素
	zadd zset 1 one	
    zadd zset 2 two
    zadd zset 3 three	
# 给zset中value为one的值加1
    zincrby zset 1 one   
# 获取two的分数
    zscore zset two    
    
# 获取zset中的范围值，0和-1表示显示所有  
    zrange zset 0 -1 withscores     
#  获取zset中的分数在10和25之间的值，由小到大排列
    zrangebyscore zset 10 25 withscores 
# 获取zset中的分数在10和25之间的值，并且只显示第1个到第4个
    zrangebyscore zset 10 25 withscores limit 0 5 
# 获取zset中的分数在10和25之间的值，由大到小排列
    Zrevrangebyscore zset 25 10 withscores 
# 获取zset中元素数量
    zcard zset 0 3 
# 获取zset中值在0和3之间的元素数量
    Zcount zset 0 3
# 删除一个或多个元素
    Zrem zset one two      
# 按照排名顺序删除元素；删除了前两个元素
    Zremrangebyrank zset 0 1  
# 按照分数排列顺序删除元素；删除了前两个元素
    Zremrangebyscore zset 0 1 
# 查看zset中按分数由小到大排列时value1的排名
    Zrank zset value1    
# 查看zset中按分数由大到小排列时value1的排名
    Zrevrank zset value1 
# 将两个有序集的元素相加 ZINTERSTORE destination numkeys key [key ...]；numkeys用来指定key的数量
	ZINTERSTORE sum_point 2 mid_test fin_test
	
myredis:1>zrange mid_test 0 -1 withscores
    1)  "Han Meimei"
    2)  "70"
    3)  "Li Lei"
    4)  "70"
    5)  "Tom"
    6)  "99.5"
myredis:1>zrange fin_test 0 -1 withscores
    1)  "Han Meimei"
    2)  "75"
    3)  "Li Lei"
    4)  "88"
    5)  "Tom"
    6)  "99.5"
# 将两个有序集的内容相加
myredis:1>ZINTERSTORE sum_point 2 mid_test fin_test
    (integer) 3
    # 显示有序集内所有成员及其 score 值
myredis:1>ZRANGE sum_point 0 -1 WITHSCORES     
    1) "Han Meimei"
    2) "145"
    3) "Li Lei"
    4) "158"
    5) "Tom"
    6) "199"
```

使用场景：排行榜；利用score实现带权排序

# 三种特殊数据类型

## 1. geospatial

用来记录地理位置的经纬度

```shell
# 添加某地的经纬度
geoadd china 121.48 31.40 shanghai
geoadd china 116.23 40.22 beijing
# 返回两地间距离,1088.7854 
geodist china shanghai beijing km
# 返回某地坐标的hash值,wtw6sk5n300
geohash china shanghai 
# 返回某地经纬度
geopos china shanghai
# 给定一个经纬度和一个半径并画成一个圆，返回在圆内的给定地区中的地理位置
georadius china 119.34 35.23 1000 km withdist
# 同上，只不过给出一个地区即可
georadiusbymember china shanghai 2000 km 
```



## 2.hyperloglog

> 集合的基数：集合中不重复的元素的个数。如A{1,2,4,5,2｝ 的基数为4。
>
> hyperloglog ：一种数据结构
>
> 合并后的新hyperloglog 的基数有0.81%的误差

```shell
# 往hyperloglog结构中添加元素
pfadd log 1 2 3 1 4
pfadd morelog 1 2 3 4 5
# 返回hyperloglog对象的基数
pfcount log 		#4
pfcount morelog		#5
# 合并两个hyperloglog对象
pfmerge newlog log morelog
pfcount newlog  #5
```

在统计网站的访问量时可以利用hyperloglog对象，将每一个访问的id都放在一个hyperloglog对象，需要统计访问量时使用pfcount命令即可。

## 3.bitmaps

bitmaps 位图：一种数据结构，操作二进制位来进行记录，所以他适用于记录某件事情的两种状态。比如，统计用户信息是否活跃，用户是否登陆，用户是否打卡等操作。

```shell
# 统计一个员工一周内打卡情况
myredis:1>setbit sign 0 1
"0"
myredis:1>setbit sign 1 1
"0"
myredis:1>setbit sign 2 0
"0"
myredis:1>setbit sign 3 1
"0"
myredis:1>setbit sign 4 1
"0"
myredis:1>setbit sign 5 0
"0"
myredis:1>setbit sign 6 1
"0"
# 一周只打卡了5天
myredis:1>bitcount sign
"5"
```

# 事务

redis事务相关的命令有4个：

- multi  开启一个事务
- exec 执行事务
- discard 清除命令队列并放弃事务
- watch

> 1.事务演示

```shell
# 事务正常执行
myredis:1>multi 				# 开启事务
"OK"
myredis:1>lpush newlist 1 2 3  	# 事务的命令被放进命令队列中
"QUEUED"   						# 返回QUEUED表示入队成功
myredis:1>rpush newlist a b c
"QUEUED"
myredis:1>exec 					# 执行事务
 1)  "OK"	# 命令是否正常执行
 2)  "3"	# 命令的返回值
 3)  "OK"
 4)  "6"
 5)  "OK"

# 放弃事务执行
myredis:1>multi 				
"OK"
myredis:1>lpush newlist 1 2 3  	
"QUEUED"   						
myredis:1>discard
"OK"

```

> 2.事务中的错误

```shell
# 1. 事务在exec执行之前出错
	redis 2.6.5 之后服务器会对入队失败的命令记录，在调用exec时会停止并取消事物的执行。之前版本会放弃执行那些错误的指令，只执行成功的指令。新版本使得在流水线（pipeline）中包含事务变得简单，因为发送事务和读取事务的回复都只需要和服务器进行一次通讯。
# 2. 事务在exec执行之后出错
	redis执行成功的方法，放弃有错误的方法。
```

> 3.redis不支持事务回滚

```shell
	1.redis命令只会因为错误的语法而失败，或者命令用在错误的类型上。这些错误应该在开发时被发现，而不是在生产环境中出现。
	2.因为不需要数据回滚，所以redis的结构可以更简单。
```



> 4.watch 

​	用来监视事务中的变量在exec之前是否被修改，如果被修改会放弃执行事务

```shell
# 演示
WATCH mykey
val = GET mykey
val = val + 1
MULTI
SET mykey $val
EXEC
# 如果在 WATCH 执行之后， EXEC 执行之前，有其他客户端修改了 mykey 的值，那么当前客户端的事务就会失败。 程序需要做的，就是不断重试这个操作(自旋)，直到没有发生碰撞为止。
--------------------
# watch 可以一次监视多个变量，
redis> WATCH key1 key2 key3
OK

# 对键的监视从 WATCH 执行之后开始生效，直到调用 EXEC 为止。
# 当 EXEC 被调用时，不管事务是否成功执行，对所有键的监视都会被取消。
```

> 5. unwatch

```shell
	使用无参数的 UNWATCH 命令可以手动取消对所有键的监视。 对于一些需要改动多个键的事务， 有时候程序需要同时对多个键进行加锁， 然后检查这些键的当前值是否符合程序的要求。 当值达不到要求时， 就可以使用 UNWATCH 命令来取消目前对键的监视， 中途放弃这个事务， 并等待事务的下次尝试。
```



> 6.使用 watch 实现 zpop (原子地弹出有序集合中分值（score）最小的元素)

```shell
WATCH zset
element = ZRANGE zset 0 0
MULTI
ZREM zset element
EXEC
```



# springboot 整合

jedis 是 springboot 2.x前redis的官方java工具类，现已被luttce取代。原因是它采用的直连，多个线程操作不安全，想要安全需要使用jedis连接池，是bio模式。

**luttce** ：采用netty，实例可以在多个线程中共享，不存在线程不安全，可以减少线程数据，是nio模式。



> 1. 导入redis启动器

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency> 
```



> 2. 配置redis

```properties
# Redis数据库索引（默认为0）  
spring.redis.database=0  
# Redis服务器地址  
spring.redis.host=192.168.50.132  
# Redis服务器连接端口  
spring.redis.port=6379  
# Redis服务器连接密码（默认为空）  
spring.redis.password=  
# 连接池最大连接数（使用负值表示没有限制）  
spring.redis.pool.max-active=200  
# 连接池最大阻塞等待时间（使用负值表示没有限制）  
spring.redis.pool.max-wait=-1  
# 连接池中的最大空闲连接  
spring.redis.pool.max-idle=10 
# 连接池中的最小空闲连接  
spring.redis.pool.min-idle=0  
# 连接超时时间（毫秒）  
spring.redis.timeout=1000
```



> 3. 测试

```java
// 导入这两个组件就可以使用redis
@Autowired
private RedisTemplate redisTemplate;
@Autowired //专门用来操作字符串类型
private StringRedisTemplate stringRedisTemplate;
```

## 1.redis自动配置类

```java
public class RedisAutoConfiguration {
    public RedisAutoConfiguration() {
    }

    @Bean
    //当容器中没有redisTemplate是redis的自动配置类才会生效，所以我们自定义一个redisTemplate来覆盖它。
    @ConditionalOnMissingBean(		
        name = {"redisTemplate"}
    )
    @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<Object, Object> template = new RedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }

    @Bean
    @ConditionalOnMissingBean
    @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
    public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
        StringRedisTemplate template = new StringRedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
}
```

## 2 . redis 的序列化

> 1.我们要明确为什么需要序列化？

​	我们只需要明白一句话，在JAVA中，一切皆对象，而将**对象的状态信息**转为**存储或传输的形式**需要序列化。



> 2.springboot有哪些序列化器？

```java
Jackson2JsonRedisSerializer		    //将对象序列化成json格式
JdkSerializationRedisSerializer		//将对象序列化称byte[]格式
OxmSerializer
StringRedisSerializer				//将对象序列化成string格式
GenericToStringRedisSerializer
GenericJackson2JsonRedisSerializer
```

如果我们使用 StringRedisTemplate 来操作redis，不需要我们手动创建序列化类，它的底层使用StringRedisSerializer 帮我们实现了序列化。

```java
public StringRedisTemplate() {
    setKeySerializer(RedisSerializer.string());
    setValueSerializer(RedisSerializer.string());
    setHashKeySerializer(RedisSerializer.string());
    setHashValueSerializer(RedisSerializer.string());
}
-------------
static RedisSerializer<String> string() {
	return StringRedisSerializer.UTF_8;
}
---------------
public static final StringRedisSerializer UTF_8 = new StringRedisSerializer(StandardCharsets.UTF_8);
```

但是如果我们使用 string , Object 类型时，StringRedisTemplate并不能帮我们完成序列化操作，需要我们手动创建。



> **3.RedisTemplate 的序列化**

创建一个redis的配置文件，使得它可以将redisTemplate的value为object类型的对象转换为json格式存入到redis中。自定义的object对象需要声明序列化接口。

```java
@Configuration
public class RedisConfig {
  @Bean
  @SuppressWarnings("all")
  public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
      RedisTemplate<String, Object> template = new RedisTemplate<String, Object>();
      template.setConnectionFactory(factory);
      Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
      ObjectMapper om = new ObjectMapper();
      om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
      om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
      jackson2JsonRedisSerializer.setObjectMapper(om);
      StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

      // key采用String的序列化方式
      template.setKeySerializer(stringRedisSerializer);
      // hash的key也采用String的序列化方式
      template.setHashKeySerializer(stringRedisSerializer);
      // value序列化方式采用jackson
      template.setValueSerializer(jackson2JsonRedisSerializer);
      // hash的value序列化方式采用jackson
      template.setHashValueSerializer(jackson2JsonRedisSerializer);
      template.afterPropertiesSet();

      return template;
  }

}
------------------------
//StringRedisSerializer 默认使用utf-8的格式
public StringRedisSerializer() {
    this(StandardCharsets.UTF_8);
}
```

## redis工具类

实际开发中不直接使用redisTemplate原生的方法来操作redis，会使用redis的工具类。

```
G:\BaiduNetdiskDownload\redis-study\springboot-redis\src\main\java\com\kuang\utils
```

# redis.conf

```shell
# Redis配置文件样例

# Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
# 启用守护进程后，Redis会把pid写到一个pidfile中，在/var/run/redis.pid
daemonize no

# 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
pidfile /var/run/redis.pid

# 指定Redis监听端口，默认端口为6379
# 如果指定0端口，表示Redis不监听TCP连接
port 6379

# 绑定的主机地址
# 你可以绑定单一接口，如果没有绑定，所有接口都会监听到来的连接
# bind 127.0.0.1

# 当客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
timeout 0

# 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
# debug (很多信息, 对开发／测试比较有用)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
loglevel verbose

# 日志记录方式，默认为标准输出，如果配置为redis为守护进程方式运行，而这里又配置为标准输出，则日志将会发送给/dev/null
logfile stdout

# 设置数据库的数量，默认数据库为0，可以使用select <dbid>命令在连接上指定数据库id
# dbid是从0到‘databases’-1的数目
databases 16

################################ SNAPSHOTTING  #################################
# 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
# Save the DB on disk:
#   满足以下条件将会同步数据:
#   900秒（15分钟）内有1个更改
#   300秒（5分钟）内有10个更改
#   60秒内有10000个更改
#   Note: 可以把所有“save”行注释掉，这样就取消同步操作了

save 900 1
save 300 10
save 60 10000

# 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
rdbcompression yes

# 指定本地数据库文件名，默认值为dump.rdb
dbfilename dump.rdb

# 工作目录.
# 指定本地数据库存放目录，文件名由上一个dbfilename配置项指定
# 
# Also the Append Only File will be created inside this directory.
# 
# 注意，这里只能指定一个目录，不能指定文件名
dir ./

################################# REPLICATION #################################

# 主从复制。使用slaveof从 Redis服务器复制一个Redis实例。注意，该配置仅限于当前slave有效
# so for example it is possible to configure the slave to save the DB with a
# different interval, or to listen to another port, and so on.
# 设置当本机为slav服务时，设置master服务的ip地址及端口，在Redis启动时，它会自动从master进行数据同步
# slaveof <masterip> <masterport>


# 当master服务设置了密码保护时，slav服务连接master的密码
# 下文的“requirepass”配置项可以指定密码
# masterauth <master-password>
t to INFO and SLAVEOF.

slave-serve-stale-data yes

# repl-ping-slave-period 10

# repl-timeout 60

################################## SECURITY ###################################

# 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过auth <password>命令提供密码，默认关闭
# requirepass foobared


################################### LIMITS ####################################

# 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，
# 如果设置maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max Number of clients reached错误信息
# maxclients 128

# 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，
# 当此方法处理后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。
# Redis新的vm机制，会把Key存放内存，Value会存放在swap区
# maxmemory <bytes>

# maxmemory-policy volatile-lru

# maxmemory-samples 3

############################## APPEND ONLY MODE ###############################

# 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。
# 因为redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
# IMPORTANT: Check the BGREWRITEAOF to check how to rewrite the append
# log file in background when it gets too big.

appendonly no

# 指定更新日志文件名，默认为appendonly.aof
# appendfilename appendonly.aof

# 指定更新日志条件，共有3个可选值：
# no:表示等操作系统进行数据缓存同步到磁盘（快）
# always:表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全）
# everysec:表示每秒同步一次（折衷，默认值）
appendfsync everysec

no-appendfsync-on-rewrite no

auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

################################## SLOW LOG ###################################
slowlog-log-slower-than 10000

slowlog-max-len 1024

################################ VIRTUAL MEMORY ###############################

### WARNING! Virtual Memory is deprecated in Redis 2.4
### The use of Virtual Memory is strongly discouraged.
# 指定是否启用虚拟内存机制，默认值为no，
# VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中
# 把vm-enabled设置为yes，根据需要设置好接下来的三个VM参数，就可以启动VM了
vm-enabled no
#
# Redis交换文件最好的存储是SSD（固态硬盘）
# 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
vm-swap-file /tmp/redis.swap

# 将所有大于vm-max-memory的数据存入虚拟内存，无论vm-max-memory设置多少，所有索引数据都是内存存储的（Redis的索引数据就是keys）
# 也就是说当vm-max-memory设置为0的时候，其实是所有value都存在于磁盘。默认值为0
vm-max-memory 0

# Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的数据大小来设定的。
# 建议如果存储很多小对象，page大小最后设置为32或64bytes；如果存储很大的对象，则可以使用更大的page，如果不确定，就使用默认值
vm-page-size 32

# 设置swap文件中的page数量由于页表（一种表示页面空闲或使用的bitmap）是存放在内存中的，在磁盘上每8个pages将消耗1byte的内存
# swap空间总容量为 vm-page-size * vm-pages
vm-pages 134217728

# 设置访问swap文件的I/O线程数，最后不要超过机器的核数，如果设置为0，那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟，默认值为4
vm-max-threads 4

############################### ADVANCED CONFIG ###############################

# 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
hash-max-zipmap-entries 512
hash-max-zipmap-value 64

list-max-ziplist-entries 512
list-max-ziplist-value 64

set-max-intset-entries 512

zset-max-ziplist-entries 128
zset-max-ziplist-value 64

# 指定是否激活重置哈希，默认为开启
activerehashing yes

################################## INCLUDES ###################################

# 指定包含其他的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各实例又拥有自己的特定配置文件
# include /path/to/local.conf
# include /path/to/other.conf
```



# redis 持久化

redis 是内存数据库，如果不将数据持久化，那么第二次打开时数据就会丢失，所以redis提供了持久化机制。

## 1. RDB（redis database)

1. 什么是RDB

   redis会在指定的时间间隔内将内存中数据保存到磁盘中（snapshot快照），它恢复时是将快照文件直接读到内存里。

   redis 会单独创建一个子进程来异步进行持久化，会先将数据写到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何IO操作的。这就确保了极高的性能。如果需要进行大规模数据的回复，且对于数据恢复的完整性不是很敏感，那么RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化的数据可能因为服务器宕机而丢失。

   可以在配置文件中配置，dump.rdb文件备份内存中数据。dump.rdb文件在一下三种情况下会被创建：

   - 满足save条件
   - 执行 flushdb 命令前
   - 关闭服务器前

   ```shell
   save 900 1
   save 300 10
   save 60 10000
   # 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
   rdbcompression yes
   # 指定本地数据库文件名，默认值为dump.rdb
   dbfilename dump.rdb
   ```

   RDB优点：

   - 适合大规模数据的恢复
   - 对完整性要求不高。

   RDB缺点：

   - 需要一点时间来执行持久化操作，如果服务器意外宕机，那么最后一次操作修改的数据就丢失了。
   - fork子的进程会占用一点空间。

   ![image-20201216144728145](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201216144728145.png)



## 2. AOF（append only file)

AOF 也是用来实现redis的持久化的，它是以日志的形式来将所有执行过的命令都记录在一个文件中（appendonly.aof），这个文件只允许追加。redis在启动前会执行该文件中所有的写命令来重新构建数据。

​	AOF 功能默认是关闭的，需要自己开启。开启之后操作redis便会生成一个appendonly.aof文件。

```shell
# yes 开启aof
appendonly no
# 指定更新日志条件，共有3个可选值：
# no:表示等操作系统进行数据缓存同步到磁盘（快）
# always:表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全）
# everysec:表示每秒同步一次（折衷，默认值）
appendfsync everysec
```

​	redis 在启动前会检查appendonly.aof 文件是否正常，如果有误，redis启动会失败。这是可以利用redis的修复工具来修复appendonly.aof文件。修复的时候会造成部分数据丢失。

```shell
redis-check-aof --fix appendonly.aof
```

aof优点：

- 每一次修改都同步，文件的完整性会更好
- 每秒同步一次，可能会丢失一秒的数据。
- 从不同步效率最高

aof缺点：

- 相对于数据文件来说，aof远远大于rdb，修复的速度也比rdb慢
- aof 运行效率也比rdb慢，所以默认用rdb持久化。





![image-20201216150919011](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201216150919011.png)

aof同步机制

```
同步机制又分为：
全同步是指slave启动时进行的初始化同步。
部分同步是指Redis运行过程中的修改同步。
```

```
一、全同步

全同步过程如下：

1）在slave启动时，会向master发送一条SYNC指令。
2）master收到这条指令后，会启动一个备份进程将所有数据写到rdb文件中去。
3）更新master的状态（备份是否成功、备份时间等），然后将rdb文件内容发送给等待中的slave。

注意，master并不会立即将rdb内容发送给slave。而是为每个等待中的slave注册写事件，当slave对应的socket可以发送数据时，再讲rdb内容发送给slave。

二、部分同步

当Redis的master/slave服务启动后，首先进行全同步。之后，所有的写操作都在master上，而所有的读操作都在slave上。因此写操作需要及时同步到所有的slave上，这种同步就是部分同步。

部分同步过程如下：

1）master收到一个操作，然后判断是否需要同步到salve。
2）如果需要同步，则将操作记录到aof文件中。
3）遍历所有的salve，将操作的指令和参数写入到savle的回复缓存中。
4）一旦slave对应的socket发送缓存中有空间写入数据，即将数据通过socket发出去。
```

## 3. 扩展

![image-20201216152733400](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201216152733400.png)

# 主从复制

## 1. 概念

​	主从复制是指将一台redis服务器的数据复制到其他的redis服务器。前者称为主节点（master/ leader），后者称为从节点（slave / follower）。**数据复制只能从主节点到从节点**，master以写为主，slave以读为主。一般配置成**一主两从**。

​	**默认情况下redis服务器都为master节点**，一个master节点可以有多个slave节点，但一个slave节点只能有一个master节点。

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201216161300429.png" alt="image-20201216161300429" style="zoom:50%;" />

​	主从复制的作用：

- 数据冗余：主从复制实现数据的热备份，是持久化之外的一种数据冗余方式。
- 故障恢复：当主节点出现问题时，可以由节点提供服务，实现快速的故障恢复；实际上是一种服务的冗余。
- 负载均衡：在主从复制的基础上配合读写分离，可以由主节点提供写服务，从节点提供读服务，分担服务器负载。这样做的原因是用户80%的操作都是读操作。
- 高可用基石：主从复制还是哨兵和集群能够实施的基础，因此说主从复制是redis高可用的基础。

## 2. 环境配置

想要将redis服务器设置成从节点前**需要修改redis的配置文件**中的 1、端口号，2、pid名字，3、log文件名字，4、dump.rdb名字 这四个配置后再启动redis，否则无法分别主节点和从节点。

设置当前redis服务器为从节点的方式如下，修改后成为**从节点的redis服务器便只能读不能写**。

```shell
# 暂时性设置从节点；从节点认主节点---认老大
slaveof ip地址 端口号 
# 永久性认老大需要在配置文件中修改
replicaof ip地址 端口号
```

使用命令行和使用配置文件设置的区别：

- 命令行设置是暂时性的，配置文件中设置的是永久性的。
- 使用**命令行**设置的从节点会在主节点宕机时**自动切换**回主节点状态。

主节点关闭后，如果需要将当前从节点设置为主节点可以使用 salveof no one 命令。



## 3. 主从复制原理

主从复制原理：slave 启动成功后会连接到master并发送一个sync命令给master。

​							master接到命令后会启动后台的存盘进程，同时收集所有接收到的用于修改数据集的命令，在后					台进程执行完毕之后，master将传送整个数据文件到slave，并完成一次完全同步（全量复制）。

​							需要注意的是：只要是重新连接master，一次完全同步（全量复制）将被自动执行。

全量和增量复制：

- 全量复制：master将所有修改数据集的命令发送给slave，slave一次性完成同步。
- 增量复制：master继续将新的所有收集到的修改命令一次传给slave，完成同步。master和slave之间的连接没有断，master修改一次数据集就会将修改命令发送给slave。



## 4.哨兵模式

​           如果某个主节点宕机了需要将某个从节点设置为主节点，那么以前是需要手动切换的，但redis2.8之后提供了哨兵模式（Sentinel），它可以监控主机是否故障，如果出现故障会根据投票数自动将从库转为主库（自动选老大）。

​			哨兵模式是一种特殊的模式，哨兵是一个独立运行的进程。他的原理是哨兵通过发送命令，等待redis服务器相应，从而监控运行的多个redis实例。下面这个是单哨兵模式，为了防止哨兵进程被杀死，还可以设置哨兵集群。

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201216182918646.png" alt="image-20201216182918646" style="zoom:67%;" />



哨兵的作用：

- 通过发送命令给redis服务器获取返回值的方式来监控redis服务器状态，包括主从服务器。
- 当哨兵检测到master宕机，会自动将slave切换成master，然后通过发布订阅模式通知其他的从服务器，修改它的配置文件，让他们切换成主机。

多哨兵模式：

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201216183355017.png" alt="image-20201216183355017" style="zoom:67%;" />



**主从切换**：假设master服务器宕机，哨兵1先检测到这个结果，但系统不会马上进行failover（故障转移）过程，此时仅仅是哨兵1主观的认为主服务器不可用，这个现象称为 **主观下线**。当其他的哨兵也检测到master服务器宕机，并且数量达到一定值时，那么哨兵之间就会进行一次投票，投票通过后系统才进行failover操作。切换成功后，就会通过发布订阅模式，让各个哨兵让自己监控的slave服务器切换主机，这个过程成为 **客观下线**。加入已经下线的master又正常了，它上线后会是别人的slave而不是master。



**哨兵模式的优缺点：**

优点：

- 哨兵集群，基于主从复制模式，所有的主从配置优点，它全都有。
- 主从可以切换，故障可以转移，系统的可用性就会很高。
- 哨兵模式就是主从模式的升级，主从的切换升级为自动切换，系统更加健壮。

缺点：

- redis 不好在线扩容，集群容量一旦到达上限，在线扩容就很麻烦。
- 实现哨兵模式的配置很麻烦，有很多种选择。	



哨兵模式的使用：

1. 配置哨兵配置文件 sentinel.conf

   ```shell
   # sentinel monitor 被监控的服务器 IP port 投票的票数
   sentinel monitor myredis 192.168.50.132 6379 1
   ```

2. 启动哨兵

   ```shell
   redis-sentinel kconfig/sentinel.conf
   ```

   

# 缓存穿透和雪崩

redis的使用很大程度缓解了服务器处理读操作的压力，但它也存着一些问题，比如数据一致性。严格来说这个问题无解，如果对数据一致性要求很高，那么就不能使用缓存。除了一致性问题外还有其他的问题，比如缓存穿透、缓存击穿、缓存雪崩。

## 缓存穿透

> 概念

用户想要查询的一个数据缓存中没有时（缓存未命中），于是会向数据库发起请求查询。如果某一时刻有许多用户查询的数据缓存中都没有时，大量的请求会发送给数据库，数据库面临巨大的压力。这就是缓存穿透。

> 解决方案

布隆过滤器：布隆过滤器是一种数据结构，对所有可能查询的参数以hash形式存储，在控制层先进行校验，不符合则丢弃，从而避免了对底层存储系统的查询压力。

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201216200131309.png" alt="image-20201216200131309" style="zoom:70%;" />



缓存空对象：当存储层不命中后，即使返回的空对象也将其缓存起来，同时会设置一个过期时间，之后再访问这个数据将会从缓存中获取，保护后端数据源。

但是这种方式也存在问题：1）如果空值能够被缓存起来，这就意味着缓存需要更多的空间存储更多的键，因为这当中可能会有许多空值的键；2）即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于需要保持一致性的业务会有影响。

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201216200322641.png" alt="image-20201216200322641" style="zoom:67%;" />









## 缓存击穿

> 概念

缓存击穿指的是某一个key被大量用户非常的频繁的查询（微博热搜）。假如redis定时清理了这个key，即使过了零点几秒又被添加到缓存中，只是这短短的零点几秒中，大量的请求全部指向了数据库，就像水坝上突然被凿开了一个洞一样，整个水坝接着就被冲毁了。

> 缓存方案

1. 设置热点数据永不过期。

2. 加互斥锁。

   使用分布式锁，保证每一个key同时只有一个线程去查询后端服务，其他线程没有获得分布式锁的权限，因此只能等待。这种方式将压力转移到了分布式锁上，因此对分布式锁的考验很大。

## 缓存雪崩

> 概念

缓存雪崩是指某一个时间段，缓存集中过期失效，redis宕机了，数据库压力骤增。

产生雪崩的原因之一：双十一零点时许多商品被加入到缓存中，假设缓存的生存时间是一个小时，那么1点时大量缓存同时过期。而查询这些商品的请求就都会由给数据库来处理，从而造成数据库崩溃。

更致命的雪崩原因是因为某一台服务器的宕机导致雪崩，这种雪崩丢数据库的压力不可预知，很有可能压垮数据库。

> 解决方案

1. redis 高可用：搭建redis集群，一台挂掉后其他服务器还可以顶住。
2. 限流降级 ：在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如对某个key只允许一个线程查询数据和写缓存，其他线程等待。还可以暂停某些不重要的服务，减少访问量。
3. 数据预热：把可能查询的数据提前加入到缓存中，设置均匀的过期时间。

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201216202337493.png" alt="image-20201216202337493" style="zoom:67%;" />