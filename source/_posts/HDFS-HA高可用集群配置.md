---
title: 'HDFS-HA高可用集群配置'
date: 2019-01-02 12:50:00
tags: 
- Hadoop
- HDFS
- 安装部署
categories: Hadoop
---
### 一. HDFS-HA集群配置
#### 1 环境准备
(1)修改IP
(2)修改主机名及主机名和IP地址的映射
(3)关闭防火墙
(4)ssh免密登录
(5)安装JDK，配置环境变量等
(6)hadoop1,hadoop2,hadoop3集群已经配置好zookeeper集群

#### 2 规划集群
|hadoop1|hadoop2|hadoop3|
|:-:|:-:|:-:|
NameNode|NameNode|
|JournalNode|JournalNode|JournalNode|
|DataNode|DataNode|DataNode|	
|ZK|ZK|ZK|
|zkfc|zkfc||
||ResourceManager||
|NodeManager|NodeManager|NodeManager|

#### 3.具体步骤
(1).在opt目录下创建一个HA文件夹
```shell
cd /opt

mkdir HA
```
(2).将hadoop-2.8.4压缩包解压到/opt/HA目录下
```shell
tar -zxvf hadoop-2.8.4.tar.gz -C /opt/HA/
```
(3).配置hadoop-env.sh
```shell
export JAVA_HOME=/opt/module/jdk1.8.0_144
```
(4).配置core-site.xml
```xml
<configuration>
    <!-- 把两个NameNode）的地址组装成一个集群mycluster -->
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://mycluster</value>
    </property>
    <!-- 指定hadoop运行时产生文件的存储目录 -->
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/opt/HA/hadoop-2.8.4/data/tmp</value>
    </property>
    <!--配置zookeeper-->
	<property>
		<name>ha.zookeeper.quorum</name>
		<value>hadoop1:2181,hadoop2:2181,hadoop3:2181</value>
	</property>
</configuration>
```
(5).配置hdfs-site.xml
```xml
<configuration>
       <!-- 完全分布式集群名称 -->
       <property>
              <name>dfs.nameservices</name>
              <value>mycluster</value>
       </property>
       <!-- 集群中NameNode节点都有哪些 -->
       <property>
              <name>dfs.ha.namenodes.mycluster</name>
              <value>nn1,nn2</value>
       </property>
       <!-- nn1的RPC通信地址 -->
       <property>
              <name>dfs.namenode.rpc-address.mycluster.nn1</name>
              <value>hadoop1:9000</value>
       </property>
       <!-- nn2的RPC通信地址 -->
       <property>
              <name>dfs.namenode.rpc-address.mycluster.nn2</name>
              <value>hadoop2:9000</value>
       </property>
       <!-- nn1的http通信地址 -->
       <property>
              <name>dfs.namenode.http-address.mycluster.nn1</name>
              <value>hadoop1:50070</value>
       </property>
       <!-- nn2的http通信地址 -->
       <property>
              <name>dfs.namenode.http-address.mycluster.nn2</name>
              <value>hadoop2:50070</value>
       </property>
       <!-- 指定NameNode元数据在JournalNode上的存放位置 -->
       <property>
              <name>dfs.namenode.shared.edits.dir</name>
       <value>qjournal://hadoop1:8485;hadoop2:8485;hadoop3:8485/mycluster</value>
       </property>
       <!-- 配置隔离机制，即同一时刻只能有一台服务器对外响应 -->
       <property>
              <name>dfs.ha.fencing.methods</name>
              <value>sshfence</value>
       </property>
       <!-- 使用隔离机制时需要ssh无秘钥登录-->
       <property>
              <name>dfs.ha.fencing.ssh.private-key-files</name>
              <value>/root/.ssh/id_rsa</value>
              <!--这里主要是看自己是什么用户，我是root用户-->
       </property>
       <!-- 声明journalnode服务器存储目录-->
       <property>
              <name>dfs.journalnode.edits.dir</name>
              <value>/opt/HA/hadoop-2.8.4/data/jn</value>
       </property>
       <!-- 关闭权限检查-->
       <property>
              <name>dfs.permissions.enable</name>
              <value>false</value>
       </property>
       <!-- 访问代理类：client，mycluster，active配置失败自动切换实现方式-->
       <property>
            <name>dfs.client.failover.proxy.provider.mycluster</name>
            <value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
       </property>
       <property>
			<name>dfs.ha.automatic-failover.enabled</name>
			<value>true</value>
		</property>
</configuration>
```
 
(6).配置slaves文件：
```shell
cd /opt/HA/hadoop-2.8.4/etc/hadoop

vi slaves
```
修改内容：
```shell
hadoop1
hadoop2
hadoop3
```

(7).拷贝配置好的hadoop环境到其他节点

```shell
scp -r /opt/HA/* root@hadoop2:/opt/HA/

scp -r /opt/HA/* root@hadoop3:/opt/HA/
```



#### 4 初始化namenode
(1).在各个JournalNode节点上，输入以下命令启动journalnode服务：
```shell
sbin/hadoop-daemon.sh start journalnode
```
(2).在[nn1]上，对其进行格式化，并启动：
hadoop1:
```shell
bin/hdfs namenode -format

sbin/hadoop-daemon.sh start namenode
```
(3).在[nn2]上，同步nn1的元数据信息：
```shell
bin/hdfs namenode -bootstrapStandby
```
(4).启动[nn2]：
hadoop2:
```shell
sbin/hadoop-daemon.sh start namenode
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTAxNjEyODllMGMxZDVmOTgucG5n?x-oss-process=image/format,png)

(5).查看web页面显示

![hadoop1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFlYjg3YjJkZTcyNDRlNmMucG5n?x-oss-process=image/format,png)
![hadoop2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJlNjNhOWE3NTg1ZWFlYWEucG5n?x-oss-process=image/format,png)
##### 5.高可用HDFS集群启动

(1)关闭所有HDFS服务：
hadoop1
```shell
sbin/stop-dfs.sh
```
hadoop2:

```shell
sbin/hadoop-daemon.sh stop namenode  
```

(2)启动Zookeeper集群,每一台都启动zookeeper：
```shell
bin/zkServer.sh start
```
(3)初始化HA在Zookeeper中状态：
在hadoop1中：

```shell
bin/hdfs zkfc -formatZK
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNmMzlhYTRlNTBiN2NlNTIucG5n?x-oss-process=image/format,png)

(4)启动HDFS服务：
```shell
sbin/start-dfs.sh
```	
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTMzNzVkYzIwNzI0N2UxMTMucG5n?x-oss-process=image/format,png)

(5)在各个NameNode节点上启动DFSZK Failover Controller，先在哪台机器启动，哪个机器的NameNode就是Active NameNode
```shell	
sbin/hadoop-daemin.sh start zkfc
```
(若是直接设置自动切换，DFSZK Failover Controller没有启动，则需要走这一步)

##### 6.验证
(1)将Active NameNode进程kill
```shell
kill -9 namenode的进程id
```
(2)将Active NameNode机器断开网络
```shell
service network stop
```