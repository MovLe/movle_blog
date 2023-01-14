---
title: 'Hadoop-全分布模式搭建'
date: 2019-01-01 10:49:00
tags: 
- Hadoop
- 安装部署
categories: Hadoop
---
#### 1.Hadoop安装准备工作：
##### (1)安装好linux操作系统
##### (2)关闭防火墙
##### (3)在linux上安装JDK
##### (4)hadoop2,hadoop3,hadoop4三台服务器已经设置过免密登陆

#### 2.解压Hadoop压缩包并配置环境变量
##### (1)将Hadoop安装包拷贝到/opt/software文件目录下  
##### (2)将Hadoop安装包解压到/opt/module文件目录下
命令为：

```Bash
tar -zxvf hadoop-2.8.4.tar.gz -C /opt/module          //将hadoop-2.8.4.tar.gz解压到/opt/module目录下
```
##### (3)配置环境变量  
###### (a)修改环境变量配置文件
修改命令：

```Bash
vi ~/.bash_profile          //修改环境变量的文件
```
添加的内容为：
```Bash
HADOOP_HOME=/opt/module/hadoop-2.8.4
export HADOOP_HOME
PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
export PATH
```
###### (b)使环境变量生效
输入命令：

```Bash
source ~/.bash_profile
```
#### 3.修改配置文件
##### (1)修改hadoop-env.sh
命令：
```Bash
vi hadoop-env.sh                             //修改hadoop-env.sh文件
```
修改内容为：
```Bash
export JAVA_HOME=/opt/module/jdk1.8.0_144          //修改JAVAHOME地址，改为自己建的jdk地址，应该在25行              
```
![jdk](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRmYmMyMDg1MDM4MWJhNzkucG5n?x-oss-process=image/format,png)

##### (2)修改hdfs-site.xml
命令：
```Bash
cd /opt/module/hadoop-2.8.4/etc/hadoop              //进入etc/hadoop目录

vi hdfs-site.xml                          // 修改hdfs-site.xml文件
```
修改内容为：

```Bash
<!--配置数据块的冗余度，默认是3-->
<property>
    <name>dfs.replication</name>
    <value>2</value>
</property> 
<property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>hadoop3:50090</value>
</property>
<!-- 配置HDFS的权限检查，默认是true-->
<!--
<property>
    <name>dfs.permissions</name>
    <value>false</value>
</property>  
-->
```
![hdfs-site.xml](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE3MTdiMjc4NWY0NjJlYTYucG5n?x-oss-process=image/format,png)


##### (3)修改core-site.xml
命令：
```Bash
vi core-site.xml
```
修改内容为：
```Bash
<!--配置HDFS的主节点，namenode地址，9000是RPC通信端口-->
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://hadoop2:9000</value>
</property> 
<!--配置HDFS数据块和元数据保存的目录,一定要修改 -->
<property>
    <name>hadoop.tmp.dir</name>
    <value>/opt/module/hadoop-2.8.4/data/tmp</value>      
</property> 
```
![core-site.xml](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM5OGJmYzI2ZmUzZTcxOTkucG5n?x-oss-process=image/format,png)

##### (4)修改mapred-site.xml(默认是没有的，需要从mapred-site.xml.template复制转化而来)   
   
命令：    
```Bash
cp mapred-site.xml.template mapred-site.xml       //从mapred-site.xml.template转化
    
vi mapred-site.xml             //修改mapred-site.xml 文件
```
修改内容为：
  
```Bash
<!--配置MR程序运行的框架，Yarn-->
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>      
</property>            
```
![mapred-site.xml](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg2YmFiZjA5OTJmODAzMzQucG5n?x-oss-process=image/format,png)

##### (5)修改yarn-site.xml

命令：
```Bash
vi yarn-site.xml 
```
修改内容为：
```Bash
<!--配置Yarn的节点-->
<property>
    <name>yarn.resourcemanager.hostname</name>
    <value>hadoop2</value>      
</property>
<!--NodeManager执行MR任务的方式是Shuffle洗牌-->
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>      
</property>
```
![yarn-site.xml](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTYyNmQzOGY2NDI0NGNlZDgucG5n?x-oss-process=image/format,png)

##### (6)修改slaves

命令：
```Bash
vi slaves
```
修改内容为：
```Bash
hadoop3            //hadoop3作为从节点
hadoop4            //hadoop4作为从节点
```
![slaves](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNhNzBlZWI4MjM1NDhkZmYucG5n?x-oss-process=image/format,png)

#### 4.通过HDFS namenode格式化(注意，要再namenode结点所在服务器格式化，本次即是在hadoop2中进行格式化)

命令：
```Bash
cd /opt/module/hadoop-2.8.4/data/tmp         //这里是step3配置的HDFS数据库和元数据存储目录

hdfs namenode -format                  //格式化
```

验证是否成功，成功后回显示：
```Bash
Storage: Storage directory /opt/module/hadoop-2.8.4/tmp/dfs/name has been successfully formatted
```
![验证格式化成功](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTcyNzA1NmY3ZmYzMzEwNTEucG5n?x-oss-process=image/format,png)

**注意**
重复格式化，hadoop.tmp.dir 先停止集群，然后在删除tmp文件夹，再重新新建tmp文件夹，重新格式化，然后再启动集群

#### 5.通过scp拷贝，将hadoop2配置好的hadoop发送到另外两台机器上：

命令：
```Bash
//拷贝到hadoop4
scp -r /opt/moudle/hadoop-2.8.4/ root@hadoop3:/opt/moudle/         

//拷贝到hadoop4
scp -r /opt/moudle/hadoop-2.8.4/ root@hadoop4:/opt/moudle/        
```

#### 6.启动Hadoop集群
##### (1)启动
输入命令
```Bash
start-all.sh             //hadoop2中启动，因为此机器是主节点
```
![启动](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA3ZGFhZTc2MmQ5YmU5NmIucG5n?x-oss-process=image/format,png)

##### (2)验证是否启动：
hadoop2:
![hadoop2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTMzZmE0MTk4ODA4NjBjMzQucG5n?x-oss-process=image/format,png)
hadoop3:
![hadoop3](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU2ZWRiY2Y4ZWNmMTc1ODUucG5n?x-oss-process=image/format,png)
hadoop4:
![hadoop4](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ2ZTM5Nzg0MzI5MTAxOWIucG5n?x-oss-process=image/format,png)

与规划的相同，故Hadoop全分布安装成功