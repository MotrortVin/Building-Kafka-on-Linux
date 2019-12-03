# Building-Kafka-on-Linux
### Linux系统上Kafka的搭建及producer和consumer的java实现。（不断更新）
-----
## 第一章节 Linux系统(centOS7)上Kafka的搭建和使用教程（超全）    
（普通用户状态下：“$”代表普通用户，“#”代表root用户）     
### 一、在Linux系统上搭建Kafka   
参考教程1：[centos7安装kafka](https://blog.csdn.net/qq_28666081/article/details/92020989)   
参考教程2：[CentOS7安装和使用kafka](https://blog.csdn.net/zzq900503/article/details/83348419)    
环境：jdk1.8    kafka2.11    centos7    
#### 1.安装JDK   
参考教程：[Centos7中yum安装jdk及配置环境变量](https://www.cnblogs.com/52lxl-top/p/9877202.html)   
##### （1）查看系统版本 cat /etc/redhat-release    
图1   
##### （2）安装之前先查看一下有无系统自带jdk   
```shell     
# rpm -qa |grep java
# rpm -qa |grep jdk
# rpm -qa |grep gcj
```   
图2   
图3   
##### （3）如果有就使用批量卸载命令   
```shell
# rpm -qa | grep java | xargs rpm -e --nodeps 
```   
##### （4）直接yum安装1.8.0版本openjdk   
```shell
# yum install java-1.8.0-openjdk* -y
```    
##### （5）检查版本    
```shell
$ java -version
```    
图4    
##### （6）生效配置（三选一，按照自己当前需求选择）     
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
##### （7）查看变量     
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
**注意：**如果报错：wget: command not found。可通过如下命令安装wget：   
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
**注意：**命名简化后，就可以通过   
```shell
$ cd kafka
```
进入Kafka目录。如果和本文路径安装不一致的话，应当参考自己的安装路径。   
### 三、安装ZooKeeper服务   
这里有三个方案可以选择：
- 方案一 使用kafka自带的zookeeper   
- 方案二 独立安装zookeeper服务   
- 方案三 使用hadoop集群cdh等套件中的zookeeper（目前探索中，未完待续）   
