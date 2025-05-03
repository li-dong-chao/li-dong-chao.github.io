---
title: redis
date: 2023-06-09
categories: ["linux"]
---

## redis是什么？

redis(Remote DIctionary Server)是互联网中最广泛使用的中间件之一，他是一个开源的 **key—value** 型数据库，
初学者可以将redis理解为 **数据结构服务器**。
具体的，redis在保存数据时是以 **键值对** 的形式存储的，
其中值可以是字符串、哈希、列表、集合或有序集合等数据结构，因此可以将其理解为一个数据结构服务器。

redis最广泛的应用场景是Cache，即做缓存。除此之外，它还可以用来session共享、消息队列等。

## redis特点

* 开源且代码非常简单优雅
* 编译安全非常简单，无任何系统依赖
* 社区活跃，各种语言广泛支持
* 高性能
* 是单线程模型（单线程仅仅是说在网络请求这一模块上用一个请求处理客户端的请求，像持久化它就会重开一个线程/进程去进行处理。）
* 支持持久化
* 支持事务
* 支持主从复制模式
* 具有原子性

## redis的安装

### windows下安装redis

redis的官网地址为： <https://redis.io/，官网上没有提供> windows 下的安装包，
而是建议直接使用 WSL2 来安装redis。

![Snipaste_2023-04-17_22-29-24](https://littletom.oss-cn-nanjing.aliyuncs.com/Snipaste_2023-04-17_22-29-24.png)

如果安装了 WSL2 可以直接参考linux下redis的安装方法直接安装，
如果没有 WSL2，可以在 [这里] (<https://github.com/tporadowski/redis/releases>) 下载适用于自己windows位数的压缩包，
直接解压即可。

![Snipaste_2023-04-17_22-44-38](https://littletom.oss-cn-nanjing.aliyuncs.com/Snipaste_2023-04-17_22-44-38.png)

> 也可使用 msi 安装包，傻瓜式安装即可，这里不做详细介绍。
>

解压得到文件夹内容如下：

![Snipaste_2023-04-17_22-49-57](https://littletom.oss-cn-nanjing.aliyuncs.com/Snipaste_2023-04-17_22-49-57.png)

其中比较重要的文件有：`redis-server.exe` 和 `redis-li.exe` 文件。
前者为redis服务端启动程序，后者为redis客户端启动程序。

双击 `redis-server.exe` 文件，可以启动redis服务端程序，启动后界面如下：

![Snipaste_2023-04-18_19-14-30](https://littletom.oss-cn-nanjing.aliyuncs.com/Snipaste_2023-04-18_19-14-30.png)

然后再双击 `redis-cli.exe` 文件，可以启动redis客户端程序，启动后界面如下：

![Snipaste_2023-04-18_19-30-20](https://littletom.oss-cn-nanjing.aliyuncs.com/Snipaste_2023-04-18_19-30-20.png)

输入命令 `ping` ，如果返回 `pong` ，说明已经安装成功。

![Snipaste_2023-04-18_19-31-39](https://littletom.oss-cn-nanjing.aliyuncs.com/Snipaste_2023-04-18_19-31-39.png)

### linux下安装redis

ubuntu系统下可以直接使用 `apt` 包管理工具直接安装redis。

```shell
# 更新
sudo apt update
# 安装redis
sudo apt install redis
# 安装完成后可以使用redis-server命令启动服务端、redis-cli启动客户端
redis-server
```

除了使用包管理工具自动安装，还可以使用源码直接安装redis。
从 [redis官网](https://redis.io/download/) 下载最新的redis源码，然后手动安装。

```shell
wget https://github.com/redis/redis/archive/7.0.11.tar.gz -O redis.7.0.11.tar.gz
tar -zxvf redis.7.0.11.tar.gz 
cd redis.7.0.11 
make
```

出现以下界面说明安装完成了。

![Snipaste_2023-04-18_19-50-31](https://littletom.oss-cn-nanjing.aliyuncs.com/Snipaste_2023-04-18_19-50-31.png)

安装完成后， `src` 目录下将会生成 `redis-server` 和 `redis-cli` 文件，
可以使用其启动redis服务端或客户端。

如果你想把 `redis-server` 和 `redis-cli` 等程序安装到 `/usr/local/bin` 目录下，
可以 `make` 改为 `make install` 命令。

## 连接redis服务

redis支持外部应用通过 **TCP连接和redis专属协议** 与redis服务端进行通信，
但同时redis也提供了客户端程序 `redis-cli` 来方便用户直接与redis服务端通信。

在windows上直接双击 `redis-cli.exe` 即可打开客户端，linux系统下可以输入命令 `redis-cli` 来启动客户端。

启动客户端后，他会尝试使用默认配置连接本地的redis服务端，因此在使用客户端之前需要先启动服务端。

如果想要连接其他的redis服务器，可以使用下面的命令

```shell
redis-cli -h host -p port -a password
# host为远端服务端地址，port为端口，password密码
# 默认配置为：
# host: localhost
# port: 6379
# password: 默认无密码，无需该参数
```

## redis数据类型

redis是一种存储数据结构的服务，因此在了解redis基础命令之前，有必要先熟悉一下redis中常用的数据结构。

### 字符串（Strings）

* 字符串与Java、Python等语言中的字符串含义一样，是一串字节组成的序列。
* 字符串是redis中最常用、最基础的数据类型。
* 在默认配置下，redis中单个字符最多能存储的数据量为512M。
* 字符串是二进制安全的。

### 列表（Lists）

* 列表是按照插入顺序排序的，多个字符串组成的链表。
* 单个列表中的最大元素数量为 `2^32 - 1 (4,294,967,295)`。
* 添加元素的时间复杂度为 $O(1)$ ，操作列表中元素的时间复杂度为 $O(n)$。

### 集合（Sets）

* 与Java、Python等语言相同，集合表示一组无序、不重复元素组成的序列。
* 支持集合常用的交并差等操作。
* 单个集合中的最大元素数量为 `2^32 - 1 (4,294,967,295)`。

### 哈希（Hashes）

* redis哈希是以键值对形式保存进行数据保存的数据结构。
* 大部分命令的时间复杂度为 $`(1)$ ， `HKEYS` 、 `HVALS` 、 `HGETALL` 时间复杂度为 $O(n)$ 。
* 单个哈希中的最大键值对数量为 `2^32 - 1 (4,294,967,295)`。

### 有序集合（Sorted Sets）

* 从字面意思就可以理解，有序集合是存在顺序的集合，其顺序体现在有序集合中的每个元素都有一个关联分数，有序集合会按分数对集合内元素排序，当存在分数相同的时候，按字典序排序。
* 大部分操作的复杂度都是 $O(n)$ ， `ZRANGE` 命令复杂度为 $O(log(n)+m)$ ， $m$ 具体含义后续介绍命令时会详细介绍。

除了上述5种最常用的数据类型之外，redis还支持 `Streams` 、 `Geospatial indexes` 、
`Bitmaps` 、 `Bitfields` 、 `HyperLogLog` 等数据类型，感兴趣的可以去 [redis数据类型](https://redis.io/docs/data-types/) 学习。

> 二进制安全是指它可以是任何二进制的序列，包含简单的字符串，甚至图片内容以及空字符等。
>

## redis 键（key）

前面介绍过，redis是以键值对的形式来保存数据的。关于键，有下面几点需要注意：

* 键是二进制安全的
* 键的内部数据结构是字符串
* 键不应该设置的太长或太短，设置的原则是尽可能使含义明确。

>
> ​    关于键的设置，有几个小技巧:
>
>     * 对于确实键很长的场景，可以进行hash处理，如 `sha1` 等；
>     * 巧用分号 `:` 可以使键的含义更明确，如 `user:1000:followers` 等。
>       之所以使用分号，是因为点 `.` 、下划线 `_` 、横杠 `-` 等其他分隔符在字符串中出现的概率更高。



## redis命令

### 键（key）

redis与键相关的命令如下：

|   命令    |                       描述                        |           举例            |
| :-------: | :-----------------------------------------------: | :-----------------------: |
|    DEL    |             用于删除key，即删除键值对             |          `DELa`           |
|   DUMP    |         序列化给定key，并返回被序列化的值         |          `DUMPa`          |
|  EXISTS   |                检查给定key是否存在                |         `EXISTSa`         |
|  EXPIRE   |               为给定key设置过期时间               |        `EXPIREa10`        |
| EXPIREAT  | 用于为key设置过期时间，接受的时间参数是UNIX时间戳 |   `EXPIREATa1293840000`   |
|  PEXPIRE  |            设置key的过期时间，以毫秒计            |      `PEXPIREa1000`       |
| PEXPIREAT | 设置key过期时间的时间戳(unixtimestamp)，以毫秒计  | `PEXPIREATa1555555555005` |
|   KEYS    |             查找所有符合给定模式的key             |          `KEYS*`          |
|   MOVE    |       将当前数据库的key移动到给定的数据库中       |         `MOVEa1`          |
|  PERSIST  |      删除给定key的过期时间，使得key永不过期       |        `PERSISTa`         |
|   PTTL    |        以毫秒为单位返回key的剩余的过期时间        |          `PTTLa`          |
|    TTL    |       以秒为单位，返回给定key的剩余生存时间       |          `TTLa`           |
| RANDOMKEY |           从当前数据库中随机返回一个key           |        `RANDOMKEY`        |
|  RENAME   |                   修改key的名称                   |        `RENAMEab`         |
| RENAMENX  |        当newkey不存在时，将key改名为newkey        |       `RENAMENXab`        |
|   TYPE    |              返回key所储存的值的类型              |          `TYPEa`          |

### 字符串（Strings）

字符串相关的命令如下：

|    命令     |                       描述                        |        举例         |
| :---------: | :-----------------------------------------------: | :-----------------: |
|     SET     |                  设置指定key的值                  |     `SETaabcd`      |
|     GET     |                  获取指定key的值                  |       `GETa`        |
|  GETRANGE   |        返回key中字符串值，并切片得到子字符        |    `GETRANGEa02`    |
|   GETSET    | 将给定key的值设为value，并返回key的旧值(oldvalue) |    `GETSETaqwer`    |
|    MGET     |          获取所有(一个或多个)给定key的值          |     `MGETabcd`      |
|    SETEX    |    设置key的值为value同时将过期时间设为seconds    |    `SETEXa10aaa`    |
|    SETNX    |           只有在key不存在时设置key的值            |    `SETNXaaaaaa`    |
|   STRLEN    |           返回key所储存的字符串值的长度           |      `STRLENa`      |
|    MSET     |           同时设置一个或多个key-value对           |   `MSETaaabbbccc`   |
|   MSETNX    |   同时设置一个或多个key-value对，仅不存在时生效   |  `MSETNXaaabbbccc`  |
|   PSETEX    |           以毫秒为单位设置key的生存时间           |   `PSETEXa10aaa`    |
|    INCR     |              将key中储存的数字值增一              |      `INCRnum`      |
|   INCRBY    |    将key所储存的值加上给定的增量值(increment)     |    `INCRBYnum2`     |
| INCRBYFLOAT |  将key所储存的值加上给定的浮点增量值(increment)   | `INCRBYFLOATnum1.5` |
|    DECR     |              将key中储存的数字值减一              |      `DECRnum`      |
|   DECRBY    |    将key所储存的值减去给定的减量值(decrement)     |    `DECRBYnum2`     |
|   APPEND    |          将value追加到key原来的值的末尾           |    `APPENDnumaa`    |

### 列表（lists）

列表相关的命令如下：

|    命令    |                                描述                                |        举例        |
| :--------: | :----------------------------------------------------------------: | :----------------: |
|   BLPOP    |                移出并获取列表的第一个元素（阻塞式）                |     `BLPOPa10`     |
|   BRPOP    |               移出并获取列表的最后一个元素（阻塞式）               |     `BRPOPa10`     |
| BRPOPLPUSH | 从列表中弹出一个值，并将该值插入到另外一个列表中并返回它（阻塞式） |  `BRPOPLPUSHab10`  |
|   LINDEX   |                      通过索引获取列表中的元素                      |     `LINDEXa2`     |
|  LINSERT   |                    在列表的元素前或者后插入元素                    | `LINSERTaBEFOREax` |
|    LLEN    |                            获取列表长度                            |      `LLENa`       |
|    LPOP    |                     移出并获取列表的第一个元素                     |      `LPOPa`       |
|   LPUSH    |                    将一个或多个值插入到列表头部                    |    `LPUSHaabc`     |
|   LPUSHX   |                   将一个值插入到已存在的列表头部                   |     `LPUSHXaq`     |
|   LRANGE   |                      获取列表指定范围内的元素                      |    `LRANGEa24`     |
|    LREM    |                            移除列表元素                            |     `LREMa0a`      |
|    LSET    |                      通过索引设置列表元素的值                      |     `LSETa2ee`     |
|   LTRIM    |                      对一个列表进行修剪(trim)                      |     `LTRIMa24`     |
|    RPOP    |                     移除并获取列表最后一个元素                     |      `RPOPa`       |
| RPOPLPUSH  |      移除列表的最后一个元素，并将该元素添加到另一个列表并返回      |   `RPOPLPUSHab`    |
|   RPUSH    |                      在列表中添加一个或多个值                      |     `RPUSHaqq`     |
|   RPUSHX   |                        为已存在的列表添加值                        |    `RPUSHXaqq`     |

### 集合（sets）

集合相关的命令如下：

|    命令     |                     描述                      |       举例       |
| :---------: | :-------------------------------------------: | :--------------: |
|    SADD     |           向集合添加一个或多个成员            |   `SADDaabcda`   |
|    SCARD    |               获取集合的成员数                |     `SCARDa`     |
|    SDIFF    |            返回给定所有集合的差集             |    `SDIFFab`     |
| SDIFFSTORE  |  返回给定所有集合的差集并存储在destination中  | `SDIFFSTOREcab`  |
|   SINTER    |            返回给定所有集合的交集             |    `SINTERab`    |
| SINTERSTORE |  返回给定所有集合的交集并存储在destination中  | `SINTERSTOREcab` |
|  SISMEMBER  |       判断member元素是否是集合key的成员       |  `SISMEMBERad`   |
|  SMEMBERS   |             返回集合中的所有成员              |   `SMEMBERSa`    |
|    SMOVE    | 将member元素从source集合移动到destination集合 |    `SMOVEabc`    |
|    SPOP     |        移除并返回集合中的一个随机元素         |     `SPOPa`      |
| SRANDMEMBER |          返回集合中一个或多个随机数           |  `SRANDMEMBERa`  |
|    SREM     |           移除集合中一个或多个成员            |     `SREMab`     |
|   SUNION    |            返回所有给定集合的并集             |    `SUNIONab`    |
| SUNIONSTORE |   所有给定集合的并集存储在destination集合中   | `SUNIONSTOREcab` |
|    SSCAN    |               迭代集合中的元素                |  `SSCAN0MATCH*`  |

### 哈希（hashs）

集合相关的命令如下：

|     命令     |                         描述                         |          举例          |
| :----------: | :--------------------------------------------------: | :--------------------: |
|     HDEL     |               删除一个或多个哈希表字段               |        `HDELaa`        |
|   HEXISTS    |         查看哈希表key中，指定的字段是否存在          |      `HEXISTSaac`      |
|     HGET     |            获取存储在哈希表中指定字段的值            |       `HGETaac`        |
|   HGETALL    |         获取在哈希表中指定key的所有字段和值          |      `HGETALLaa`       |
|   HINCRBY    |   为哈希表key中的指定字段的整数值加上增量increment   |     `HINCRBYaab1`      |
| HINCRBYFLOAT |  为哈希表key中的指定字段的浮点数值加上增量increment  |  `HINCRBYFLOATaab1.5`  |
|    HKEYS     |                获取所有哈希表中的字段                |       `HKEYSaa`        |
|     HLEN     |                获取哈希表中字段的数量                |        `HLENaa`        |
|    HMGET     |                 获取所有给定字段的值                 |      `HMGETabcc`       |
|    HMSET     |   同时将多个field-value(域-值)对设置到哈希表key中    |   `HMSETaaaaabbbccc`   |
|     HSET     |        将哈希表key中的字段field的值设为value         |       `HSETaaaa`       |
|    HSETNX    |     只有在字段field不存在时，设置哈希表字段的值      |     `HSETNXaaaaa`      |
|    HVALS     |                  获取哈希表中所有值                  |       `HVALSaa`        |
|    HSCAN     |                 迭代哈希表中的键值对                 | `HSCANaa0matchaCOUNT2` |
|   HSTRLEN    | 返回哈希表key中，与给定域field相关联的值的字符串长度 |      `HSTRLENaab`      |

### 有序集合（sorted sets）

集合相关的命令如下：

|       命令       |                                描述                                |           举例           |
| :--------------: | :----------------------------------------------------------------: | :----------------------: |
|       ZADD       |       向有序集合添加一个或多个成员，或者更新已存在成员的分数       |       `ZADDx100v1`       |
|      ZCARD       |                        获取有序集合的成员数                        |         `ZCARDx`         |
|      ZCOUNT      |                计算在有序集合中指定区间分数的成员数                |      `ZCOUNT80100`       |
|     ZINCRBY      |            有序集合中对指定成员的分数加上增量increment             |      `ZINCRBYx10v1`      |
|   ZINTERSTORE    | 计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合key中  |    `ZINTERSTOREq2xxx`    |
|    ZLEXCOUNT     |               在有序集合中计算指定字典区间内成员数量               |      `ZLEXCOUNTx-+`      |
|      ZRANGE      |             通过索引区间返回有序集合成指定区间内的成员             |       `ZRANGEx02`        |
|   ZRANGEBYLEX    |                   通过字典区间返回有序集合的成员                   |   `ZRANGEBYLEXx[v1[v2`   |
|  ZRANGEBYSCORE   |                通过分数返回有序集合指定区间内的成员                |  `ZRANGEBYSCOREx101200`  |
|      ZRANK       |                    返回有序集合中指定成员的索引                    |        `ZRANKxv1`        |
|       ZREM       |                   移除有序集合中的一个或多个成员                   |        `ZREMxxv1`        |
|  ZREMRANGEBYLEX  |               移除有序集合中给定的字典区间的所有成员               | `ZREMRANGEBYLEXxx[v1[v2` |
| ZREMRANGEBYRANK  |               移除有序集合中给定的排名区间的所有成员               |  `ZREMRANGEBYRANKxx020`  |
| ZREMRANGEBYSCORE |               移除有序集合中给定的分数区间的所有成员               | `ZREMRANGEBYSCORExx2030` |
|    ZREVRANGE     |        返回有序集中指定区间内的成员，通过索引，分数从高到底        |     `ZREVRANGEx010`      |
| ZREVRANGEBYSCORE |         返回有序集中指定分数区间内的成员，分数从高到低排序         | `ZREVRANGEBYSCOREx20120` |
|     ZREVRANK     | 返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序 |      `ZREVRANKxv2`       |
|      ZSCORE      |                     返回有序集中，成员的分数值                     |       `ZSCORExv1`        |
|   ZUNIONSTORE    |           计算一个或多个有序集的并集，并存储在新的key中            |    `ZUNIONSTOREz2xxx`    |
|      ZSCAN       |           迭代有序集合中的元素（包括元素成员和元素分值）           |        `ZSCANx0`         |

## 参考

* <https://www.redis.com.cn/tutorial.html>
* <https://redis.io/docs/getting-started/>
