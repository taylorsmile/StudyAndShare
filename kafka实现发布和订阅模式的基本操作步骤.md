### kafka实现发布/订阅模式的基本操作步骤

```
1、启动zookeeper (添加-daemon 是为了控制台少一些日志的输出，将其放在后台)
zookeeper-server-start -daemon /usr/local/etc/kafka/zookeeper.properties &

2、启动kafka (添加-daemon 是为了控制台少一些日志的输出，将其放在后台)
kafka-server-start -daemon /usr/local/etc/kafka/server.properties &

3、创建topic (创建HelloWorld的主题，分区1个，备份1个)
kafka-topics --zookeeper localhost:2181 --create --topic HelloWorld --partitions 1 --replication-factor 1

4、查看某topic的分区和备份的相关详细信息 --describe
kafka-topics --zookeeper localhost:2181 --describe --topic HelloWorld

5、创建producer (9092是kafka集群默认的端口)  往kafka集群中指定的HelloWorld主题发布消息
kafka-console-producer --broker-list localhost:9092 --topic HelloWorld 

6、创建consumer 指定订阅某Topic的消息 
kafka-console-consumer --bootstrap-server localhost:9092 --topic HelloWorld

7、查看topic中的所有内容 (添加--from-beginning会将指定的主题中以往所有的数据信息都读区出来。此属性可根据业务场景决定是否添加)
kafka-console-consumer --bootstrap-server localhost:9092 --topic HelloWorld --from-beginning

8、删除自创建的topic
kafka-topics --zookeeper localhost:2181 --delete --topic HelloWorld 

9、停止kafka
kafka-server-stop

10、停止zookeeper
zookeeper-server-stop 

11、查看当前kafka和zookeeper的进程是否已停止，jps(Java Virtual Machine Process Status Tool)命令
jps命令是查看当前本机上所有java进程的pid
``` 

### 补充

```
1、查看目前kafka集群中有哪些topic
kafka-topics --zookeeper localhost:2181 --list 

2、若是已指定分区数来创建topic的情况下，此时想要修改分区数来提高消费能力 --alter
kafka-topics --zookeeper localhost:2181 --topic HelloWorld --alter --partitions 2 

3、消费者组的概念：
早先的消费者组的消息处理方式是"队列模式"。所谓的队列模式便是：生产者生产消息就是入队，消费者消费消息便是出队，消息出队的同时也会删除此条消息，即消息只能被消费一次。队列模式的缺陷：只能由一位消费者消费，并不支持多个消费者同时消费。

基于队列模式的缺陷，后期出现了"发布/订阅模式"，所谓的发布/订阅模式便是：新增一个topic的概念，首先以消费者组的名义去kafka集群订阅topic，生产者往这个topic发消息，topic下面会有很多的partitions(分区)，消息会随机发布到这些分区中，然后消费者组中的每位消费者都一起去订阅分区的消息(每一个分区只能被消费者组中的某一位消费者订阅，不可能存在两位消费者同时消费同一个partition)

若消费者组中的消费者数量大于分区数量，则会存在有些消费者不消费的情况，一直是空闲的状态；
若消费者组中的消费者数量小于分区数量，则会存在某位消费者会消费多个分区的数据，这会增加消费者的订阅数据的压力
综合以上的情况，建议设置消费者中的消费者数量等于topic下的分区数

4、当消费者消费的时候，会在kafka集群中发现除了新创建的topic之外，还会出现一个 __consumer-offsets的topic主题，此主题是kafka内部的topic，是用于记录消费者组的注册信息以及读区消息时的提交位移状态值

5、当某kafka的服务配置到zookeeper中后，查看其相关的配置信息
zookeeper-shell localhost:2181
```
