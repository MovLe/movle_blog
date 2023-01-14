---
title: 'ZooKeeper客户端命令行操作'
date: 2019-01-03 14:50:00
tags: ZooKeeper
categories: ZooKeeper
---
#### 0.基础语法

|命令基本语法|功能描述|
|:-:|:-:|
|help|显示所有操作命令|
|ls path [watch]|使用 ls 命令来查看当前znode中所包含的内容|
|ls2 path [watch]|查看当前节点数据并能看到更新次数等数据|
|create|普通创建(永久节点)|
|-s|含有序列|
|-e|临时(重启或者超时消失)|
|get path [watch]|获得节点的值|
|set|设置节点的具体值|
|stat|查看节点状态|
|delete|删除节点|
|rmr|递归删除节点|

#### 1.启动客户端
```shell
bin/zkCli.sh
```
#### 2.显示所有操作命令
```shell
help
```
![help](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA2MDBjNzlhNzExOTVmNTEucG5n?x-oss-process=image/format,png)
#### 3.查看当前znode中所包含的内容
```shell
ls /
```
![ls /](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFlY2EyODgzMDg3NDQ3NmQucG5n?x-oss-process=image/format,png)
#### 4.查看当前节点数据并能看到更新次数等数据
```shell
ls2 /
```
![ls2 /](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTlkZmE4NzhiNzZjNDAwMWUucG5n?x-oss-process=image/format,png)
#### 5.创建普通节点
```shell
create /app1 "hello app1"
```
![create /app1 "hello app1"](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNmYjdkNTU3OTYyYTNkMmYucG5n?x-oss-process=image/format,png)
```shell
create /app1/server101 "192.168.1.101"
```
![create /app1/server101 "192.168.1.101"](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNmZTM4NmUwMzQ0Y2NmNTcucG5n?x-oss-process=image/format,png)
#### 6.获得节点的值
```shell
get /app1
```
![get /app1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFhODA3YTJhNDZkYjYzMGMucG5n?x-oss-process=image/format,png)
```shell
get /app1/server101
```
![get /app1/server101](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVhMzA3ZTExMzhmZWQ1MjAucG5n?x-oss-process=image/format,png)
#### 7.创建短暂节点
```shell
create -e /app-emphemeral 8888
```

(1)在当前客户端是能查看到的
```shell
ls /
```
![ls /](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWYwNDdmZWZlNjgwNWIzN2MucG5n?x-oss-process=image/format,png)

(2)退出当前客户端然后再重启客户端
```shell
quit

bin/zkCli.sh
```
(3)再次查看根目录下短暂节点已经删除
```	shell
ls /
```

![ls /](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRhMzgyYjAxYzUzZWVlODgucG5n?x-oss-process=image/format,png)

#### 8.创建带序号的节点
(1)先创建一个普通的根节点app2
```shell
create /app2 "app2"
```
(2)创建带序号的节点
```shell
create -s /app2/aa 888
```

```shell
create -s /app2/bb 888
```

```shell
create -s /app2/cc 888
```


如果原节点下有1个节点，则再排序时从1开始，以此类推。
```shell
create -s /app1/aa 888
```


![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNjYTViZTQyMWE1NjNkN2IucG5n?x-oss-process=image/format,png)

#### 9.修改节点数据值
```shell
set /app1 999
```
![set /app1 999](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNiYmRiNTdkNzIzYzQwM2UucG5n?x-oss-process=image/format,png)
#### 10.节点的值变化监听
(1)在104主机上注册监听/app1节点数据变化
```shell
get /app1 watch
```
![get /app1 watch](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ5MzIzZThiMDBjNjM5ZDMucG5n?x-oss-process=image/format,png)

(2)在103主机上修改/app1节点的数据
```shell
set /app1  777
```	
(3)观察104主机收到数据变化的监听
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE1MzA1OGQ4NjVjZjE5MTMucG5n?x-oss-process=image/format,png)


#### 11.节点的子节点变化监听（路径变化）
(1)在104主机上注册监听/app1节点的子节点变化
```
ls /app1 watch
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUyNGYwYjhmOTA3NWM5MDMucG5n?x-oss-process=image/format,png)

(2)在103主机/app1节点上创建子节点
```shell
create /app1/bb 666
```

(3)观察104主机收到子节点变化的监听
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc4OThmMzIxZTcwNzQ2NmMucG5n?x-oss-process=image/format,png)
#### 12.删除节点
```shell
delete /app1/bb
```
#### 13.递归删除节点
```shell
rmr /app2
```
#### 14.查看节点状态
```shell
stat /app1
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTEzODk2ZTM4ZDU5NTVkYmMucG5n?x-oss-process=image/format,png)