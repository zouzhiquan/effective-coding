---
author: zouzq7@163.com
created: "2018-10-26 02:13:44 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Kafka-producer解析
updated: "2023-08-24 14:52:06 +0000"
---

<div>

Kafka
作为一个消息系统，其中很大的一个用途就是作为业务上的解耦，而它实现的模式就是经典的生产者消费者模式。毫无疑问，就出现了producer、consumer。然后消息总得有地方存放啊，然后就有了具体的broker，那在broker上是如何进行组织和存放的，就出现了partition。对应的为保证消息不丢失，也就出现了消息备份组这样一个概念（ISR，in-sync
replica）再加上消息的topic也就形成了，kafka的 topic-partition-message
的三级负载结构。到这里Kafka中比较核心的几个概念就都有了，下面开始详细介绍。

</div>

<div>

\

</div>

<div>

producer

</div>

<div>

producer也就是生产者，是kafka中消息的产生方，产生消息并提交给kafka集群完成消息的持久化，这个过程中主要涉及ProducerRecord对象的构建、分区选择、元数据的填充、ProducerRecord对象的序列化、进入消息缓冲池、完成消息的发送、接受broker的响应。

</div>

<div>

具体的流程是这样的：

</div>

<div>

![](Kafka-producer%E8%A7%A3%E6%9E%90.resources/C0685A4F-D9B3-49CF-BB2F-F8D744FE905A.png) 
 \

</div>

<div>

1、确定topic信息

</div>

<div>

2、确定value信息

</div>

<div>

3、然后进行消息的序列化处理

</div>

<div>

4、由分区选择器确定对应的分区信息

</div>

<div>

5、将消息写入消息缓冲区

</div>

<div>

6、完成消息请求的发送

</div>

<div>

7、完成消息响应的处理

</div>

<div>

\

</div>

<div>

ProducerRecord：

</div>

<div>

ProducerRecord
对象比较核心的信息有：topic、partition（这个信息是根据分区选择器来确定的）、key、value、timestamp

</div>

<div>

PS：时间戳信息是默认当前时间的，但是用户可以指定时间戳信息，但是不推荐这么做，broker中大体有这么几种log也就是消息存放文件普通日志文件，时间索引文件，普通索引文件。如果强行指定时间戳很有可能导致时间索引失效。

</div>

<div>

\

</div>

<div>

元数据：

</div>

<div>

元数据信息主要包括offset消息在分区日志中的位移信息、timestamp、topic/partition
topic及对应的分区信息、checksum 消息对应的CRC32码、serializedKeySize
序列化后的key的字节数、serializedValueSize 序列化后的Value的字节数

</div>

<div>

\

</div>

<div>

Partition：

</div>

<div>

分区选择器，默认是murmur2
对于key进行hash计算然后对于总分区数求模以此得到被发送的分区号，当然我们实现producer时可以自定义partition，或者指定特定分区。

</div>

<div>

\

</div>

<div>

serializer：

</div>

<div>

serializer是kafka实现的自己的序列化工具用于将消息对象序列化成字节序列，Kafka中提供了ByteArraySerializer、ByteBufferSerializer、BytesSerializer、Long（Double
Integer
String）Serializer等几种序列化方法，用户也可以使用自定义的或者第三方的序列化工具。只需要使用指定对应参数即可（切记Kafka中指定对应的工具类时都是使用权限定名称来做的）

</div>

<div>

序列化相关的参数有如下：

</div>

<div>

key.serializer 针对各个部分做序列化方式

</div>

<div>

key.deserializer key.serializer对应解序列化方式

</div>

<div>

value.serializer 对value部分指定的序列化方式

</div>

<div>

value.deserializer value.serializer对应解序列化方式

</div>

<div>

可以简单的理解为key要比value的应用范围广。

</div>

<div>

\

</div>

<div>

batch：

</div>

<div>

buffer.memory
指定producer待发送消息缓冲区的内存大小，默认32m，如果需要更改就使用这个参数进行修改。这里需要注意的是当producer端写消息的速度超过了专属IO线程发送消息的速度，并且缓冲区的消息数量超过buffer.memory指定的大小时，producer会抛出异常通知用户介入处理，这个缓冲区的大小需要根据实际场景来确定。

</div>

<div>

batch.size
 指一个batch的大小，它直接决定了一个batch中存在的消息数量，这个直接与producer的吞吐量及延时等直接相关，因为所谓的micr-batch
是指原本应该串行一条条发送的消息更改为缓存一部分消息，等达到对应的消息规模时一次性发送，也不会像批处理规模那么大（主要为了平衡延时与性能，这个会有专门的篇章来介绍micr-batch）

</div>

<div>

linger.size 

</div>

<div>

producer端会专门划出一部分内存用于待发送消息的缓存，batch.size决定了发送消息数量，同时间接决定了消息缓存时存在的延时。linger.size
就是针对这一点设计出来的，它决定了消息被投放进缓冲区时是否立马被发送，默认参数是0（立即发送），这个大多数情况下是合理的，但是会很大程度上拉低kafka的吞吐量。具体要根据实际的使用场景来确定了。

</div>

<div>

\

</div>

<div>

通信协议：

</div>

<div>

kafka 并没有使用现有的http协议等，而是在TCP
协议之上实现了自己的通信协议。单个client会创建多个socket链接与多个broker进行交互,Kafka
原生Java
client使用类似于epoll的方式在单个连接上不停的轮训传数据，但是每个broker上只需要维护一个Scoket链接,保证了消息的请求的顺序处理，所以很清晰的可以看到在client端就需要我们自己去维护这个顺序了。

</div>

<div>

整体来说Kafka 中大约有三类连接：client与broker之间消息传输、controller
与所有broker之间的交互、client 获取元数据&rebalance的通信过程。

</div>

<div>

同其他协议类似，Kafka的通信协议的请求和响应也都是格式化的。由
固定长度初始类型（int8、int16、int32、int64）、可变长度类型（bytes、string）、数组。请求头由
api_key（int16，请求类型）、api_version（int16，请求版本号）、correlation_id（int32，与请求响应的关联号，这个字段就是给响应用的）、client_id（client
id）

</div>

<div>

经常接触到的Kafka请求类型有：PRODUCE请求（生产消息请求）、FETCH请求（服务于消费消息，并不一定是clients向broker拉消息，也可能是follower副本向leader副本索要消息）、METADATA请求（获取指定topic的元数据信息：\[topics\]+allow_auto_topic_creation）

</div>

<div>

PS：这里有一点需要说明，clients与broker是单向兼容的，这个在生产环境中如果不注意是格外容易发生问题的。这个兼容性是指，高版本broker可以兼容低版本clients，但是低版本broker无法兼容高版本clients，所以说升级clients版本，尤其是对接新的consumer时一定要格外注意。这个问题主要针对非Java
client的，对于Java
client来说，会自动判断连接的broker端所支持的client请求的最高版本。

</div>

<div>

 检查版本参数

</div>

<div>

\

</div>

<div>

producer interceptor

</div>

<div>

拦截器是新版本才出现的一个特性，并且是非必须的，interceptor
核心的函数有onSend（在消息序列化计算分区之前就被调用）、onAcknowleagement（被应答前或者说在发送失败时，这个方法是运行在producer的I/O线程中的，所以说如果存在很多重逻辑的话会导致严重影响处理消息的速率）、close。通常是通过为clients定制一部分通用且简单的逻辑时才会使用的。

</div>

<div>

\

</div>

<div>

压缩算法

</div>

<div>

Kafka支持的压缩算法还是很可观的：GZIP、Snappy、LZ4，默认情况下不进行消息压缩，毕竟会消耗很大一部分cpu时间，导致send方法处理时间变慢。启动LZ4
进行消息压缩的producer的吞吐量是最高的。

</div>

<div>

\

</div>

<div>

\

</div>

<div>

\

</div>
