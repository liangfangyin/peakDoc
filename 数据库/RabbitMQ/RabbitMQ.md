### RabbitMQ的特点

**可靠性:**

RabbitMQ使用一些机制来保证可靠性， 如持久化、传输确认及发布确认等。

**灵活的路由:**

在消息进入队列之前，通过交换器来路由消息。对于典型的路由功能， RabbitMQ 己经提供了一些内置的交换器来实现。针对更复杂的路由功能，可以将多个 交换器绑定在一起， 也可以通过插件机制来实现自己的交换器。

**扩展性:**

多个RabbitMQ节点可以组成一个集群，也可以根据实际业务情况动态地扩展 集群中节点。

**高可用性 :**

队列可以在集群中的机器上设置镜像，使得在部分节点出现问题的情况下队 列仍然可用。

**多种协议:**

RabbitMQ除了原生支持AMQP协议，还支持STOMP， MQTT等多种消息 中间件协议。

**多语言客户端** :

RabbitMQ 几乎支持所有常用语言，比如 Java、 Python、 Ruby、 PHP、 C#、 JavaScript 等。

**管理界面** :

RabbitMQ 提供了一个易用的用户界面，使得用户可以监控和管理消息、集 群中的节点等。

**令插件机制**:

RabbitMQ 提供了许多插件 ， 以实现从多方面进行扩展，当然也可以编写自 己的插件。

### RabbitMQ优点：

**异步处理**

相比于传统的串行、并行方式，提高了系统吞吐量。如原先A接口需要调用BCD系统需要1秒，现在利用队列订阅，只需要负责A系统业务+队列写入时间即可。

**应用解耦**

系统间通过消息通信，不用关心其他系统的处理。 如A系统只需要关心自身业务，其他系统需要用A系统的数据，只需要订阅，即可实现，无需关心A系统生成的数据发送给哪些系统。

**流量削锋**

可以通过消息队列长度控制请求量；可以缓解短时间内的高并发请求。

**日志处理：**

解决大量日志传输。

**消息通讯**

消息队列一般都内置了高效的通信机制，因此也可以用在纯的消息通讯。比如实现点对点消息队列，或者聊天室等。

![](https://img-blog.csdnimg.cn/47cf832ec94a41458eccf3f3150bfaeb.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA44CB5oGw5aW9,size_20,color_FFFFFF,t_70,g_se,x_16#alt=img)

### RabbitMQ缺点：

**系统可用性降低：**

系统内部关联队列，一旦队列宕机，整个系统都瘫痪。

**系统复杂度提高：**

增加新模块可能导致系统对接和部署难度加大。

**一致性问题：**

系统写入成功，但是不能保证其他订阅模块也一起成功。

### 消息队列选型：

1、如果消息队列不是将要构建系统的重点，对消息队列功能和性能没有很高的要求，只需要一个快速上手易于维护的消息队列，建议使用RabbitMQ。

2、如果系统使用消息队列主要场景是处理在线业务，比如在交易系统中用消息队列传递订单，需要低延迟和高稳定性，建议使用RocketMQ。

3、如果需要处理海量的消息，像收集日志、监控信息或是埋点这类数据，或是你的应用场景大量使用了大数据、流计算相关的开源产品，那Kafka是最适合的消息队列。

### **四大核心概念**

**生产者**

产生数据发送消息的程序是生产者

**交换机**

交换机是 RabbitMQ 非常重要的一个部件，一方面它接收来自生产者的消息，另一方面它将消息推送到队列中。交换机必须确切知道如何处理它接收到的消息，是将这些消息推送到特定队列还是推送到多个队列，亦或者是把消息丢弃，这个得有交换机类型决定

**队列**

队列是 RabbitMQ 内部使用的一种数据结构，尽管消息流经 RabbitMQ 和应用程序，但它们只能存储在队列中。队列仅受主机的内存和磁盘限制的约束，本质上是一个大的消息缓冲区。许多生产者可以将消息发送到一个队列，许多消费者可以尝试从一个队列接收数据。这就是我们使用队列的方式

**消费者**

消费与接收具有相似的含义。消费者大多时候是一个等待接收消息的程序。请注意生产者，消费者和消息中间件很多时候并不在同一机器上。同一个应用程序既可以是生产者又是可以是消费者。

![](https://img-blog.csdnimg.cn/b3da8e686515403fb27130bbbccd69f0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA44CB5oGw5aW9,size_20,color_FFFFFF,t_70,g_se,x_16#alt=img)

**Broker：**

接收和分发消息的应用，RabbitMQ Server 就是 Message Broker

**Virtual host：**

出于多租户和安全因素设计的，把 AMQP 的基本组件划分到一个虚拟的分组中，类似于网络中的 namespace 概念。当多个不同的用户使用同一个RabbitMQ server 提供的服务时，可以划分出多个 vhost，每个用户在自己的 vhost 创建 exchange／queue 等

**Connection**：

publisher／consumer 和 broker 之间的 TCP 连接

**Channel**：

如果每一次访问 RabbitMQ 都建立一个 Connection，在消息量大的时候建立 TCP Connection 的开销将是巨大的，效率也较低。Channel 是在 connection 内部建立的逻辑连接，如果应用程序支持多线程，通常每个 thread 创建单独的 channel 进行通讯，AMQP method 包含了 channel id 帮助客户端和 message broker 识channel，所以 channel 之间是完全隔离的。Channel 作为轻量级的Connection 极大减少了操作系统建立 TCP connection 的开销

**Exchange**：

message 到达 broker 的第一站，根据分发规则，匹配查询表中的 routing key，分发消息到 queue 中去。常用的类型有：direct (point-to-point), topic (publish-subscribe) and fanout (multicast)

**Queue**：

消息最终被送到这里等待 consumer 取走。

**Binding**：

exchange 和 queue 之间的虚拟连接，binding 中可以包含 routing key，Binding 信息被保存到 exchange 中的查询表中，用于 message 的分发依据。

### 交换机类型

**direct Exchange(直接交换机)**

匹配路由键，只有完全匹配消息才会被转发

**Fanout Excange（扇出交换机）**

将消息发送至所有的队列

**Topic Exchange(主题交换机)**

将路由按模式匹配，此时队列需要绑定要一个模式上。符号“#”匹配一个或多个词，符号“匹配不多不少一个词。因此“abc.#”能够匹配到“abc.def.ghi”，但是“abc.” 只会匹配到“abc.def”。

**Header Exchange**

在绑定Exchange和Queue的时候指定一组键值对，header为键，根据请求消息中携带的header进行路由。

### 工作模式

**simple (简单模式)**

一个消费者消费一个生产者生产的信息。

![](https://img-blog.csdnimg.cn/img_convert/f877af3d3c54959ea2f0ee02b62ab7a4.png#alt=img)

**Work queues(工作模式)**

一个生产者生产信息，多个消费者进行消费，但是一条消息只能消费一次。

![](https://img-blog.csdnimg.cn/img_convert/f877af3d3c54959ea2f0ee02b62ab7a4.png#alt=img)

**Publish/Subscribe（发布订阅模式）**

生产者首先投递消息到交换机，订阅了这个交换机的队列就会收到生产者投递的消息。

![](https://img-blog.csdnimg.cn/img_convert/7cf031325f0f739ab02645145e73e55a.png#alt=img)

**Routing（路由模式）**

生产者生产消息投递到direct交换机中，扇出交换机会根据消息携带的routing Key匹配相应的队列。

![](https://img-blog.csdnimg.cn/img_convert/0936ebee28c7953c028c8022a8e2c60b.png#alt=img)

**Topics（主题模式）**

生产者生产消息投递到topic交换机中，上面是完全匹配路由键，而主题模式是模糊匹配，只要有合适规则的路由就会投递给消费者。

![](https://img-blog.csdnimg.cn/img_convert/11437ab731afe4d7161761300e485526.png#alt=img)

### 消息的稳定性

#### 1.消息持久化

RabbitMQ的消息默认存在内存中的，一旦服务器意外挂掉，消息就会丢失。

消息持久化需做到三点：

- Exchange设置持久化
- Queue设置持久化
- Message持久化发送：发送消息设置发送模式deliveryMode=2，代表持久化消息

#### 2.ACK确认机制

多个消费者同时收取消息，收取消息到一半，突然某个消费者挂掉，要保证此条消息不丢失，就需要acknowledgement机制，就是消费者消费完要通知服务端，服务端才将数据删除，这样就解决了，即使一个消费者出了问题，没有同步消息给服务端，还有其他的消费端去消费，保证了消息不丢的case。

#### 3.设置集群镜像模式

RabbitMQ三种部署模式：

1）单节点模式：最简单的情况，非集群模式，节点挂了，消息就不能用了。业务可能瘫痪，只能等待。

2）普通模式：默认的集群模式，某个节点挂了，该节点上的消息不能用，有影响的业务瘫痪，只能等待节点恢复重启可用（必须持久化消息情况下）。

3）镜像模式：把需要的队列做成镜像队列，存在于多个节点，属于RabbitMQ的HA方案

为什么设置镜像模式集群，因为队列的内容仅仅存在某一个节点上面，不会存在所有节点上面，所有节点仅仅存放消息结构和元数据。

#### 4.消息补偿机制

持久化的消息，保存到硬盘过程中，当前队列节点挂了，存储节点硬盘又坏了，消息丢了，怎么办？消息补偿机制需要建立在消息要写入DB日志，发送日志，接受日志，两者的状态必须记录。然后根据DB日志记录check 消息发送消费是否成功，不成功，进行消息补偿措施，重新发送消息处理。

### 延迟队列

RabbitMQ本身没有延迟队列，需要靠TTL和DLX模拟出延迟的效果。

### TTL（Time To Live）

RabbitMQ可以针对Queue和Message设置 x-message-tt，来控制消息的生存时间，如果超时，则消息变为dead letter。

RabbitMQ针对队列中的消息过期时间有两种方法可以设置。

A: 通过队列属性设置，队列中所有消息都有相同的过期时间。

B: 对消息进行单独设置，每条消息TTL可以不同。

如果同时使用，则消息的过期时间以两者之间TTL较小的那个数值为准。消息在队列的生存时间一旦超过设置的TTL值，就成为dead letter。

### DLX (Dead-Letter-Exchange)

RabbitMQ的Queue可以配置x-dead-letter-exchange 和x-dead-letter-routing-key（可选）两个参数，如果队列内出现了dead letter，则按照这两个参数重新路由。

**x-dead-letter-exchange**：出现dead letter之后将dead letter重新发送到指定exchange

**x-dead-letter-routing-key**：指定routing-key发送

消息被消费端拒绝（basic.reject or basic.nack）并且requeue=false

利用DLX，当消息在一个队列中变成死信后，它能被重新publish到另一个Exchange。这时候消息就可以重新被消费
