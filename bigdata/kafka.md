## 第一章 Kafka概述

### 1.1、定义

传统定义：kafka是一个**分布式**的基于**发布订阅模式**的**消息队列**。kafka发布消息分成不同的类别，不是直接将消息发送给订阅者，而是订阅者选择感兴趣的消息。用于日志与分布式存储之间由于数据量过大而无法承载时，作为消息中间件作为缓冲。

最新定义：kafka是一个开源的分布式事件流处理平台，被数千家公司用于高性能数据管道、流分析、数据集成和关键任务应用。也就是它可以做更多事情，包括数据存储、处理分析、计算、集成。

### 1.2、消息队列

#### 1.2.1、消息队列应用场景

1、缓存/消峰：处理短时间大量数据时，把大量数据存储到消息队列，然后从消息队列慢慢读取。

例如双十一零点秒杀，服务器无法承受巨大压力，可以使用消息队列作为缓冲。

2、解耦：可以让数据源和存储计算直接解耦。例如数据源有3个、目的地有4个，那么就要在12个地方进行数据源与目的地之间的适配。解耦之后，使用kafka作为中间件，把消息全部发送到kafka，读取也从kafka，这样只需要写7个地方的代码。

3、异步通信：可以将不紧急或者不重要的操作放到消息队列中，先做要紧的事。队列中的成不成功也无所谓。

#### 1.2.2、消息队列两种模式

1、点对点

一个生产者只服务一个消费者。生产者将消息发送到消息队列，生产者从消息队列中读取消息，然后给消息队列发送一个读取的信息，然后消息队列删除已读的消息。

2、发布订阅

生产者发布不同主题的消息，消费者按需选择不同主题的消息。不同的生产者发送消息到消息队列，按主题进行分类存储，消费者从消息队列中选择某主题的消息，读取后消息队列并不删除，其他消费者也可以再次读取，也可以读取另一个主题的消息。

### 1.3、基础架构

1、一个主题队列分为不同的分区，存储在分布式集群中。

2、消费者是分成组的，组内的消费者并行消费消息队列。

3、同一个分区的主题会有备份，会有一个leader和多个follower，leader就是原本的消息队列，follower是它的副本。

## 第二章 Kafka快速入门

### 2.1、Kafka集群搭建



略



### 2.2、Kafka命令行操作

启动kafka之前一定要先启动zookeeper，停止kafka时要先把kafka停掉，过一会再停掉zookeeper。

zookeeper启动停止命令：

```shell
# 启动命令
bin/zookeeper-server-start.sh -daemon config/zookeeper-server.properties

# 停止命令
bin/zookeeper-server-stop.sh
```

kafka启动停止命令

```shell
# 启动命令
bin/kafka-server-start.sh -daemon config/server.properties

# 停止命令
bin/kafka-server-stop.sh
```

#### 2.2.1、主题命令行操作

使用命令对主题进行操作（增删改查），下面是一些常用的命令参数：

| 参数                               | 含义                      |
| ---------------------------------- | ------------------------- |
| `--bootstrap-server <server:port>` | 连接kafka的节点，是必须的 |
| `--topic`                          | 对主题进行操作            |
| `--create`                         | 创建主题                  |
| `--delete`                         | 删除主题                  |
| `--alter`                          | 修改主题（分区）          |
| `--list`                           | 列出所有主题              |
| `--describe`                       | 查看主题的详细信息        |
| `--partitions <Integer>`           | 设置分区数                |
| `--replication-factor <Integer>`   | 设置副本数                |
| `--config <String>`                | 更新系统的默认配置        |

具体命令行操作如下：

**创建主题**

```shell
# 创建主题
bin/kafka-topics.sh --bootstrap-server hadoop102:9092,hadoop103:9092 --topic first --create --partitions 1 --replication-factor 3
```

>   1.   使用 kafka-topics.sh 启动
>   2.   r、--partitions、--replication-factor 是创建主题必须的，否则不会执行。
>   3.   --bootstrap-server 后面可以跟多台机器，以防有机器挂掉执行失败。
>
>   但是实测，不指定分区，不指定副本数量也是可以创建的，默认值是1， 1（单节点）。kafka版本是3.2.1，不知道是从哪个版本开始变的

**列出主题**

```shell
bin/kafka-topics.sh --bootstrap-server hadoop102:9092 --list
```

**查看主题的详细信息**

```shell
bin/kafka-topics.sh --bootstrap-server hadoop102:9092 --topic first --describe
```

**修改主题配置**

```shell
bin/kafka-topics.sh --bootstrap-server hadoop102:9092 --topic first --alter --partitions 3
```

>   1.   分区只能增加，不能减少。因为分区减少，消费者就找不到被删除的分区了。
>   2.   副本数量不能通过命令行修改。

**删除主题**

```shell
bin/kafka-topics.sh --bootstrap-server hadoop102:9092 --topic first --delete
```

#### 2.2.2、生产者命令行操作

使用命令行操作生产者，然后发送消息到消息队列。

```shell
bin/kafka-console-producer.sh --bootstrap-server hadoop102:9092 --topic first
```

执行完上面的命令后，就进入了交互式窗口，可以输入消息，回车发送。

#### 2.2.3、消费者命令行操作

使用控制台的消费者，消费主题中的数据。

```shell
bin/kafka-console-consumer.sh --bootstrap-server hadoop102:9092 --topic first
```

这样一旦生产者发送数据到主题，消费者就可以消费到数据。

如果要从头开始消费生产者产生的数据，就要使用 `--fr0m-beginning` 参数。

```shell
bin/kafka-topics.sh --bootstrap-server hadoop102:9092 --topic first --describe --from-beginning
```

## 第三章 Kafka生产者

### 3.1、生产者消息发送流程

#### 3.1.1、发送原理

#### 3.1.2、生产者重要参数列表

### 3.2、异步发送API

异步发送是各分区的batch独立发送，互不干扰。

#### 3.2.1、普通异步发送



使用java连接Kafka，并创建生产者发送消息到指定主题。

```java
public class KafkaProducerDemo {
    public static void main(String[] args) {
        // 0 新建配置
        Properties properties = new Properties();
        // 配置kafka服务主机
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        // 配置String序列化的类
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        // 1 创建kafka生产者，需要传入配置
        KafkaProducer<String, String> kafkaProducer = new KafkaProducer<>(properties);
        // 2 发送消息到主题，使用 producerRecord
        for (int i = 0; i < 5; i++) {
            kafkaProducer.send(new ProducerRecord<>("first", i + ": hello, kafka!"));
        }

        // 3 最后关闭producer
        kafkaProducer.close();

    }
}
```

#### 3.2.2、带回调的异步发送

带回调的也非常简单，在发送函数中新建Callback回调函数。

```java
public class KafkaProducerCallback {
    public static void main(String[] args) {
        // 0 新建配置
        Properties properties = new Properties();
        // 配置kafka服务主机
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        // 配置String序列化的类
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        // 1 创建kafka生产者，需要传入配置
        KafkaProducer<String, String> kafkaProducer = new KafkaProducer<>(properties);
        // 2 发送消息到主题，使用 producerRecord
        for (int i = 0; i < 5; i++) {
            /***** 传入回调方法 *****/
            kafkaProducer.send(new ProducerRecord<>("first", i + ": hello, kafka!"), new Callback() {
                @Override
                public void onCompletion(RecordMetadata recordMetadata, Exception e) {
                    if (e == null) {
                        System.out.println("topic: " + recordMetadata.topic()
                                + ", partition: " + recordMetadata.partition());
                    } else {
                        e.printStackTrace();
                    }
                }
            });
        }

        // 最后关闭producer
        kafkaProducer.close();
    }
}

```

### 3.3、同步发送API

同步发送是等分区中的全部发送完毕之后才能进行下一批的发送，是所有分区同步发送。也是非常简单，在send函数之后调用get方法即可。

```java
public class KafkaProducerDemo {
    public static void main(String[] args) {
        // 0 新建配置
        Properties properties = new Properties();
        // 配置kafka服务主机
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        // 配置String序列化的类
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        // 1 创建kafka生产者，需要传入配置
        KafkaProducer<String, String> kafkaProducer = new KafkaProducer<>(properties);
        // 2 发送消息到主题，使用 producerRecord
        for (int i = 0; i < 5; i++) {
            /***** send之后调用get *****/
            kafkaProducer.send(new ProducerRecord<>("first", i + ": hello, kafka!")).get();
        }

        // 3 最后关闭producer
        kafkaProducer.close();

    }
}
```

### 3.4、生产者分区

#### 3.4.1、分区好处

1.   便于合理使用存储资源。如果每台机器磁盘大小不一样，我们可以将文件多存储到磁盘多的机器，也叫负载均衡。
2.   提高并行度。存储为多个分区，便于消费者并行消费。

#### 3.4.2、生产者发送消息的分区策略

1.   如果指定了分区，那么就直接存储到该分区。
2.   如果没有指定分区，但是有key，将key的hash值与分区数取余。得到应该存储的分区。
3.   如果没有指定分区也没有key，会优先选择上个使用的分区，如果上个分区满了就随机选择一个分区，如果随机到了上个使用的分区，就再次执行随机，直到不是上个分区。

#### 3.4.2、自定义分区器

需要实现Patitioner接口中的方法。只需要实现 partition 方法即可，返回值就是分区。

```java
public class MyPartitioner implements Partitioner {
    /**
     *
     * @param s 主题
     * @param o key
     * @param bytes 序列化后的key
     * @param o1 value
     * @param bytes1 序列化后的value
     * @param cluster
     * @return
     */
    @Override
    public int partition(String s, Object o, byte[] bytes, Object o1, byte[] bytes1, Cluster cluster) {
        String value = o1.toString();
        int partition;
        if (value.contains("hello")) {
            partition = 0;
        } else {
            partition = 1;
        }
        return partition;
    }

    @Override
    public void close() {

    }

    @Override
    public void configure(Map<String, ?> map) {

    }
}
```

写好自定义分区器后，将分区器应用到producer。

```java
// 使用自定义的分区器
properties.put(ProducerConfig.PARTITIONER_CLASS_CONFIG, MyPartitioner.class.getName());
```

### 3.5、生产经验——如何提高吞吐量

吞吐量与四个值有关：

1.   batch.size：批大小，默认16k，这一批满了，或者等待时间达到最大发送。
2.   linger.ms：批发送的最长等待时间，默认0ms，即立即发送。一般调整到5 - 100ms。
3.   缓冲区大小：如果分区特别多，缓冲区放不下这么多批，那么后面的只能等待。这个时候就要提高缓冲区的大小，默认是32m。
4.   压缩：序列化的数据越小，一批装的越多，吞吐量越大。常用的压缩有谷歌的snappy。

```java
// 缓冲区大小，默认32m
properties.put(ProducerConfig.BUFFER_MEMORY_CONFIG, 1 << 25);
// 批大小，默认16k
properties.put(ProducerConfig.BATCH_SIZE_CONFIG, 1 << 14);
// 等待时间，默认0ms
properties.put(ProducerConfig.LINGER_MS_CONFIG, 5);
// 压缩策略
properties.put(ProducerConfig.COMPRESSION_TYPE_CONFIG, "snappy");
```

### 3.6、生产经验——数据可靠性

数据可靠性指的是数据发送到数据存储完毕这个过程，如何保证数据存储的完整性。

当一批数据满了之后或者到了最大等待时间后，就发送数据给kafka集群，集群执行存储策略。集群存储之后如何给发送方一个响应，这是保证数据可靠性的关键。

数据发送给Kafka集群的Leader，Leader有三种应答策略：

1.   acks = 0，即不回应，发送方也不知道发送成功没有，发送成功后集群正常存储没有。所以不会采用这种策略，但是这种策略效率比较高。
2.   acks = 1，只需要Leader回应。Leader将发送的数据存储到磁盘后，就发送响应。效率中等，数据可靠性中等。如果Leader发送之后就挂了，那么下次发送收不到回应就会选择另一个Leader，这次存储的数据就丢失了。
3.   acks = -1/all，需要所有Followers都存储到磁盘后再由Leader发送响应。效率低，数据可靠性高。缺点是会造成数据重复，如果所有Follower都存储完毕，但是这个时候Leader挂了，那么发送方就会重新发送，选择新的Leader，那已经存储的数据就作废了，再存一遍数据。另外，如果Follower长时间不回应怎么办，这个是Leader实时维护的ISR列表能解决的问题。如果Follower超过一定时间没有发送同步请求或者没有发送回应，就将它踢出ISR列表。

下面使用Java配置Producer的应答机制。

```java
properties.put(ProducerConfig.ACKS_CONFIG, "1"); // 使用 acks = 1
properties.put(ProducerConfig.RETRIES_CONFIG, 3); // 重试次数
```





