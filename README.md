# Building-Kafka-on-Linux     
>作者：[Motrort Vin](github.com/MotrortVin)，转载请标明出处。
-----
## 第一章 Linux系统(centOS7)上Kafka的搭建和使用教程（超全）    
（普通用户状态下：“$”代表普通用户，“#”代表root用户）     
参考教程1：[centos7安装kafka](https://blog.csdn.net/qq_28666081/article/details/92020989)   
参考教程2：[CentOS7安装和使用kafka](https://blog.csdn.net/zzq900503/article/details/83348419)    
环境：jdk1.8    kafka2.11    centos7    
### 一、安装JDK   
参考教程：[Centos7中yum安装jdk及配置环境变量](https://www.cnblogs.com/52lxl-top/p/9877202.html)   
#### 1.查看系统版本 cat /etc/redhat-release    
图1   
#### 2.安装之前先查看一下有无系统自带jdk   
```shell     
# rpm -qa |grep java
# rpm -qa |grep jdk
# rpm -qa |grep gcj
```   
图2   
图3   
#### 3.如果有就使用批量卸载命令   
```shell
# rpm -qa | grep java | xargs rpm -e --nodeps 
```   
#### 4.直接yum安装1.8.0版本openjdk   
```shell
# yum install java-1.8.0-openjdk* -y
```    
#### 5.检查版本    
```shell
$ java -version
```    
图4    
#### 6.生效配置（三选一，按照自己当前需求选择）     
- 临时生效     
```shell
$ export JAVA_HOME=/usr/lib/jvm/<span style="font-family: Arial;">jre-1.8.0-openjdk-1.8.0.121-0.b13.el7_3.x86_64</span> 
```     
- 对当前用户生效   
```shell
$ vim ~/.bashrc
```     
在文件底部加入下面一句     
```shell
export  JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64
```     
- 使所有用户生效     
```shell
$ vim /etc/profile    
 #set java environment  
export JAVA_HOME=/usr/lib/jvm/java
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/jre/lib/rt.jar
export PATH=$PATH:$JAVA_HOME/bin
```     
**注意：**     
- ①如果遇到下面的提示：     
图5    
需要在你的代码前加sudo，临时取得授权。     
- ②代码中jdk的版本和路径因人而异，具体情况因自己所装java的版本和地址而异。     
#### 7.查看变量     
```shell
$ echo $JAVA_HOME
```     
图6     
```shell
$ echo $CLASSPATH
```     
（如果设置了对所有用户生效，即可出现下图的报文）     
图7     
### 二、安装Kafka    
安装地址： [Kafka镜像文件地址](http://mirror.bit.edu.cn/apache/kafka)    
选择自己希望安装的kafka版本。这里以kafka2.1.1-2.2.1为例。     
#### 1.利用wget下载Kafka压缩包   
```shell
$ wget http://mirror.bit.edu.cn/apache/kafka/2.2.1/kafka_2.11-2.2.1.tgz
```   
图7    
**注意：**
如果报错：wget: command not found。可通过如下命令安装wget：   
```shell
# yum -y install wget
```    
#### 2.解压   
```shell
$ tar zxvf kafka_2.11-2.2.1.tgz
```
图8   
#### 3.命名化简（为了简化以后的shell命令和脚本）     
```shell
$ mv kafka_2.11-2.2.1 kafka
```    
**注意：**
命名简化后，就可以通过   
```shell
$ cd kafka
```
进入Kafka目录。如果和本文路径安装不一致的话，应当参考自己的安装路径。   
### 三、安装ZooKeeper服务   
这里有三个方案可以选择：
- 方案一 使用kafka自带的zookeeper   
- 方案二 独立安装zookeeper服务   
- 方案三 使用hadoop集群cdh等套件中的zookeeper（目前探索中，未完待续）   
#### 1.方案一 使用kafka自带的zookeeper    
- 特点：适用于测试等小型场景或者单点kafka。     
关于使用kafka安装包中的脚本启动单节点Zookeeper的实例。直接调到
__四、Kafka操作 中的方案一__
即可。    
#### 2.方案二 独立安装zookeeper服务     
- 特点：最灵活的方式，适用于单点和多节点kafka。   
参考教程：[zookeeper-3.5.6最新版安装攻略，以及安装问题汇总](https://www.cnblogs.com/itworkers/p/11697513.html)    
ZooKeeper下载网址：[ZooKeeper镜像文件下载网址](http://mirror.bit.edu.cn/apache/zookeeper/)     
选择自己需要安装的ZooKeeper版本。这里以目前最新版本的zookeeper-3.5.6为例。      
##### （1）下载     
**注意：**
目前的最新版本3.5.5开始，带有bin名称的包才是我们想要的下载可以直接使用的里面有编译后的二进制的包，而之前的普通的tar.gz的包里面是只是源码的包无法直接使用。所以要安装带bin的包！（这个源码包和编译后的包的命名方式将来可能会改，大家下载之前一定要去网页上确认一下自己下载的版本的已编译的包的名字）    
```shell
wget http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.5.6/apache-zookeeper-3.5.6-bin.tar.gz
```    
图9    
##### （2）解压安装并简化命名（与Kafka安装流程类似）      
```shell
tar -zxvf apache-zookeeper-3.5.6-bin.tar.gz
mv apache-zookeeper-3.5.6-bin zookeeper
cd zookeeper
```     
图10   
进入了ZooKeeper目录，最后创建data目录，为稍后配置文件做准备：   
##### （3）创建配置文件   
可以直接修改，也可以复制粘贴sample文件，然后做修改。    

