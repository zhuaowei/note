## redis

#### 1、简介

Redis 是完全开源的，遵守 BSD 协议，是一个高性能的 ==key-value== 数据库。

Redis 与其他 key - value 缓存产品有以下三个特点：

-   Redis==支持数据的持久化==，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
-   Redis不仅仅支持简单的key-value类型的数据，同时还提供==list，set，zset，hash==等数据结构的存储。
-   Redis支持==数据的备份==，即master-slave模式的数据备份。

##### 1.1、Redis 优势

-   性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。
-   丰富的数据类型 – Redis支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。
-   原子 – Redis的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。
-   丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。

##### 1.2、Redis与其他key-value存储有什么不同？

-   Redis有着更为复杂的数据结构并且提供对他们的==原子性操作==，这是一个不同于其他数据库的进化路径。Redis的数据类型都是==基于基本数据结构的同时对程序员透明==，无需进行额外的抽象。
-   Redis==运行在内存中但是可以持久化到磁盘==，所以在对不同数据集进行高速读写时需要权衡内存，因为数据量不能大于硬件内存。在内存数据库方面的另一个优点是，相比在磁盘上相同的复杂的数据结构，在内存中操作起来非常简单，这样Redis可以做很多内部复杂性很强的事情。同时，在磁盘格式方面他们是==紧凑的以追加的方式==产生的，因为他们并不需要进行随机访问。

#### 2、安装使用

##### 2.1、安装 （centos7.6）

###### 1.下载

```shell
wget http://download.redis.io/releases/redis-4.0.9.tar.gz
```

###### 2、解压

```shell
tar xzf redis-4.0.9.tar.gz
```

###### 3、移动

```shell
sudo mv ./redis-4.0.9 /usr/local/redis/
cd /usr/local/redis/
```

###### 4、生成

```shell
sudo make
```

###### 5、测试

```shell
sudo make test
```

>   如果遇到这个错误或者其它error：You need tcl 8.5 or newer in order to run the Redis test
>
>   先安装 tcl：
>
>   ```javascript
>   wget http://downloads.sourceforge.net/tcl/tcl8.6.1-src.tar.gz  
>   sudo tar xzvf tcl8.6.1-src.tar.gz  -C /usr/local/  
>   cd  /usr/local/tcl8.6.1/unix/  
>   sudo ./configure  
>   sudo make  
>   sudo make install 
>   ```

然后重新进入redis解压目录，重新测试

```shell
cd /usr/local/redis/
sudo make test
```

###### 6、安装命令

```shell
sudo make install
cd /usr/local/bin
```

###### 7、移动配置

```shell
sudo cp /usr/local/redis/redis.conf /etc/redis/
```

##### 2.2、配置

修改配置文件

`/etc/redis/redis.conf`

```tex
bind 127.0.0.1 # 绑定的地址
port 6379 # 绑定的端口
daemonize yes # 以守护进程方式运行，类似服务
```

远程连接

```tex
bind 127.0.0.1 # 注释掉这行
protected-mode no # 改为 no，即允许远程访问。
requirepass foobared # 设置密码，把 foobared改成你自己的密码
```

远程登录使用 `redis-cli -h xxx.xxx.xxx.xxx` 命令，然后输入 `auth xxx` ，xxx即设置的密码。

>   如果使用阿里云服务器，记得打开响应的端口。

##### 2.3、常用命令

```shell
# 服务器端的命令
redis-server
# 查看redis服务进程
ps aux | grep redis
# 杀死redis服务
sudo kill -9 pid
# 指定加载的配置文件
redis-server /etc/redis/redis.conf
```

通用命令

| 命令   | 示例            | 说明                      |
| ------ | --------------- | ------------------------- |
| select | select 0        | 选择0号数据库             |
| set    | set name lily   | 设置key=name，value=lily  |
| get    | get hello       | 获取key=hello的结果       |
| keys   | keys he*        | 根据pattern查找符合的key  |
| dbsize | dbsize          | 返回key的总数             |
| exists | exists a        | 检查key=a是否存在         |
| del    | del a           | 删除key=a的数据           |
| expire | expire hello 20 | 设置key=hello 20秒后过期  |
| ttl    | ttl hello       | 查看key=hello的过期时间。 |

#### 3、数据类型

Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。

##### 3.1、string

string 是redis最基本的数据类型，一个key对应一个value，一个string类型的值对打能存储512MB。

实例：

```sql
SET hello 'hello redis' // 设置值
GET hello // 获取值
DEL hello // 删除一个键值对

mset name lily sex 1 age 18 // 一次性设置多个
mget name sex age // 一次行获取多个
clear // 清除屏幕

incr age // 自增1，只能对整数
decrby age 3 // 自减3
```

##### 3.2、hash

redis hash是一个键值对集合，是一个String类型的field和value的映射表，很适合存储对象。

一个hash可以存储 2³² - 1 个键值对（40多亿）。

| 命令                          | 示例                 | 说明                                           |
| ----------------------------- | -------------------- | ---------------------------------------------- |
| hget                          | hget emp:1 age       | 获取emp:1的age属性值                           |
| hset                          | hset emp:1 name lily | 设置emp:1的name属性                            |
| hmset<br />hmget<br />hgetall | hgetall emp:1        | 获取emp:1的所有属性，奇数行是field，偶数行是值 |
| hdel                          | hdel emp:1 name      | 删除hash中的一个field                          |
| hexists                       | hexists emp:1 name   | 判读是否存在                                   |
| hlen                          |                      |                                                |

```sql
HMSET test field1 "hello" field2 "world"
HGET test field1
```

##### 3.3、List

字符串列表，按照插入顺序排序。也可以从头和尾插入。

一个list同样可以存储 2³² - 1 个（40多亿）。

| 命令  |                     |                     |
| ----- | ------------------- | ------------------- |
| lpush | lpush listkey a b c | 从左边依次插入a,b,c |
| rpush | rpush listkey d e f | 从右边依次插入d,e,f |
| lpop  | lpop listkey a b c  | 从左边依次弹出a,b,c |
| rpop  | rpop listkey d e f  | 从右边依次弹出d,e,f |

>   如果 lpop/rpop 不指定参数，会弹出相应的两端的元素。

```sql
lpush test redis
lpush test mysql
lpush test mongodb
lrange test 0 10 // 查找
```

##### 3.4、set

无序，不重复的集合，是通过hash表实现的，所以添加、删除、查找的复杂度都是O(1)。

```sql
sadd test redis
sadd test mongodb
sadd test mongodb // 这条不会插入
smembers test // 查看set
```

可以存储 2³² - 1 个（40多亿）。

集合运算

| 命令   | 示例             | 说明                     |
| ------ | ---------------- | ------------------------ |
| sinter | sinter set1 set2 | 取交集                   |
| sunion | sunion set1 set2 | 取并集                   |
| sdiff  | sdiff set1 set2  | set1中有，set2没有的元素 |

##### 3.5、zset

有序集合，同样不允许重复，但是有序。不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。

```sql
zadd test 0 redis
zadd test 0 mongodb
zadd test 0 mongodb // 如果存在，更新分数
zrange test 0 -1 withscores // -1 代表所有，
ZRANGEBYSCORE test 0 1000 // 根据分数查找，0 - 1000的元素
```

#### 4、命令

##### 4.1、HyperLogLog

##### 4.2、发布订阅

redis发布订阅（pub/sub）是一种消息通信模式：订阅者发送消息，订阅者接受消息。

redis客户端可以订阅任意数量的频道。

>   client1

```sql
// 创建，订阅频道
SUBSCRIBE firstChannel
```

>   client2

```sql
// 发布消息
PUBLISH firstChannel "message from subscribe"
```

| 命令         | 使用 | 描述                         |
| ------------ | ---- | ---------------------------- |
| SUBSCRIBE    |      | 订阅一个或多个频道           |
| PSUBSCRIBE   |      | 订阅一个或多个符合模式的频道 |
| PUBSUB       |      | 查看订阅和发布状态           |
| UNSUBSCRIBE  |      | 退订所有参数指定的频道       |
| PUNSUBSCRIBE |      | 退订所有符合模式的频道       |
| PUBLISH      |      | 发布订阅到指定频道           |

##### 4.3、事务

redis事务执行特点：

1.  命令在结束事务前被放入缓存队列
2.  执行事务过程中，其中个别命令失败不会影响前后的命令执行，前面的不回滚，后面的也继续执行
3.  事务执行时，其他客户端提交到命令不会在事务指定的命令队列。

| 命令    | 使用          | 描述                                       |
| ------- | ------------- | ------------------------------------------ |
| MULTI   | MULTI         | 标记事务开始                               |
| EXEC    | EXEC          | 标记事务结束                               |
| DISCARD | DISCARD       | 取消事务，放弃执行命令                     |
| WATCH   | WATCH key ... | 监控key，如果key被其他命令改动，则中断事务 |
| UNWATCH | UNWATCH       | 取消监视所有key                            |

##### 4.4、脚本

##### 4.5、redis连接

| 命令   | 使用            | 描述             |
| ------ | --------------- | ---------------- |
| AUTH   | AUTH “password” | 验证密码是否正确 |
| ECHO   | ECHO message    | 打印消息         |
| PING   | PING            | 测试服务是否运行 |
| QUIT   | QUIT            | 退出             |
| SELECT | SELECT index    | 选择数据库       |

##### 4.6、客户端

