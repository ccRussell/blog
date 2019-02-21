# Zookeeper环境搭建

[TOC]

### 一、ZK介绍

[zookeeper官网](https://zookeeper.apache.org/)

### 二、ZK安装（单机）

**版本：**3.4.5

**安装包：**zookeeper-3.4.5-cdh5.7.0.tar.gz

**解压缩：**

```shell
## 将zk解压到指定目录
tar -zxf ~/bigData/zookeeper-3.4.5-cdh5.7.0 -C ~/bigData/

## 在/opt文件夹建立软链接
ln -s ~/bigData/zookeeper-3.4.5-cdh5.7.0 /opt/zookeeper

## 设置环境变量
vim ~/.zshrc
#zookeeper
export ZK_HOME=/opt/zookeeper
export PATH=$ZK_HOME/bin:$PATH

## 使环境变量生效
source ~/.zshrc
```

###三、ZK配置

zk安装完成后，需要进行一些配置

```shell
## 进入配置文件目录
cd $ZK_HOME/conf

## 复制一份配置文件
cp zoo_sample.cfg zoo.cfg

## 修改配置文件
vim zoo.cfg
```

**配置文件内容：**

```properties
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5
# 这个位置我做了改动，还是相同的理念，我希望所有的文件位置在我的掌控之下
dataDir=/Users/liumenghao/bigData/temp/zookeeper
# 端口号
clientPort=2181
```

**启动ZK：**

```shell
## 由于我们已经添加了环境变量，因此可以在任何路径执行
zkServer.sh start

Using config: /opt/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED

## 查看进程
jps -lm
94211 org.apache.zookeeper.server.quorum.QuorumPeerMain /opt/zookeeper/bin/../conf/zoo.cfg

## 停止ZK
zkServer.sh stop
```

可以看到zk启动的时候默认使用conf目录下的zoo.cfg配置文件，因此我们才会新建一份zoo.cfg文件

### 四、WebUI安装

github上托管了一个zkui的开源项目，是ZK的UI界面，通过界面我们可以很方便的进行一些常规操作

https://github.com/DeemOpen/zkui

**部署UI：**

```shell
## 从github上克隆下载源码
cd ~/bigData/
git clone git@github.com:DeemOpen/zkui.git

## 使用maven进行打包（第一次执行会比较慢，因为要下载jar包到本地）
cd ~/bigData/zkui
mvn clean install

## 修改config.cfg文件(由于是本地使用，不用做任何修改)
vim config.cfg

## 将config.cfg copy到打包出来的target目录
cp config.cfg ./target

## 启动jar包
cd ~/bigData/zkui/target
nohup java -jar zkui-2.0-SNAPSHOT-jar-with-dependencies.jar &

## 建立软链接
ln -s ~/bigData/zkui /opt/zkui
```

**访问UI：**

**端口号：**9090

**账号密码：**admin. manager

**我的访问路径**：localhost/9090

现在在页面上看不到节点，那是因为现在我们没有在zk上注册服务，后面我们搭建kafka，hbase等应用的时候，就可以看到zk的节点了

### 五、总结

ZK是分布式系统的基础组件，由于我们只是学习使用，因此并没有搭建zk集群（主要是没有更多的机器）