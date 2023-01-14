---
title: '[Centos]-CentOS7安装MySql'
date: 2018-12-06 15:00:00
tags: 
- Linux
- MySQL
- 安装部署
- CentOS
categories: MySQL
---

#### 1.yum查找mysql依赖的包
```shell
yum search libaio
```
![查找](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTYwZThhZjViZjlmOTBmMTgucG5n?x-oss-process=image/format,png)

#### 2.yum下载mysql依赖的包
```shell
yum install libaio
```
![下载依赖包](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFiYThkNTRiZTE3NmNlODEucG5n?x-oss-process=image/format,png)
#### 3.搜索wget
```shell
yum search wget
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZjNWJlMTIzZTA0NWM3YjIucG5n?x-oss-process=image/format,png)

#### 4.下载wget
```shell
yum install wget.x86_64
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFlMTAxNmQ0NGJmNjE0MGYucG5n?x-oss-process=image/format,png)

#### 5.

```shell
wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZjMjdiMmM5ZWVmYjZjMTQucG5n?x-oss-process=image/format,png)

#### 6.
```shell
yum localinstall mysql-community-release-el7-5.noarch.rpm
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUzYzIwMTExODFjZTc1NTAucG5n?x-oss-process=image/format,png)

#### 7.验证是否添加成功
```shell
yum repolist enabled | grep "mysql.*-community.*"
```
![验证](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA4ZGU1MWU5ZTE4MTI1NGIucG5n?x-oss-process=image/format,png)


#### 8.安装：
```shell
yum install mysql-community-server
```
![安装](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU1ZjA0ZjY5NDNlZDY2ZWEucG5n?x-oss-process=image/format,png)

#### 9.查看是否安装好
```shell
whereis mysql
```
![查看](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE5NTlmYWNlMTZkZTljYzEucG5n?x-oss-process=image/format,png)

#### 10.启动mysql
```shell
systemctl start mysqld
```

#### 11.查看是否启动成功：
```shell
systemctl status mysqld
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVjZDlhMWI0NGRiNTM2Y2EucG5n?x-oss-process=image/format,png)

#### 12.关闭mysql
```shell
systemctl stop mysqld
```

#### 13.修改mysql密码：
##### (1)进入mysql
```shell
mysql
```
##### (2)使用mysql库
```shell
use mysql;
```

##### (3)修改密码：
```shell
update user set password=password('新密码') where user='要更改密码的用户名';
```
##### (4)修改用户权限列表：
```shell
flush privileges;
```

![mysql](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU4ZDI1YmQ5MzcyZjZkNDMucG5n?x-oss-process=image/format,png)

##### (5)重新登陆mysql
```shell
mysql -uroot -p

mysql -uroot -proot
```

![登陆](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTViNWVhZmIwMTg4OGU0NGIucG5n?x-oss-process=image/format,png)

