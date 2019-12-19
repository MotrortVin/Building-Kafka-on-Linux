# Building-Kafka-on-Linux     
>作者：[Motrort Vin](github.com/MotrortVin)，转载请标明出处。
-----
## 第一章 Linux系统(centOS7)上Kafka的搭建和使用教程（超全）    
（普通用户状态下：“$”代表普通用户，“#”代表root用户）     
参考教程1：[centos7安装kafka](https://blog.csdn.net/qq_28666081/article/details/92020989)   
参考教程2：[CentOS7安装和使用kafka](https://blog.csdn.net/zzq900503/article/details/83348419)    
环境：jdk1.8    kafka2.11    centos7     
- [一、安装JDK](###一安装JDK)
- [二、安装Kafka](###二安装Kafka)
- [三、安装ZooKeeper服务](#----zookeeper--)
  * [1.方案一 使用kafka自带的zookeeper](#1------kafka---zookeeper)
  * [2.方案二 独立安装zookeeper服务](#2--------zookeeper--)
  * [3.方案三 使用hadoop集群cdh等套件中的zookeeper（目前探索中，未完待续）](#3------hadoop--cdh-----zookeeper------------)
- [四、kafka操作](#--kafka--)
  * [1.方案一：使用kafka自带的zookeeper](#1------kafka---zookeeper)
  * [2.方案二：独立安装zookeeper服务](#2--------zookeeper--)
  * [3.方案三、目前正在探索中](#3-----------)
    + [附：前台执行和后台执行方法对比](#---------------)    
-----
### 一、安装JDK   
参考教程：[Centos7中yum安装jdk及配置环境变量](https://www.cnblogs.com/52lxl-top/p/9877202.html)   
#### 1.查看系统版本 cat /etc/redhat-release    
![graph1](/graphs/1.png) 
#### 2.安装之前先查看一下有无系统自带jdk   
```shell     
# rpm -qa |grep java
# rpm -qa |grep jdk
# rpm -qa |grep gcj
```   
![graph2](/graphs/2.png)   
![graph3](/graphs/3.png) 
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
![graph4](/graphs/4.png)     
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
![graph5](/graphs/5.png)    
需要在你的代码前加sudo，临时取得授权。     
- ②代码中jdk的版本和路径因人而异，具体情况因自己所装java的版本和地址而异。     
#### 7.查看变量     
```shell
$ echo $JAVA_HOME
```     
![graph6](/graphs/6.png)     
```shell
$ echo $CLASSPATH
```     
（如果设置了对所有用户生效，即可出现下图的报文）     
![graph7](/graphs/7.png)     
### 二、安装Kafka    
安装地址： [Kafka镜像文件地址](http://mirror.bit.edu.cn/apache/kafka)    
选择自己希望安装的kafka版本。这里以kafka2.1.1-2.2.1为例。     
#### 1.利用wget下载Kafka压缩包   
```shell
$ wget http://mirror.bit.edu.cn/apache/kafka/2.2.1/kafka_2.11-2.2.1.tgz
```   
![graph8](/graphs/8.png)        
**注意：**
如果报错：wget: command not found。可通过如下命令安装wget：   
```shell
# yum -y install wget
```    
#### 2.解压   
```shell
$ tar zxvf kafka_2.11-2.2.1.tgz
```
![graph9](/graphs/9.png)      
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
![graph10](/graphs/10.png)     
##### （2）解压安装并简化命名（与Kafka安装流程类似）      
```shell
tar -zxvf apache-zookeeper-3.5.6-bin.tar.gz
mv apache-zookeeper-3.5.6-bin zookeeper
cd zookeeper
```     
![graph11](/graphs/11.png)     
进入了ZooKeeper目录，最后创建data目录，为稍后配置文件做准备：   
##### （3）创建配置文件   
可以直接修改，也可以复制粘贴sample文件，然后做修改。   
**a.直接修改**   
使用命令vi conf/zoo.cfg 打开名为 conf/zoo.cfg 的配置文件，并将所有以下参数设置为起点。
```(shell)
vi conf/zoo.cfg
```   
进入编辑模式后，输入下面几行：   
```(shell)
tickTime=2000
dataDir=/…(zookeeper的路径)/zookeeper/data
clientPort=2181
initLimit=5
syncLimit=2
```   
编辑结束后，按Esc键，输入 :wq再按回车即可保存退出。    
**b.复制修改**   
```(shell)
cp zoo_sample.cfg zoo.cfg
vim zoo.cfg
```   
进入编辑模式后，修改dataDir这一行，改成当前地址:    
```(shell)
dataDir=/…(zookeeper的路径)/zookeeper/data
```   
(如果在解压安装过程中完全按照本文教程，则修改如下图)   
![graph12](/graphs/12.png)     
#### 3.方案三 使用hadoop集群cdh等套件中的zookeeper（目前探索中，未完待续） 
如果已经有hadoop集群，一般都已经有zookeeper集群服务了。    
### 四、kafka操作     
#### 1.方案一：使用kafka自带的zookeeper   
参考链接：[kafka单机环境搭建及其基本使用](https://www.cnblogs.com/qpf1/p/9161742.html)    
##### (1)进入Kafka目录   
如若当前不在kafka目录中的话，可以先进入kafka的目录。    
```(shell)
cd ~/kafka
```   
##### (2)首先启动kafka自带的zookeeper
```(shell)
# bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
```   
或者
```(shell)
$ sudo bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
```   
**注意：**
这里`-daemon`是后台执行的意思，为了简化操作，建议后台执行zookeeper。如果去掉的话则会前台执行，此时则需要再开启一个服务器终端，执行下一步操作。   
##### (3)接下来启动kafka   
在kafka目录下执行下面的命令：   
```(shell)
bin/kafka-server-start.sh –daemon config/server.properties
```    
上述命令可以后台执行kafka，为了简化操作，建议后台执行kafka。如果需要前台执行的话，也可以去掉命令中-daemon，前台执行，这样会出现执行kafka的报文。   
如果使用前台执行出现报文如下，则说明启动成功。   
```(shell)
[2018-05-21 12:21:07,901] INFO Registered kafka:type=kafka.Log4jController MBean (kafka.utils.Log4jControllerRegistration$)
[2018-05-21 12:21:09,417] INFO starting (kafka.server.KafkaServer)
[2018-05-21 12:21:09,419] INFO Connecting to zookeeper on localhost:2181 (kafka.server.KafkaServer)
...
[2018-05-21 12:21:15,955] INFO [KafkaServer id=0] started (kafka.server.KafkaServer)
```   
这时如果前台执行界面可能不再有反应，这是正常情况，因为我们执行的是kafka的前台启动命令。所以如果需要进行下一步操作，需要再打开一个新的终端。如下：   
![graph13](/graphs/13.png)    
##### (4)使用Kafka
如若当前不在kafka目录中的话，可以先进入kafka的目录。   
```(shell)
cd ~/kafka     
```   
进入kafka目录后执行下面操作:   
创建topic:   
```(shell)
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic talk
```   
输入并执行，即可创建一个名称为talk的topic(使用`kafka-topics.sh`创建单分区单副本的topic test):   
![graph14](/graphs/14.png)     
生产信息(使用`kafka-console-producer.sh`发送消息)：要注意端口设置！   
```(shell)
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic talk
```    
![graph15](/graphs/15.png)    
输入信息内容，比如：   
```(shell)
Hello World!
Hello Kafka!
```   
按`ctrl`+`C`，退出编辑。   
消费信息(使用`kafka-console-consumer.sh`接收消息并在终端打印)：   
旧版本命令：要注意端口设置！   
```(shell)
bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic talk --from-beginning
```   
新版本命令：要注意端口设置！   
```(shell)
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic talk --from-beginning
```   
![graph16](/graphs/16.png)    
##### (5)结束之后，停止kafka服务器    
```(shell)
$ sudo bin/kafka-server-stop.sh config/server.properties
```   
![graph17](/graphs/17.png)     
如果你的kafka采用的是前台执行，打开之前用于开启kafka的那个终端，可以查看你之前所有操作的具体log，如下图：   
![graph18](/graphs/18.png)   
##### (6)停止zookeeper
如若当前不在kafka目录中的话，可以先进入kafka的目录。   
```(shell)
cd ~/kafka     
```   
在新开的终端里键入cd+kafka的路径后，进入kafka目录后停止kafka自带的zookeeper服务。    
```(shell)
# bin/zookeeper-server-stop.sh -daemon config/zookeeper.properties
```   
或者   
```(shell)
$ sudo bin/zookeeper-server-stop.sh -daemon config/zookeeper.properties
```     
#### 2.方案二：独立安装zookeeper服务    
##### （1）启动ZooKeeper服务器   
如若当前不在zookeeper目录中的话，可以先进入zookeeper的目录。   
```(shell)
cd ~/zookeeper
bin/zkServer.sh start
```   
如果报错了：   
```(shell)
Starting zookeeper ... bin/zkServer.sh: line 169: /path/to/zookeeper/data/zookeeper_server.pid: Permission denied
FAILED TO WRITE PID
```    
在代码前面加sudo获取授权就好了。如果响应如下图所示，说明启动成功。    
![graph19](/graphs/19.png)    
启动CLI进入操作系统界面   
```(shell)
bin/zkCli.sh  
```   
接下来将被连接到zookeeper服务器，此时会有如下响应：   
![graph20](/graphs/20.png)     
(此处的大段字符段落，笔者使用省略号代替。)   
这时界面可能不再有反应，这是正常情况，因为我们执行的是kafka的前台启动命令。所以如果需要进行下一步操作，需要再打开一个新的终端。如下：    
![graph21](/graphs/21.png)  
**注意：**
如果没有出现最后一行`[zk: localhost:2181(CONNECTED) 0]`，则未进入操作系统界面，这里可以检查一下自己在三、2.(3)创建配置文件 中dataDir的路径是否有误。   
如果报错，可以键入命令：   
```(shell)
sudo bin/zkServer.sh status
```   
查看zookeeper是否真正启动，如果出现Error contacting service. It is probably not running.则说明zookeeper未能真正启动。（未能真正启动的原因可以自行百度）   
##### (2)在新的终端里，按照方案一中的（3）（4）（5）执行
##### (3)停止ZooKeeper服务器   
如果目前处于[zk: localhost:2181(CONNECTED) 0]阶段，且执行完所有操作后，可以按`ctrl`+`C`退出操作系统界面。然后输入下面的命令退出。     
```(shell)
bin/zkServer.sh stop
```    
（没有权限的话记得加sudo）    
![graph22](/graphs/22.png)    
#### 3.方案三、目前正在探索中    
### 附：前台执行和后台执行方法对比     
前台执行由于是在前台打开kafka，优点是可以随时在打开kafka的终端中查看当前操作的日志，但不方便，需要在多个服务器终端中执行命令，如果打开kafka的终端与服务器断开连接，则kafka也被停止。所以推荐使用后台执行命令。   
参考教程：[kafka单机环境搭建及其基本使用](https://www.cnblogs.com/qpf1/p/9161742.html)    

