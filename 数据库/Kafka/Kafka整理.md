# 一、Kafka简介

## 1.1 概述

Kafka是最初由Linkedin公司开发，是一个分布式、分区的、多副本的、多订阅者，基于zookeeper协调的分布式日志系统（也可以当做MQ系统），常见可以用于web/nginx日志、访问日志，消息服务等等，Linkedin于2010年贡献给了Apache基金会并成为顶级开源项目。

主要应用场景是：日志收集系统和消息系统。

![](https://images2018.cnblogs.com/blog/1385722/201808/1385722-20180804221732434-2116774825.png#id=JeQsB&originHeight=356&originWidth=696&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

Kafka设计原则：

- **高吞吐**、低延迟：Kakfa 最大的特点就是收发消息非常快，kafka 每秒可以处理几十万条消息，它的最低延迟只有几毫秒。
- **高伸缩性**： 每个主题(topic) 包含多个分区(partition)，主题中的分区可以分布在不同的主机(broker)中。
- **持久性、可靠性**： Kafka 能够允许数据的持久化存储，消息被持久化到磁盘，并支持数据备份防止数据丢失，Kafka 底层的数据存储是基于 Zookeeper 存储的，Zookeeper 我们知道它的数据能够持久存储。
- **容错性**： 允许集群中的节点失败，某个节点宕机，Kafka 集群能够正常工作。
- **高并发**： 支持数千个客户端同时读写。

## 1.2 基本术语

![](https://images2018.cnblogs.com/blog/1228818/201805/1228818-20180507190731172-1317551019.png#id=EuUtW&originHeight=425&originWidth=842&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

- **controller/master**：集群中控制器，它负责管理整个集群中所有分区和副本的状态和元数据的管理
- **Producer**：消息生产者，就是向 kafka broker 发消息的客户端；
- **Consumer**：消息消费者，向 kafka broker 取消息的客户端；
- **Consumer Group**（CG）：消费者组，由多个consumer 组成。消费者组内每个消费者负责消费不同分区的数据，一个分区只能由一个组内消费者消费；消费者组之间互不影响。所有的消费者都属于某个消费者组，即消费者组是逻辑上的一个订阅者。
- **Broker**：一台kafka服务器就是一个broker。一个集群由多个 broker 组成。一个 broker可以容纳多个 topic。为了支持Producer直接向Leader Partition写数据，所有的Kafka服务节点都支持Topic Metadata的请求，返回哪些Server节点存活的、Partition的Leader节点的分布情况。
- **Topic**：可以理解为一个队列，生产者和消费者面向的都是一个 topic；
- **Partition**：为了实现扩展性，一个非常大的topic可以分布到多个broker（即服务器）上，一个 topic可以分为多个partition，每个partition是一个有序的队列；
- **Replica**：副本，为保证集群中的某个节点发生故障时，该节点上的partition数据不丢失，且kafka仍然能够继续工作，kafka提供了副本机制，一个topic的每个分区都有若干个副本，一个leader和若干个follower。
- **leader**：每个分区多个副本的“主”，生产者发送数据的对象，以及消费者消费数据的对象都是leader。
- **follower**：每个分区多个副本中的“从”，实时从leader中同步数据，保持和 leader数据的同步。leader发生故障时，某个follower会成为新的 follower。
- **Offset**：消费者消费的位置信息，监控数据消费到什么位置，当消费者挂掉再重新恢复的时候，可以从消费位置继续消费。
- **ZooKeeper服务**：Kafka 集群能够正常工作，需要依赖于 ZooKeeper，ZooKeeper 帮助 Kafka 存储和管理集群元数据信息。在最新版本中, 已经慢慢要脱离 ZooKeeper。

![](https://mmbiz.qpic.cn/mmbiz_png/FrBePKkiazprsia87BW5t5obib0WYTq6GavNicVBCBKxK5vSLNfib7QKFuo00Wl4Vs8SslKSOsCt52c2iaBIBPkUYibeA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1#id=z08nS&originHeight=315&originWidth=842&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://mmbiz.qpic.cn/mmbiz_png/FrBePKkiazprsia87BW5t5obib0WYTq6Gav3mtHFk0UtFhzgfLqmwWqaz78prwbLlc5Xd0H2Go3lhbu8AHzOUDLPg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1#id=JkqJj&originHeight=523&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 1.3 使用场景

- **限流削峰**：Kafka 多用于互联网领域某一时刻请求特别多的情况下，可以把请求写入Kafka 中，避免直接请求后端程序导致服务崩溃。
- **传递消息**：Kafka 另外一个基本用途是传递消息，应用程序向用户发送通知就是通过传递消息来实现的，这些应用组件可以生成消息，而不需要关心消息的格式，也不需要关心消息是如何发送的。
- **活动跟踪**：Kafka 可以用来跟踪用户行为，比如我们经常回去淘宝购物，你打开淘宝的那一刻，你的登陆信息，登陆次数都会作为消息传输到 Kafka ，当你浏览购物的时候，你的浏览信息，你的搜索指数，你的购物爱好都会作为一个个消息传递给 Kafka ，这样就可以生成报告，可以做智能推荐，购买喜好等。
- **日志记录**：Kafka 的基本概念来源于提交日志，比如我们可以把数据库的更新发送到 Kafka 上，用来记录数据库的更新时间，通过kafka以统一接口服务的方式开放给各种consumer，例如hadoop、Hbase、Solr等。
- **流式处理**：流式处理是有一个能够提供多种应用程序的领域。
- **度量指标**：Kafka也经常用来记录运营监控数据。包括收集各种分布式应用的数据，生产各种操作的集中反馈，比如报警和报告。

## 1.4 消息队列模式

Kafka 的消息队列一般分为两种模式：点对点模式和发布订阅模式。
Kafka 是支持消费者群组的，也就是说 Kafka 中会有一个或者多个消费者，如果一个生产者生产的消息由一个消费者进行消费的话，那么这种模式就是点对点模式。
如果一个生产者或者多个生产者产生的消息能够被多个消费者同时消费的情况，这样的消息队列成为发布订阅模式的消息队列。

#### 1.4.1 点对点消息传递

在点对点消息系统中，消息持久化到一个队列中。此时，将有一个或多个消费者消费队列中的数据。但是一条消息只能被消费一次。当一个消费者消费了队列中的某条数据之后，该条数据则从消息队列中删除。该模式即使有多个消费者同时消费数据，也能保证数据处理的顺序。这种架构描述示意图如下：

![](https://images2018.cnblogs.com/blog/1228818/201805/1228818-20180507190326476-771565746.png#id=P4O4H&originHeight=452&originWidth=874&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### 1.4.3  发布-订阅消息传递

在发布-订阅消息系统中，消息被持久化到一个topic中。与点对点消息系统不同的是，消费者可以订阅一个或多个topic，消费者可以消费该topic中所有的数据，同一条数据可以被多个消费者消费，数据被消费后不会立马删除。在发布-订阅消息系统中，消息的生产者称为发布者，消费者称为订阅者。该模式的示例图如下：

![](https://images2018.cnblogs.com/blog/1228818/201805/1228818-20180507190443404-1266011458.png#id=RdzBQ&originHeight=488&originWidth=926&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 1.5 核心API

Kafka 有五个核心API，它们分别是

- **Producer** API，它允许应用程序向一个或多个 topics 上发送消息记录
- **Consumer** API，允许应用程序订阅一个或多个 topics 并处理为其生成的记录流
- **Streams** API，它允许应用程序作为流处理器，从一个或多个主题中消费输入流并为其生成输出流，有效的将输入流转换为输出流。
- **Connector** API，它允许构建和运行将 Kafka 主题连接到现有应用程序或数据系统的可用生产者和消费者。
- **Admin** API，它用于管理和检查topic，broker和其他Kafka对象。

## 1.6  kafka集群架构

### 1.6.1 工作流程

在了解kafka集群之前, 我们先来了解下kafka的工作流程, Kafka集群会将消息流存储在 Topic 的中，每条记录会由一个Key、一个Value和一个时间戳组成。

![](https://mmbiz.qpic.cn/mmbiz_png/FrBePKkiazpqp2KIDPBicGrHV0oWKfZJpNL1RAMicRz7ILEwCibLLyORL0Eb0GK2ffZ5Fbc0iaTicsOvQLST9GwqMhBw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1#id=nPrxM&originHeight=510&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

Kafka 中消息是以 Topic 进行分类的，生产者生产消息，消费者消费消息，读取和消费的都是同一个 Topic。但是Topic 是逻辑上的概念， Partition 是物理上的概念，每个 Partition 对应一个 log 文件，该 log 文件中存储的就是 Producer 生产的数据。**Producer 端生产的数据会不断顺序追加到该 log 文件末尾，并且每条数据都会记录有自己的 Offset**。而消费者组中的每个消费者，也都会实时记录当前自己消费到了哪个 Offset，方便在崩溃恢复时，可以继续从上次的 Offset 位置消费。

### 1.6.2 存储机制

![](https://mmbiz.qpic.cn/mmbiz_png/FrBePKkiazpqp2KIDPBicGrHV0oWKfZJpNvoEHsECkaWN2dKf9DLWNKocvydUskScwURtDcibfz1DdmUvQ63ibnz1A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1#id=ADtE5&originHeight=804&originWidth=1031&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

此时 Producer 端生产的消息会不断追加到 log 文件末尾，这样文件就会越来越大, 为了防止 log 文件过大导致数据定位效率低下，那么Kafka 采取了分片和索引机制。它将每个 Partition 分为多个 Segment，每个 Segment 对应4个文件：“.index” 索引文件, “.log” 数据文件, “.snapshot” 快照文件, “.timeindex” 时间索引文件。这些文件都位于同一文件夹下面，该文件夹的命名规则为：topic 名称-分区号。例如, heartbeat心跳上报服务 这个 topic 有三个分区，则其对应的文件夹为 heartbeat-0，heartbeat-1，heartbeat-2这样。

![](https://mmbiz.qpic.cn/mmbiz_png/FrBePKkiazpqp2KIDPBicGrHV0oWKfZJpNDnLjltkRpPdX8ibxeQPEspvtgicibSpT9aSEicNF8H1hTXyqC8GXBwgHnw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1#id=prSpW&originHeight=185&originWidth=349&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

index, log, snapshot, timeindex 文件以当前 Segment 的第一条消息的 Offset 命名。其中 “.index” 文件存储大量的索引信息，“.log” 文件存储大量的数据，索引文件中的元数据指向对应数据文件中 Message 的物理偏移量。

下图为index 文件和 log 文件的结构示意图：

![](https://mmbiz.qpic.cn/mmbiz_png/FrBePKkiazpqp2KIDPBicGrHV0oWKfZJpNyVZqezNbkLN3BpF3H0bL5O0zqOVvH90apktVib2MaTHPpnfgO8j3TtQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1#id=JHRwg&originHeight=680&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 1.6.3 Replica - 副本

kafka中的 Partition 为了保证数据安全，每个 Partition 可以设置多个副本。此时我们对分区0,1,2分别设置3个副本（注:设置两个副本是比较合适的）。而且每个副本都是有"角色"之分的，它们会选取一个副本作为 Leader 副本，而其他的作为 Follower 副本，我们的 Producer 端在发送数据的时候，只能发送到Leader Partition里面 ，然后Follower Partition会去Leader那自行同步数据, Consumer 消费数据的时候，也只能从 Leader 副本那去消费数据的。

![](https://mmbiz.qpic.cn/mmbiz_png/FrBePKkiazpqp2KIDPBicGrHV0oWKfZJpNp1BbJOhsk42zMibfF9PT5ZG1L95viaQNpPNKPvI8SQW9u2oHc1axAUPQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1#id=aesim&originHeight=503&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://mmbiz.qpic.cn/mmbiz_png/FrBePKkiazpqp2KIDPBicGrHV0oWKfZJpNJ4fdyKu9zicaeCu6gkG67mdXjZgAiakZFjicBQJiardJvPOhLzlxd6kp8g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1#id=tJDzC&originHeight=358&originWidth=1080&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 1.6.4 Controller

Kafka Controller，其实就是一个 Kafka 集群中一台 Broker，它除了具有普通Broker 的消息发送、消费、同步功能之外，还需承担一些额外的工作。Kafka 使用公平竞选的方式来确定 Controller ，最先在 ZooKeeper 成功创建临时节点 /controller 的Broker会成为 Controller ，一般而言，Kafka集群中第一台启动的 Broker 会成为Controller，并将自身 Broker 编号等信息写入ZooKeeper临时节点/controller。

### 1.6.5 Offset 的维护

Consumer 在消费过程中可能会出现断电宕机等故障，在 Consumer 恢复后，需要从故障前的 Offset 位置继续消费。所以 Consumer 需要实时记录自己消费到了哪个 Offset，以便故障恢复后继续消费。在 Kafka 0.9 版本之前，Consumer 默认将 Offset 保存在 ZooKeeper 中，但是从 0.9 版本开始，Consumer 默认将 Offset 保存在 Kafka 一个内置的 Topic 中，该 Topic 为 __consumer_offsets, 以支持高并发的读写。

## 1.7 kafka架构图

![](https://mmbiz.qpic.cn/mmbiz_png/FrBePKkiazpqp2KIDPBicGrHV0oWKfZJpN1YiaXYib5Psjs2hLESX4FIZOjvrPoASZb2Zn7Vjz6VUbSKrO2GF2wqcw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1#id=zg26e&originHeight=698&originWidth=836&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

# 二、安装

## 1.1 docker安装

1、Docker拉取相关镜像

docker pull wurstmeister/zookeeper:latest

docker pull wurstmeister/kafka

2、运行容器

docker run -d --name zookeeper -p 2181:2181 -t wurstmeister/zookeeper

docker run -d --name kafka --publish 9092:9092 --link zookeeper --env KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 --env KAFKA_ADVERTISED_HOST_NAME=localhost --env KAFKA_ADVERTISED_PORT=9092 wurstmeister/kafka


其中：1）日志目录映射到宿主机中；2）容器使用宿主机的系统时间

![](https://img-blog.csdnimg.cn/5ae19a3f4ddc4ec698176d11ccf3bd76.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAc3Vuc2hpbmVsb3ZlYm95,size_20,color_FFFFFF,t_70,g_se,x_16#id=THvXo&originHeight=50&originWidth=1810&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 1.2 平台安装

### Step 1: 下载代码

下载 1.0.0版本并解压缩：

```
tar -xzf kafka_2.11-1.0.0.tgz
cd kafka_2.11-1.0.0
```

### Step 2: 启动服务器

Kafka 使用 [ZooKeeper](https://zookeeper.apache.org/) 如果你还没有ZooKeeper服务器，你需要先启动一个ZooKeeper服务器。 您可以通过与kafka打包在一起的便捷脚本来快速简单地创建一个单节点ZooKeeper实例。

```
bin/zookeeper-server-start.sh config/zookeeper.properties
```

### Step 3: 创建一个 topic

让我们创建一个名为“test”的topic，它有一个分区和一个副本：

```
 bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```

现在我们可以运行list（列表）命令来查看这个topic：

```
 bin/kafka-topics.sh --list --zookeeper localhost:2181
```

或者，您也可将代理配置为：在发布的topic不存在时，自动创建topic，而不是手动创建。

### Step 4: 发送一些消息

Kafka自带一个命令行客户端，它从文件或标准输入中获取输入，并将其作为message（消息）发送到Kafka集群。默认情况下，每行将作为单独的message发送。

运行 producer，然后在控制台输入一些消息以发送到服务器。

```
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```

### Step 5: 启动一个 consumer

Kafka 还有一个命令行consumer（消费者），将消息转储到标准输出。

```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```

如果您将上述命令在不同的终端中运行，那么现在就可以将消息输入到生产者终端中，并将它们在消费终端中显示出来。
所有的命令行工具都有其他选项；运行不带任何参数的命令将显示更加详细的使用信息。

### Step 6: 设置多代理集群

到目前为止，我们一直在使用单个代理，这并不好玩。对 Kafka来说，单个代理只是一个大小为一的集群，除了启动更多的代理实例外，没有什么变化。 为了深入了解它，让我们把集群扩展到三个节点（仍然在本地机器上）。

首先，为每个代理创建一个配置文件 (在Windows上使用`copy` 命令来代替)：

```
cp config/server.properties config/server-1.properties
cp config/server.properties config/server-2.properties
```

现在编辑这些新文件并设置如下属性：

```
config/server-1.properties:
    broker.id=1
    listeners=PLAINTEXT://:9093
    log.dir=/tmp/kafka-logs-1
 
config/server-2.properties:
    broker.id=2
    listeners=PLAINTEXT://:9094
    log.dir=/tmp/kafka-logs-2
```

`broker.id`属性是集群中每个节点的名称，这一名称是唯一且永久的。我们必须重写端口和日志目录，因为我们在同一台机器上运行这些，我们不希望所有的代理尝试在同一个端口注册，或者覆盖彼此的数据。

我们已经建立Zookeeper和一个单节点了，现在我们只需要启动两个新的节点：

```
bin/kafka-server-start.sh config/server-1.properties &
bin/kafka-server-start.sh config/server-2.properties &
```

现在创建一个副本为3的新topic：

```
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic my-replicated-topic
```

Good，现在我们有一个集群，但是我们怎么才能知道那些代理在做什么呢？运行"describe topics"命令来查看：

```
bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic
```

以下是对输出信息的解释。第一行给出了所有分区的摘要，下面的每行都给出了一个分区的信息。因为我们只有一个分区，所以只有一行。

- “leader”是负责给定分区所有读写操作的节点。每个节点都是随机选择的部分分区的领导者。
- “replicas”是复制分区日志的节点列表，不管这些节点是leader还是仅仅活着。
- “isr”是一组“同步”replicas，是replicas列表的子集，它活着并被指到leader。

请注意，在示例中，节点1是该主题中唯一分区的领导者。

我们可以在已创建的原始主题上运行相同的命令来查看它的位置：

```
bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic test
```

让我们发表一些信息给我们的新topic：

```
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic my-replicated-topic
```

现在我们来消费这些消息：

```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --topic my-replicated-topic
```

### Step 7: 使用Connect来导入/导出数据

从控制台读出数据并将其写回是十分方便操作的，但你可能需要使用其他来源的数据或将数据从Kafka导出到其他系统。针对这些系统， 你可以使用Kafka Connect来导入或导出数据，而不是写自定义的集成代码。

Kafka Connect是Kafka的一个工具，它可以将数据导入和导出到Kafka。它是一种可扩展工具，通过运行_connectors（连接器）_， 使用自定义逻辑来实现与外部系统的交互。 在本文中，我们将看到如何使用简单的connectors来运行Kafka Connect，这些connectors 将文件中的数据导入到Kafka topic中，并从中导出数据到一个文件。

首先，我们将创建一些种子数据来进行测试：

```
echo -e "foo\nbar" > test.txt
```

在Windows系统使用:

```
echo foo> test.txt
echo bar>> test.txt
```

接下来，我们将启动两个_standalone（独立）_运行的连接器，这意味着它们各自运行在一个单独的本地专用 进程上。 我们提供三个配置文件。首先是Kafka Connect的配置文件，包含常用的配置，如Kafka brokers连接方式和数据的序列化格式。 其余的配置文件均指定一个要创建的连接器。这些文件包括连接器的唯一名称，类的实例，以及其他连接器所需的配置。

```
bin/connect-standalone.sh config/connect-standalone.properties config/connect-file-source.properties config/connect-file-sink.properties
```

在启动过程中，你会看到一些日志消息，包括一些连接器正在实例化的指示。 一旦Kafka Connect进程启动，源连接器就开始从`test.txt`读取行并且 将它们生产到主题`connect-test`中，同时接收器连接器也开始从主题`connect-test`中读取消息， 并将它们写入文件`test.sink.txt`中。我们可以通过检查输出文件的内容来验证数据是否已通过整个pipeline进行交付：

```
 more test.sink.txt
```

请注意，数据存储在Kafka topic`connect-test`中，因此我们也可以运行一个console consumer（控制台消费者）来查看 topic 中的数据（或使用custom consumer（自定义消费者）代码进行处理）：

```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic connect-test --from-beginning
```

连接器一直在处理数据，所以我们可以将数据添加到文件中，并看到它在pipeline 中移动：

```
echo Another line>> test.txt
```
