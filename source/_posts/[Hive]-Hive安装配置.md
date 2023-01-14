---
title: '[Hive]-Hive安装部署'
date: 2019-07-15 12:00:00
tags: 
- Hive
- 安装部署
categories: Hive
---

#### 1.前提：
* mysql已安装
* hadoop集群已配置

#### 2.步骤

##### (1)把apache-hive-1.2.1-bin.tar.gz上传到linux的/opt/software目录下

##### (2)解压apache-hive-1.2.1-bin.tar.gz到/opt/module/目录下面

```shell
tar -zxvf apache-hive-1.2.1-bin.tar.gz -C /opt/module/
```
##### (3)修改apache-hive-1.2.1-bin的名称为hive-1.2.1
```shell
cd /opt/module

mv apache-hive-1.2.1-bin/ hive-1.2.1
```

##### (4)修改环境变量：

```shell
vi /etc/profile
```
添加内容：

```shell
export HIVE_HOME=/opt/module/hive-1.2.1
export PATH=$PATH:$HIVE_HOME/bin
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFjNDUxZDNiMGRiOWU2MzUucG5n?x-oss-process=image/format,png)

使环境变量生效：

```shell
source /etc/profile
```

##### (5)修改/opt/module/hive/conf目录下的hive-env.sh.template名称为hive-env.sh
```shell
mv hive-env.sh.template hive-env.sh
```
##### (6)配置hive-env.sh文件

```shell
vi hive-env.sh
```
配置HADOOP_HOME路径
```shell
export HADOOP_HOME=/opt/module/hadoop-2.8.4
export HIVE_CONF_DIR=/opt/module/hive-1.2.1/conf
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFlMDZkYmY1Mjk0NzdmNzQucG5n?x-oss-process=image/format,png)


##### (7)注：Hive的log默认存放在/tmp/itstar/hive.log目录下（当前用户名下）。
###### (a)修改hive的log存放日志到/opt/module/hive/logs
###### (b)修改conf/hive-log4j.properties.template文件名称为hive-log4j.properties
```shell
pwd

mv hive-log4j.properties.template hive-log4j.properties
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU4YzhlMGEzMzUyNDlkMzUucG5n?x-oss-process=image/format,png)

###### (c )在hive-log4j.properties文件中修改log存放位置

```shell
cd /opt/module/hive-1.2.1

mkdir logs
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTExOWJhNzFlZTQ2OTUwMTkucG5n?x-oss-process=image/format,png)

```shell
cd /opt/module/hive-1.2.1/conf

vi hive-log4j.properties 
```
修改内容：

```shell
hive.log.dir=/opt/module/hive-1.2.1/logs
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY3ZDFiZTUxNWE0OWQwNjUucG5n?x-oss-process=image/format,png)

##### (8)Hadoop 配置
###### (a)必须启动hdfs和yarn
```shell
sbin/start-dfs.sh

sbin/start-yarn.sh
```
###### (b)在HDFS上创建/tmp和/user/hive/warehouse两个目录并修改他们的同组权限可写
```shell
hadoop fs -mkdir /tmp
hadoop fs -mkdir -p /user/hive/warehouse

hadoop fs -chmod g+w /tmp
hadoop fs -chmod g+w /user/hive/warehouse
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVlMjk4OTRjNjM4MTYyZjQucG5n?x-oss-process=image/format,png)
##### (9).将hive元数据配置到mysql
###### (a)将mysql-connector-java-5.1.27.jar上传到/opt/module/hive-1.2.1/lib中：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdmNTU2MmQ4YjJiMWM5OTUucG5n?x-oss-process=image/format,png)

###### (b)创建hive-site.xml
```shell
cd /opt/module/hive-1.2.1/conf

touch hive-site.xml

vi hive-site.xml
```

添加内容：

```xml
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <property>
      <name>javax.jdo.option.ConnectionURL</name>
      <value>jdbc:mysql://hadoop1:3306/metastore1?createDatabaseIfNotExist=true&amp;characterEncoding=utf-8&amp;useSSL=false</value>
      <description>JDBC connect string for a JDBC metastore</description>
    </property>

    <property>
      <name>javax.jdo.option.ConnectionDriverName</name>
      <value>com.mysql.jdbc.Driver</value>
      <description>Driver class name for a JDBC metastore</description>
    </property>

    <property>
      <name>javax.jdo.option.ConnectionUserName</name>
      <value>root</value>
      <description>username to use against metastore database</description>
    </property>

    <property>
      <name>javax.jdo.option.ConnectionPassword</name>
      <value>000000</value>
      <description>password to use against metastore database</description>
    </property>
</configuration>
```

注意：最好是在mysql所在的服务器中安装hive
###### (c ).配置完毕后，如果启动hive异常，可以重新启动虚拟机。(重启后，别忘了启动hadoop集群)

###### (d)在hive的bin目录下执行
```shell
./schematool -dbType mysql -initSchema
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJiNGMzNjdiOTcwNjM5ZDQucG5n?x-oss-process=image/format,png)
![创建成功](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUyMGY2ZTI5Nzg5OTE5YmIucG5n?x-oss-process=image/format,png)

##### (10).添加其他配置信息：

命令：

```shell
vi /opt/module/hive-1.2.1/conf/hive-site.xml
```

添加内容：
###### (a)修改default数据仓库原始位置
```xml
<property>
<name>hive.metastore.warehouse.dir</name>
<value>/user/hive/warehouse</value>
<description>location of default database for the warehouse</description>
</property>
```
###### (b)添加如下配置信息，就可以实现显示当前数据库，以及查询表的头信息配置

```xml
<property>
    <name>hive.cli.print.header</name>
    <value>true</value>
</property>

<property>
    <name>hive.cli.print.current.db</name>
    <value>true</value>
</property>
```

![配置](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBlMzIzNGUwYjNjMTMxMGUucG5n?x-oss-process=image/format,png)
![配置后启动hive](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVlMmZiOGFkYjQ0MzA5NmQucG5n?x-oss-process=image/format,png)

