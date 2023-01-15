---
title: '[Hadoop]-Hadoop3.0-全分布模式搭建'
date: 2023-01-15 11:49:00
tags: 
- Hadoop
- 安装部署
categories: Hadoop
---
### 0.集群配置
||bigdata101|bigdata102|bigdata103|
|-|-|-|-|
|HDFS|NameNode,DataNode|DataNode|SecondaryNameNode,DataNode|
|YARN|NodeManager|ResourceManager,NodeManager|NodeManager|

### 1.前提

* 防火墙已关闭
* JDK已安装
* 机器之间已经设置ssh免密登录

### 2.解压Hadoop安装包
```shell
cd /opt/soft
tar -zxvf hadoop-3.1.3.tar.gz -C /opt/module/
```

### 3.添加环境变量
(1)进入环境变量目录
```shell
cd /etc/profile.d
vi my_env.sh
```

(2)添加内容：
```shell
# HADOOP_HOME
export HADOOP_HOME=/opt/module/hadoop-3.1.3
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin

# 配置用户
export HDFS_NAMENODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
export YARN_RESOURCEMANAGER_USER=root
export YARN_NODEMANAGER_USER=root
```

(3)保存后让文件生效
```shell
source /etc/profile 
```

### 4.集群配置
(1)core-site.xml
```xml
<configuration>
    <!-- 指定NameNode的地址 -->
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://bigdata101:8020</value>
    </property>
    <!-- 指定hadoop数据的存储目录 -->
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/opt/module/hadoop-3.1.3/data</value>
    </property>
</configuration>
```
(2)hdfs-site.xml
```xml
<configuration>
    <!-- nn web端访问地址-->
    <property>
        <name>dfs.namenode.http-address</name>
        <value>bigdata101:9870</value>
    </property>
    <!-- 2nn web端访问地址-->
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>bigdata103:9868</value>
    </property>
</configuration>
```
(3)yarn-site.xml
```xml
<configuration>
<!-- Site specific YARN configuration properties -->
    <!-- 指定MR走shuffle -->
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>

    <!-- 指定ResourceManager的地址-->
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>bigdata102</value>
    </property>

    <!-- 环境变量的继承 -->
    <property>
        <name>yarn.nodemanager.env-whitelist</name>
       <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
    </property>

    <!-- 开启日志聚集功能 -->
    <property>
        <name>yarn.log-aggregation-enable</name>
        <value>true</value>
    </property>
    <!-- 设置日志聚集服务器地址 -->
    <property>
        <name>yarn.log.server.url</name>
        <value>http://bigdata101:19888/jobhistory/logs</value>
    </property>
    <!-- 设置日志保留时间为7天 -->
    <property>
        <name>yarn.log-aggregation.retain-seconds</name>
        <value>604800</value>
    </property>
</configuration>
```
(4)mapred-site.xml
```xml
<configuration>
    <!-- 指定MapReduce程序运行在Yarn上 -->
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>

    <!-- 历史服务器端地址 -->
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>bigdata101:10020</value>
    </property>

    <!-- 历史服务器web端地址 -->
    <property>
        <name>mapreduce.jobhistory.webapp.address</name>
        <value>bigdata101:19888</value>
    </property>
</configuration>
```

(5)workers
```shell
bigdata101
bigdata102
bigdata103
```

### 5.分发Hadoop文件及其配置文件
```shell
xsync /opt/module/hadoop-3.1.3

xsync /etc/profile.d/my_env.sh

source /etc/profile   # 三台机器都需要将环境变量生效
```

### 6.启动集群
```shell
hdfs namenode -format # 第一次初始化namenode  bigdata101

start-dfs.sh.      # 启动hdfs

start-yarn.sh      # 启动yarn

mapred --daemon start historyserver  # 启动历史服务器 bigdata101
```