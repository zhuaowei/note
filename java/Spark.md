# Spark

## 一 、概述

### 1.1、简介

Spark是基于内存的、可扩展的、通用的大数据计算引擎。Hadoop也是一种大数据引擎。

Spark和Hadoop都是 apache 公司的项目，Spark起步较晚。

### 1.2、Spark vs Hadoop

hadoop 是一次性计算，它从磁盘中读取数据，计算完成后再将结果存储到磁盘中，多个作业实现通信是通过读写磁盘。

Spark 多个作业之间是基于内存的。由于它是基于内存的，所以它处理任务会比Hadoop快，但是如果内存不足，就只能使用Hadoop。所以他们不能相互替代。

### 1.3、核心模块

Spark有几个核心模块，Spark SQL、Spark Streaming、Spark MLib、Spark GraphX。这几个模块都是基于Spark Core实现的。

1.   Spark Core：实现了最基础的、最核心的功能，其他模块都是在此基础上扩展的。
2.   Spark SQL：Spark SQL 是操作结构化数据的
3.   Spark Streaming：操作流式数据的
4.   Spark MLib：机器学习模块
5.   Spark GraphX：图形计算模块

### 1.4、环境准备

JDK1.8

Hadoop2.7.2

Scala2.12

Spark2.4.8

### 1.5、WordCount

打开IDEA，新建Maven项目，添加Scala SDK（打开project Structure，Global Libraries，添加Scala SDK）。

添加Spark-core依赖和Scala插件。

```scala
import org.apache.spark.{SparkConf, SparkContext}

object WordCount {
  def main(args: Array[String]): Unit = {
    // 输入文件地址
    val inputFile = "file:///E:/Project/java/scala_wordcount/data/word.txt"
    // 定义自己的Spark项目配置
    val conf = new SparkConf().setAppName("WordCount").setMaster("local")
    // 创建Saprk实例，只有这个实例能够与Spark集群进行通信
    val sc = new  SparkContext(conf)
    val textFile = sc.textFile(inputFile)
    /**
     * textFile是RDD[String]类型的，类似集合
     * 将没一行按空格分割，然后处理到一个RDD[String] 中
     * map 将每一个单词创建一个二元组 ("word", 1) 
     * reduceByKey 这个函数进行了两步操作，第一步是根据key将数据分组，第二步是执行reduce操作
     * 对每一个分区的 value 进行处理，a 一个key的value，b另一个key的value
     * a + b 结束后，结果成为下一个执行的 a，直到最终加完。如果只有一个，根本不会进入这个lambda函数
     */
    val wordCount = textFile.flatMap(line => line.split(" "))
      .map(word => (word, 1)).reduceByKey((a, b) => a + b)
    wordCount.foreach(println)

  }
}
```

### 1.6、Spark Shell

spark shell是spark的scala交互式解释器，类似Pyhton shell，可以执行Scala命令。

进入shell默认有一个sc，如果用户再定义sc是不生效的。可以通过启动shell的命令定义 shell。

shell有两个参数，第一是指令spark运行的模式和分区数量，第二个参数是执行的包。这个包可以是Jar文件，也可以是maven依赖。

```shell
./bin/spark-shell --master local
./bin/spark-shell --master local[4] --jars code.jar # 多个jar用逗号分隔
./bin/spark-shell --master local[4] --packages "org.example:example:0.1"
```

## 二、Spark框架

### 2.1、环境

环境是Spark运行的模式，例如本地，standalone，yarn等

#### 2.1.1、本地环境

不依赖其他任何节点就能运行的环境，一般用于开发调试。

直接解压压缩包就可以运行本地模式。

```shell
./bin/spark-shell # 在spark根目录执行
```

执行命令后就进入了scala命令行

**访问本地的4040端口，就可以看到执行过的任务。**

在开发中，将写好的程序打成 Jar 包，提交到Spark执行。

```shell
./bin/spark-submit \
--class <classname> \
--master local \
--jars <pathToJar>
```

#### 2.1.2、standalone

独立部署模式，包含主从节点，具体配置可以参考网上教程。

#### 2.1.3、Yarn

#### 2.1.4、Windows

### 2.2、核心组件

#### 2.2.1、Driver

Spark的驱动器节点用于执行Spark任务中的 main 方法。

1.   将程序转化成Saprk作业
2.   在Executor之间调度任务
3.   跟踪Executor的执行情况
4.   通过UI展示查询运行的情况

#### 2.2.2、Executor

集群中工作节点（Worker）中的一个JVM进程，负责在 Spark 作业中运行具体任务，任务彼此之间相互独立。Spark 应用启动时，Executor节点被同时启动。

1.   负责运行组成Spark应用的任务，并将结果返回为驱动器进程
2.   他们通过自身的块管理器（Block Manager）为用户程序中要求缓存RDD提供内存式存储。RDD是直接缓存在Executor进程内的，因此任务可以在运行时充分利用缓存数据加速运算。

#### 2.2.3、Master 和 Workor

Master是一个进程，负责资源调度和分配，并进行集群的监控等职责。

Worker也是一个进程，一个Worker运行在集群中的一台服务器上，有Master分配资源对数据进行并行的处理和计算。

#### 2.2.4、ApplicationMaster

Hadoop用户向yarn集群提交应用程序时，提交程序中应该包含ApplicationMaster，用于向资源调度七申请执行任务的资源容器Container，用户在自己的程序任务job，监控整个任务的执行，跟踪整个任务的状态，处理任务失败等异常情况。

### 2.3、核心概念

#### 2.3.1、Executor与Core

一个Executor就是一个工作节点，在启动时，可以通过参数配置节点的个数和节点的配置。

```shell
--num-executor 节点数量
--executor-memory 每个节点的内存大小
--executor-cores 每个节点的cpu核数
```

#### 2.3.2、并行度

Spark执行任务一般是多个任务同时执行，由于分布在不同的节点，所以可以做到并行执行（不是并发，并发是同一时刻，有多个任务执行）。执行任务的个数称为并行度，也可以通过配置改变并行度。

#### 2.3.3、DAG（有向无环图）

指的是项目的依赖关系是有向无环图，如果有环，则会形成死循环。

Maven中的依赖关系也是一样。

#### 2.3.4、提交流程

将一个任务条给spark执行后，有两条执行流程，一条是资源，一条是计算。

spark应用程序提交到yarn环境中执行，一般有两种执行模式，一是Client，一是Cluster，区别在于执行是Driver程序执行的节点位置。

## 三、Spark Core

### 3.1、RDD

#### 3.1.1、什么是RDD？

RDD（Resilient Distributed Dataset）是弹性分布式数据集，是Spark中最基本的数据处理模型。代码中是一个抽象类，它代表一个弹性的、不可变的、可分区的、里面的元素可并行计算的集合。

1.   弹性
     1.   存储的弹性：内存与磁盘自动切换（内存不够会自动存储到磁盘）
     2.   容错的弹性：数据丢失可以自动回复（因为程序知道数据从哪里来，如何进行分区）
     3.   计算的弹性：计算出错重试机制
     4.   分片的弹性：根据需要重新分片
2.   分布式：数据存储在大数据集群的不同节点
3.   数据集：RDD封装了计算了逻辑，并不保存数据（对数据的操作其实就是RDD的封装，就是计算的逻辑）
4.   数据抽象：RDD是一个抽象类，需要子类具体实现（不同的阶段都有不同的RDD子类，类似IO的装饰模式，每一个子类都在父类的基础上扩展功能）
5.   不可变：RDD封装了计算逻辑，是不可以改变的，想要改变，只能产生新的RDD，在新的RDD里面封装计算逻辑。
6.   可分区，并行计算

#### 3.1.2、核心属性

































### 3.2、Spring Core

## 五、Spark 源码

## 六、Spark Streaming

## 七、Spark 

