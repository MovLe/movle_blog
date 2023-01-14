---
title: 'HBase安装部署'
date: 2019-04-08 12:00:00
tags: 
- HBase
- 安装部署
categories: HBase
---

### HBase部署
#### 1、Zookeeper正常部署
首先保证Zookeeper集群的正常部署，并启动之：
```shell
/opt/module/zookeeper-3.4.5/bin/zkServer.sh start
```
#### 2、Hadoop正常部署
Hadoop集群的正常部署并启动：
```shell
/opt/module/hadoop-2.8.4/sbin/start-dfs.sh
/opt/module/hadoop-2.8.4/sbin/start-yarn.sh
```
#### 3、HBase的解压
解压HBase到指定目录：
```shell
tar -zxvf /opt/soft/hbase-1.3.1-bin.tar.gz -C /opt/module/
```
#### 4、HBase的配置文件
需要修改HBase对应的配置文件。

##### (1)hbase-env.sh:

```shell
cd /opt/module/hbase-1.3.1/conf

vi hbase-env.sh
```
修改内容：

```shell
export JAVA_HOME=/opt/module/jdk1.8.0_144
export HBASE_MANAGES_ZK=false
```
![hbase-env.sh](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTllMGY0YjVjYzcxZWRjMzIucG5n?x-oss-process=image/format,png)

提示：如果使用的是JDK8以上版本，注释掉hbase-env.sh的45-47行，不然会报警告

![注释](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJiNjFhYTgzYmVmZTgxOGQucG5n?x-oss-process=image/format,png)

##### (2)hbase-site.xml
```shell
vi hbase-site.xml
```

修改内容：
```xml
	<property>  
		<name>hbase.rootdir</name>  
		<value>hdfs://hadoop1:9000/hbase</value>  
	</property>

	<property>
		<name>hbase.cluster.distributed</name>
		<value>true</value>
	</property>

	<property>
		<name>hbase.master.port</name>
		<value>16000</value>
	</property>

	<property>  
		<name>hbase.zookeeper.quorum</name>
		<value>hadoop1:2181,hadoop2:2181,hadoop3:2181</value>
	</property>

	<property>  
		<name>hbase.zookeeper.property.dataDir</name>
	    <value>/opt/module/zookeeper-3.4.10/zkData</value>
	</property>
    <property>
         <name>hbase.master.maxclockskew</name>
         <value>180000</value>
    </property>
```

![hbase-site.xml](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM3NzM0ODVjMmQ4OTM4MjEucG5n?x-oss-process=image/format,png)

##### (3)regionservers：

```shell
vi regionservers
```
修改内容：
```shell
hadoop1
hadoop2
hadoop3
```
![regionservers](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUxNGVhY2ZkNmE5ZjBiY2QucG5n?x-oss-process=image/format,png)
![修改配置文件](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNkNmUxNTM4NGRkZTQyZGEucG5n?x-oss-process=image/format,png)


#### 5.HBase需要依赖的Jar包(额外，不用配置,当hbase与hadoop版本不一致时，可能会出现hbase输出存储不到hdfs的情况，那时需要配置这些)
由于HBase需要依赖Hadoop，所以替换HBase的lib目录下的jar包，以解决兼容问题：

##### (1). 删除原有的jar：
```shell
rm -rf /opt/module/hbase-1.3.1/lib/hadoop-*
rm -rf /opt/module/hbase-1.3.1/lib/zookeeper-3.4.10.jar
```
##### (2). 拷贝新jar，涉及的jar有：
```shell
hadoop-annotations-2.8.4.jar
hadoop-auth-2.8.4.jar
hadoop-client-2.8.4.jar
hadoop-common-2.8.4.jar
hadoop-hdfs-2.8.4.jar
hadoop-mapreduce-client-app-2.8.4.jar
hadoop-mapreduce-client-common-2.8.4.jar
hadoop-mapreduce-client-core-2.8.4.jar
hadoop-mapreduce-client-hs-2.8.4.jar
hadoop-mapreduce-client-hs-plugins-2.8.4.jar
hadoop-mapreduce-client-jobclient-2.8.4.jar
hadoop-mapreduce-client-jobclient-2.8.4-tests.jar
hadoop-mapreduce-client-shuffle-2.8.4.jar
hadoop-yarn-api-2.8.4.jar
hadoop-yarn-applications-distributedshell-2.8.4.jar
hadoop-yarn-applications-unmanaged-am-launcher-2.8.4.jar
hadoop-yarn-client-2.8.4.jar
hadoop-yarn-common-2.8.4.jar
hadoop-yarn-server-applicationhistoryservice-2.8.4.jar
hadoop-yarn-server-common-2.8.4.jar
hadoop-yarn-server-nodemanager-2.8.4.jar
hadoop-yarn-server-resourcemanager-2.8.4.jar
hadoop-yarn-server-tests-2.8.4.jar
hadoop-yarn-server-web-proxy-2.8.4.jar
zookeeper-3.4.10.jar
```
提示：这些jar包的对应版本应替换成你目前使用的hadoop版本，具体情况具体分析。

查找jar包举例：
```shell
find /opt/module/hadoop-2.8.4/ -name hadoop-annotations*
```
然后将找到的jar包复制到HBase的lib目录下即可。

#### 6.HBase软连接Hadoop配置(额外，不用配置)

```shell
ln -s /opt/module/hadoop-2.8.4/etc/hadoop/core-site.xml /opt/module/hbase-1.3.1/conf/core-site.xml

ln -s /opt/module/hadoop-2.8.4/etc/hadoop/hdfs-site.xml /opt/module/hbase-1.3.1/conf/hdfs-site.xml
```

#### 7.配置环境变量
```shell
vi /etc/profile
```
修改内容：
```shell
export HBASE_HOME=/opt/module/hbase-1.3.1
export PATH=$PATH:$HBASE_HOME/bin
```

![配置环境变量](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFhZTI5YzBiMWVlZTU3OWQucG5n?x-oss-process=image/format,png)

使环境变量生效
```shell
source /etc/profile
```

#### 8、HBase远程scp到其他集群
```shell
scp -r /opt/module/hbase-1.3.1/ root@hadoop2:/opt/module/

scp -r /opt/module/hbase-1.3.1/ root@hadoop3:/opt/module/
```

#### 9、HBase服务的启动
##### (1)启动方式1：
```shell
bin/hbase-daemon.sh start master
bin/hbase-daemon.sh start regionserver
```
提示：如果集群之间的节点时间不同步，会导致regionserver无法启动，抛出ClockOutOfSyncException异常。

##### (2)启动方式2：
```shell
bin/start-hbase.sh
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNjMzBlNTlmMGFmNDBiZGQucG5n?x-oss-process=image/format,png)

对应的停止服务：
```shell
bin/stop-hbase.sh
```
#### 10、查看Hbse页面
启动成功后，可以通过“host:port”的方式来访问HBase管理页面，例如：
http://hadoop1:16010

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY3NGRkMDMzMjIxN2MwYTkucG5n?x-oss-process=image/format,png)

