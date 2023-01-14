---
title: '[Sqoop]-Sqoop安装配置'
date: 2019-05-09 12:00:00
tags: 
- Sqoop
- 安装部署
categories: Sqoop
---
#### 0.前提
安装Sqoop的前提是已经具备Java和Hadoop的环境。

#### 1.下载并解压

##### (1)最新版下载地址：
[http://mirrors.hust.edu.cn/apache/sqoop/1.4.6/](http://mirrors.hust.edu.cn/apache/sqoop/1.4.6/)

#### 2. 上传安装包sqoop-1.4.6.bin__hadoop-2.0.4-alpha.tar.gz到虚拟机中，如我的上传目录是：/opt/soft/


#### 3.解压sqoop安装包到指定目录，如：
```shell
cd /opt/soft

tar -zxvf sqoop-1.4.6.bin__hadoop-2.0.4-alpha.tar.gz -C /opt/module/
```

#### 4. 修改配置文件

Sqoop的配置文件与大多数大数据框架类似，在sqoop根目录下的conf目录中。

##### (1).重命名配置文件

```shell
mv sqoop-env-template.sh sqoop-env.sh

mv sqoop-site-template.xml sqoop-site.xml     //此行不用做
```

##### (2).修改配置文件

sqoop-env.sh
```shell
cd /opt/module/sqoop-1.4.6.bin__hadoop-2.0.4-alpha/conf  

vi sqoop-env.sh
```
添加内容：
```shell
export HADOOP_COMMON_HOME=/opt/module/hadoop-2.8.4

export HADOOP_MAPRED_HOME=/opt/module/hadoop-2.8.4

export HIVE_HOME=/opt/module/hive-1.2.1 

export ZOOKEEPER_HOME=/opt/module/zookeeper-3.4.10 

export ZOOCFGDIR=/opt/module/zookeeper-3.4.10/conf
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTAxNjE5ODMzNGJlMjZmZmUucG5n?x-oss-process=image/format,png)

#### 5.拷贝JDBC驱动

拷贝jdbc驱动到sqoop的lib目录下，如：
```shell
cp -a mysql-connector-java-5.1.27-bin.jar /opt/module/sqoop-1.4.6.bin__hadoop-2.0.4-alpha/lib
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNlNjg3ZDVhODZmYThjMmMucG5n?x-oss-process=image/format,png)

#### 6. 验证Sqoop

我们可以通过某一个command来验证sqoop配置是否正确：
```shell
$ bin/sqoop help
```
出现一些Warning警告（警告信息已省略），并伴随着帮助命令的输出：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTMxOTQ4NWJlNWQ5NjhhNGQucG5n?x-oss-process=image/format,png)
#### 7.测试Sqoop是否能够成功连接数据库
```shell
bin/sqoop list-databases --connect jdbc:mysql://hadoop1:3306/ --username root --password 000000
```
出现如下输出：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWEzNDllODJmMmI5NTI0YjgucG5n?x-oss-process=image/format,png)

#### 8.注释配置文件
注:注释掉configure-sqoop 134行到143行的内容，内容如下

```shell
cd /opt/module/sqoop-1.4.6.bin__hadoop-2.0.4-alpha/bin 

vi configure-sqoop
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUwODdlMWYxMWUwYjU4ZjMucG5n?x-oss-process=image/format,png)
```shell
134 ## Moved to be a runtime check in sqoop.

135 #if [ ! -d "${HCAT_HOME}" ]; then

136 #  echo "Warning: $HCAT_HOME does not exist! HCatalog jobs will fail."

137 #  echo 'Please set $HCAT_HOME to the root of your HCatalog installation.'

138 #Φ

139 #

140 #if [ ! -d "${ACCUMULO_HOME}" ]; then

141 #  echo "Warning: $ACCUMULO_HOME does not exist! Accumulo imports will fail."

142 #  echo 'Please set $ACCUMULO_HOME to the root of your Accumulo installation.'

143 #Φ
```

注释前：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUyMzkwZjFlN2ZlZDFhYjcucG5n?x-oss-process=image/format,png)

注释后：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI1NjIzNTBkMDg3NWY0ZmQucG5n?x-oss-process=image/format,png)

验证：
```shell
bin/sqoop list-databases --connect jdbc:mysql://hadoop1:3306/ --username root --password 000000
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTcwMmY4NDdkNDc2M2VlNzcucG5n?x-oss-process=image/format,png)