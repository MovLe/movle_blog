---
title: 'CentOS7(单节点)安装Mongodb'
date: 2018-12-07 11:00:00
tags: 
- Linux
- Mongodb
- 安装部署
- CentOS
categories: Mongodb
---

#### 1.上传安装包:将mongodb安装包上传到/usr/local/tars文件目录
![上传](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJiNGMzNDVkM2IwMTViOTUucG5n?x-oss-process=image/format,png)

#### 2.解压:解压到/usr/local下

```shell
cd /usr/local/tars

tar -zxvf mongodb-linux-x86_64-rhel62-3.4.3.tgz -C /usr/local/
```
![解压](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQxYWY4YjY5ODc3MTI3ZGIucG5n?x-oss-process=image/format,png)

#### 3.新建文件夹
```shell
cd /usr/local/

mv mongodb-linux-x86_64-rhel62-3.4.3/ mongodb                        //重命名

cd mongodb

mkdir data

cd /usr/local/mongodb/data

mkdir db                         //用作存放数据
mkdir logs                      //用作存放日志

cd /usr/local/mongodb/data/logs

touch mongodb.log        //新建文件以存放日志
```
![新建目录](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQwMzBiOGViNWVhZmExMmIucG5n?x-oss-process=image/format,png)

#### 4.修改配置文件
```shell
cd /usr/local/mongodb/data

touch mongodb.conf            //新建配置文件

vi mongodb.conf                 //修改配置文件
```

修改内容如下：

```shell
dbpath=/usr/local/mongodb/data/db                                      //数据库存放目录

logpath=/usr/local/mongodb/data/logs/mongodb.log                    //日志地址

fork = true                                                       //是否后台运行

logappend = true                                            //日志输出形式
```

![修改配置文件](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQzMTlkODg2ZDJhMDgwY2UucG5n?x-oss-process=image/format,png)

#### 5.启动
```shell
cd /usr/local/mongodb

./bin/mongod -config ./data/mongodb.conf                      //启动
```

![启动](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNlMzI0ZTUyMjU3Mjg5YWYucG5n?x-oss-process=image/format,png)

#### 6.关闭：
```shell
cd /usr/local/mongodb

./bin/mongod -shutdown -config ./data/mongodb.conf     //关闭
```

