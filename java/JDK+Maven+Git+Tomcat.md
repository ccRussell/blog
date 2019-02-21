# Jdk+Maven+Git 环境搭建

[TOC]

### 一、Jdk环境搭建

Jdk运行环境是必不可少的，是搭建所有环境的基础。

**版本**：1.8

**安装包**：jdk-8u181-linux-x64.tar.gz

**解压缩安装**：

```shell
## 将文件解压到对应的文件夹
tar -zxf ~/bigData/package/jdk-8u181-linux-x64.tar.gz -C ~/bigData
```

**设置环境变量**：

```shell
## 验证机器是否已经安装jdk
java -version
-bash: java: command not found

## 在/opt文件夹中建立软链接
ln -s ln -s ~/bigData/jdk1.8.0_181 /opt
mv jdk1.8.0_181 jdk

## 设置环境变量
vim ~/.zshrc

## 在文件中输入如下内容
#jdk
export JAVA_HOME=/opt/jdk/
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH

##使得设置生效
source ~/.zshrc
## 验证
java -version
java version "1.8.0_181"
Java(TM) SE Runtime Environment (build 1.8.0_181-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.181-b13, mixed mode)
```

Jdk1.8环境搭建完成

### 二、Maven环境搭建

后面我们编写的demo基本都是Maven项目，因此Maven环境必不可少

**版本**：3.5.4

**安装包**：apache-maven-3.5.4-bin.tar.gz

**解压缩安装**：

```shell
## 将文件解压到对应的文件夹
tar -zxf ~/bigData/package/apache-maven-3.5.4-bin.tar.gz -C ~/bigData
```

**设置环境变量：**

```shell
## 验证机器是否已经安装maven
mvn -version

## 设置对所有用户长久有效的环境变量
vim /etc/profile

## 在文件中输入如下内容
export MAVEN_HOME=/root/demos/software/apache-maven-3.5.4
export PATH=$MAVEN_HOME/bin:$PATH

##使得设置生效
source /etc/profile

## 验证
mvn -version
```

Maven环境搭建完成

### 三、Git环境搭建

git是一款免费，开源的分布式版本控制系统，后面我们的项目都是使用git进行管理。并且项目源码都是托管在github上。

**版本：**2.8.0

**安装包：**v2.8.0.tar.gz

**源码编译安装：**

```shell
## 下载源码
wget https://github.com/git/git/archive/v2.8.0.tar.gz

## 使用yum安装依赖（必须首先安装依赖，否则无法编译源码）
sudo yum -y install zlib-devel openssl-devel cpio expat-devel gettext-devel curl-devel perl-ExtUtils-CBuilder perl-ExtUtils- MakeMaker 

## 解压缩源码
tar -zxf v2.8.0.tar.gz

## 编译源码（prefix代表安装的目录位置）
cd /root/demos/package/v2.8.0.tar.gz
sudo make prefix=/root/demos/software/git-2.8.0 all

## 安装（prefix代表安装的目录位置）
sudo make prefix=/root/demos/software/git-2.8.0 install
```

**设置环境变量：**

```shell
## 设置对所有用户长久有效的环境变量
vim /etc/profile

## 在文件中输入如下内容
export GIT_HOME=/root/demos/software/git-2.8.0
export PATH=$GIT_HOME/bin:$PATH

##使得设置生效
source /etc/profile

## 验证
git --version
```

**配置git(连接github)：**

```shell
## 配置用户名、email
git config --global user.name "liumenghao"
git config --global user.email "727769385@qq.com"

## 配置编码
git config --global gui.encoding utf-8

## 忽略win或者linux的换行符
git config --global core.autocrlf false

## 生成rsa认证公钥和私钥（一直回车）
ssh-keygen -t rsa -C "727769385@qq.com"

## 进入到目录,打开公钥,将内容复制出来，然后粘贴到github
cd ~/.ssh
vim id_rsa.pub
```

### 四、Tomcat安装

Tomcat是免费的开源web服务器。后面我们会搭建springboot demo，使用的是内置的tomcat。这里我们搭建tomcat主要是为后面部署一些UI界面做准备，例如Jstorm的UI就需要Tomcat环境

**版本：**8.5.37

**安装包：**apache-tomcat-8.5.37.tar.gz

**安装tomcat**：

```shell
## 解压缩到指定目录
tar -zxf apache-tomcat-8.5.37.tar.gz -C /root/demos/software/

## 设置对所有用户长久有效的环境变量
vim /etc/profile

## 输入如下内容
export TOMCAT_HOME=/root/demos/software/apache-tomcat-8.5.37
export PATH=$TOMCAT_HOME/bin:$PATH

## 使之生效
source /etc/profile

## ps：对tomcat设置环境变量其实没有太多意义
```

**启动tomcat：**

```shell
## 进入tomcat目录
cd $TOMCAT_HOME/bin

## 启动
sh startup.sh

## 查看进程
jps -lm
15239 org.apache.catalina.startup.Bootstrap start
```

**访问tomcat：**

在自己的主机上输入：$阿里云公网ip:8080

### 五、总结

- 不要对Linux命令发怵，搞清楚每一个命令的含义，这样就算在其他系统中安装也是一样的道理，只不过命令可能有所不同而已。
- 建议大家不要使用yum安装这些功能，因为自己手动安装可以指定安装位置。这样对自己安装的软件做到心中有数，在启动或者修改配置的时候也更方便
- 对于上文中出现的一些linux命令，大家如果不熟悉，我的建议是不要照搬使用。而是先去百度了解其使用场景和基本语法，例如：scp、jps等





