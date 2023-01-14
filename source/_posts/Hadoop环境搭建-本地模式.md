---
title: 'Hadoop环境搭建-本地模式搭建'
date: 2019-01-01 11:49:00
tags: 
- Hadoop
- 安装部署
categories: Hadoop
---
#### 1.Hadoop安装准备工作：
(1)安装好linux操作系统
(2)关闭防火墙
(3)在linux上安装JDK

#### 2.本地模式具体步骤：
##### (1)解压hadoop安装包：
```shell
tar -zxvf hadoop-2.8.4.tar.gz -C /opt/module 
```
##### (2)配置环境变量：
###### (a).修改命令：
```shell
vi ~/.bash_profile          //修改环境变量的文件
```
######  (b).添加的内容为：
```shell
HADOOP_HOME=/opt/module/hadoop-2.8.4
export HADOOP_HOME
PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
export PATH
```
###### (c ).使环境变量生效
输入命令：

```shell
source ~/.bash_profile
```

##### (3)修改配置文件hadoop-env.sh
###### (a).进入hadoop-2.8.4/etc/hadoop文件夹
输入命令：
```shell
cd /opt/module/hadoop-2.8.4/etc/hadoop              //进入hadoop-2.8.4/etc/hadoop文件夹
```
###### (b).修改配置文件hadoop-env.sh：
输入命令：

```shell
vi hadoop-env.sh                             //修改hadoop-env.sh文件
```
修改内容为：
```shell
export JAVA_HOME=/opt/module/jdk1.8.0_144          //修改JAVAHOME地址，改为自己建的jdk地址，应该在25行              
```
![jdk](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZlZWZiZWIzYjcxYWFlNjkucG5n?x-oss-process=image/format,png)


###### (c ).验证（运行一个MR程序）
(1)在/root/temp目录下创建一个a.txt文件
(2)修改a.txt文件
```shell
touch a.txt                                 //新建a.txt文件
vi a.txt                                    //修改a.txt

i am a tiger you are also a tiger           //添加内容
```
(3)进入/opt/module/hadoop-2.8.4/share/hadoop/mapreduce文件夹下
(4)运行wordcount程序 hadoop jar hadoop-mapreduce-examples-2.8.4.jar wordcount ~/temp/a.txt  ~/temp/output/wc0717
```shell
cd /opt/module/hadoop-2.8.4/share/hadoop/mapreduce   //进入文件夹

//hadoop jar 程序包 函数名 输入文件地址（这里是本地linux路径，因为本地模式没有hdfs） 输出文件地址
hadoop jar hadoop-mapreduce-examples-2.8.4.jar wordcount ~/temp/a.txt  ~/temp/output/wc0717     //执行MR程序
```
运行成功后，会在/root/temp/output/文件夹下，生产wc0717文件下，此文件夹下有两个文件part-r-000000和_SUCCESS
![wordcount程序运行结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFkNTUwN2U5NmY0MGZkNGEucG5n?x-oss-process=image/format,png)
