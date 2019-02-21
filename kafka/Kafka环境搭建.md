# Kafka环境搭建

[TOC]

### 一、Kafka介绍

[Kafka官网](http://kafka.apache.org/)

### 二、Kafka安装

**版本：** 1.0.0

**安装包：** [kafka_2.12-1.0.0.tgz](https://blog.csdn.net/Russell_Liu/article/details/87463554)

**关于安装包的说明：**

- 2.12代表此安装包是使用2.12版本的scala编译的，并不代表kafka的版本
- 1.0.0代表kafka的版本
- 使用1.0.0版本是因为它是一个里程碑的版本
- 目前公司使用的大多数为0.80版本和0.90版本

**解压缩安装：**

```shell
## 将安装包解压缩
tar -zxf ~/bigData/package/kafka_2.12-1.0.0.tgz -C ~/bigData

## 建立软连接
ln -s ~/bigData/kafka_2.12-1.0.0 /opt/kafka

## 配置环境变量
vim ~/.zshrc
export KAFKA_HOME=/opt/kafka
export PATH=$KAFKA_HOME/bin:$PATH

## 使环境变量生效
source ~/.zshrc

## 修改kafka配置文件server.properties
vim /opt/kafka/config/server.properties
```

**我们需要关注的配置文件如下：**

```properties
## kafka broker的id
broker.id=0

## kafka本地的地址和端口号
listeners=PLAINTEXT://:9092

## kafka log的地址
log.dirs=/opt/kafka/kafka-logs

## kafka需要使用zk
zookeeper.connect=localhost:2181
```

**启动Kafka**

```shell
## 由于已经建立了环境变量，不必到bin目录下执行
## 指定配置文件启动kafka
kafka-server-start.sh -daemon /opt/kafka/config/server.properties

## 查看进程
jps -lm | grep kafka
98272 kafka.Kafka /opt/kafka/config/server.properties
```

注意：

- 启动kafka之前请确保zk已经启动
- 这时可以在zk UI上看到关于kafka节点的信息了

### 三、Kafka常见shell命令

```shell
## 进入到kafka的安装目录(由于已经配置环境变量，因此不进入bin目录也可以)
cd $KAFKA_HOME/bin

## 启动kafka
kafka-server-start.sh -daemon /opt/kafka/config/server.properties

## 创建topic
kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic kafka_streaming_topic

## 发送topic消息（生产者）
kafka-console-producer.sh --broker-list localhost:9092 --topic kafka_streaming_topic

## 消费topic消息（消费者）
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic kafka_streaming_topic

## 查看所有的topic命令
kafka-topics.sh --list --zookeeper localhost:2181

## 增加topic分区数
kafka-topics.sh --zookeeper localhost:2181  --alter --topic kafka_streaming_topic --partitions 10
```

下面大家可以通过shell命令感受下kafka的producer和consumer

1、启动Kafka

2、创建topic

```shell
kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic kafka_streaming_topic
```

3、确定topic创建成功

```shell
# 使用如下命令
kafka-topics.sh --list --zookeeper localhost:2181
# 出现创建的topic即可
kafka_streaming_topic
```

5、开启一个控制台发送消息

```shell
kafka-console-producer.sh --broker-list localhost:9092 --topic kafka_streaming_topic
```

6、开启另外一个控制台接收消息

```shell
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic kafka_streaming_topic
```

在生产者控制台发送消息，在消费者界面会接收到消息：

![](/Users/liumenghao/github/csdn-blog/Java大数据入门/images/Kafka生产者.jpg)

![](/Users/liumenghao/github/csdn-blog/Java大数据入门/images/Kafka消费者.jpg)

### 四、Kafka管理UI部署

同zk类似，虽然安装了kafka服务端，但是我们对当前服务器中有多少个topic，多少个group等等信息并不了解，每次需要查看还需要输入一些shell命令，因此我们需要一个web UI页面来帮助我们了解当前kafka集群的信息

Kafka一共有三款管理工具

- Kafka Web Console

> 监控功能较为全面，可以预览消息，监控Offset、Lag等信息，但存在bug，不建议在生产环境中使用。

- Kafka Manager

> 偏向Kafka集群管理，若操作不当，容易导致集群出现故障。对Kafka实时生产和消费消息是通过JMX实现的。没有记录Offset、Lag等信息

- KafkaOffsetMonitor

> 程序一个jar包的形式运行，部署较为方便。只有监控功能，使用起来也较为安全

这里在公司的生产环境，一般都会部署Kafka Manager和KafkaOffsetMonitor，这里就给大家演示如何在自己的机器上部署

**KafkaOffsetMonitor**

我们使用github上开源的一个项目：https://github.com/quantifind/KafkaOffsetMonitor/releases/tag/v0.2.1

jar包下载：[KafkaOffsetMonitor-assembly-0.2.1.jar](https://blog.csdn.net/Russell_Liu/article/details/87463554)

```shell
## 进入到package目录,下载jar包
cd ~/bigData/package
wget https://github.com/quantifind/KafkaOffsetMonitor/releases/download/v0.2.1/KafkaOffsetMonitor-assembly-0.2.1.jar

## 在bigData下建立文件夹kafkaOffsetMonitor/ 
cd ~/bigData
mkdir kafka-offset-monitor
cp ~/bigData/package/KafkaOffsetMonitor-assembly-0.2.1.jar ~/bigData/kafka-offset-monitor/

## 启动
java -cp KafkaOffsetMonitor-assembly-0.2.1.jar com.quantifind.kafka.offsetapp.OffsetGetterWeb --zk 127.0.0.1:2181 --port 8088  --refresh 5.seconds --retain 1.days

## 浏览器访问
localhost/8088
```

**Kafka Manager**

github上的开源项目，项目地址：https://github.com/yahoo/kafka-manager

下面的步骤会进行源码编译，时间比较久，所以我在百度网盘里有直接提供编译好的压缩包

[kafka-manager-1.3.3.22.zip](https://blog.csdn.net/Russell_Liu/article/details/87463554)

```shell
## 下载源码
https://github.com/yahoo/kafka-manager/releases
## 解压缩，然后进行编译（编译时间很久）
tar -zxf kafka-manager-1.3.3.22.tar.gz
cd ./kafka-manager-1.3.3.22
./sbt clean dist

## 命令执行完成后，命令执行完成后，在 target/universal 目录中会生产一个zip压缩包kafka-manager-1.3.3.22.zip。将压缩包拷贝到要部署的目录下解压。
mkdir ~/bigData/kafka-manager
cp ~/bigData/package/kafka-manager-1.3.3.22/target/universal/kafka-manager-1.3.3.22.zip ~/bigData/kafka-manager
unzip ~/bigData/kafka-manager/kafka-manager-1.3.3.22.zip

## 修改配置文件
vim ~/bigData/kafka-manager/conf
kafka-manager.zkhosts="localhost:2181"

## 启动kafka-manager
cd ~/bigData/kafka-manager/bin
./kafka-manager

## 访问console
localhost:9000
```

启动之后如下图所示：

![](/Users/liumenghao/github/csdn-blog/Java大数据入门/images/kafka-manager截图.jpg)

这时候还没有创建集群，点击Add Cluster即可创建集群

### 五、总结

目前我们已经在机器上完成了kafka的安装（单机版）。但是我们目前与kafka的交互仅限于使用shell命令。我们搭建kafka的目的是学习使用它，而后面我们就使用java api来编写生产者和消费者的demo帮助大家更好的理解kafka。

**参考文章：**

1、[kafka开源管理工具Kafka-manager部署](http://baijiahao.baidu.com/s?id=1598139489983645370&wfr=spider&for=pc)

