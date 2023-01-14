---
title: 'CentOS7安装MySql(已下载的mysql安装包)'
date: 2018-12-06 11:00:00
tags: 
- Linux
- MySQL
- 安装部署
- CentOS
categories: MySQL
---

#### 1.将下载好的安装包上传到linux：

#### 2.检测本地是否有mysql已存在的包

```shell
rpm -qa | grep mysql
```
#### 3.检测本地是否有mariadb已存在的包
```shell
rpm -qa | grep mariadb
```
#### 4.如果存在，则使用yum命令卸载

```shell
yum -y remove mariadb-libs-5.5.54-2.el7.x86_64
//或者
rpm -e --nodeps mariadb-libs-5.5.54-2.el7.x86_64
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQzZWQzMTMwNmQwNDI4MjAucG5n?x-oss-process=image/format,png)

#### 5.创建一个文件夹，上传jar包到/opt/software/mysql

```shell
mkdir /opt/module/mysql
```
#### 6.解压mysql jar包

```shell
tar -xvf mysql-5.7.19-1.el7.x86_64.rpm-bundle.tar -C /opt/module/mysql
```
#### 7.安装mysql的 server、client、common、libs、lib-compat

```shell
rpm -ivh --nodeps mysql-community-server-5.7.19-1.el7.x86_64.rpm
rpm -ivh --nodeps mysql-community-client-5.7.19-1.el7.x86_64.rpm
rpm -ivh mysql-community-common-5.7.19-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-5.7.19-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-compat-5.7.19-1.el7.x86_64.rpm
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdmMWEwYjdiOGViNDczNTEucG5n?x-oss-process=image/format,png)

#### 8.查看mysql的服务是否启动

```shell
systemctl status mysqld
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgxZWI5MTVmOWZkN2EyODIucG5n?x-oss-process=image/format,png)
#### 9.启动mysql的服务
```shell
systemctl start mysqld
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWMzNzBiMGY1ZjY2Y2EyZDQucG5n?x-oss-process=image/format,png)

#### 10.再次检查mysql的服务是否启动
```shell
systemctl status mysqld
```

#### 11.查看默认生成的密码
```shell
cat /var/log/mysqld.log | grep password
```
![查看默认生成的密码](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZmOWI4YjNhODYzMDI1ZjkucG5n?x-oss-process=image/format,png)

#### 12.登录mysql服务
```shell
mysql -uroot -p’然后粘贴上密码’
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVhYjEyMGYxODI4ZGQwYTYucG5n?x-oss-process=image/format,png)

#### 13.修改mysql密码规则
|强度|规则|
|:-:|:-:|
|0 or LOW|长度|
|1 or MEDIUM|长度、大小写、数字、特殊字符|
|2 or STRONG|长度、大小写、数字、特殊字符、词典|


注：以下修改是临时修改
###### (a).	密码强度检查等级，0/LOW、1/MEDIUM、2/STRONG
```shell
set global validate_password_policy=0;
```

###### (b).密码至少要包含的小写字母个数和大写字母个数
```shell
set global validate_password_mixed_case_count=0;
```

###### (c ).密码至少要包含的数字个数 
```shell
set global validate_password_number_count=3;
```
 
###### (d).密码至少要包含的特殊字符数
```shell
set global validate_password_special_char_count=0;
```
###### (e).密码最小长度，参数默认为8，
```shell
set global validate_password_length=3;
```

&nbsp;&nbsp;&nbsp;&nbsp;它有最小值的限制，最小值为：validate_password_number_count + 密码至少要包含的数字个数validate_password_special_char_count +特殊字符 (2 * validate_password_mixed_case_count)至少要包含的小写字母个数和大写字母个数

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk3NTE0ZjAyODM4ZDJmZTcucG5n?x-oss-process=image/format,png)

#### 14.修改密码：
```shell
SET PASSWORD FOR 'root'@'localhost'=PASSWORD('000000');
```

(这两步可以跳过)
```shell
use mysql;
SHOW VARIABLES LIKE 'validate_password%';
```

| Variable_name                        | Value |
|:-:|:-:|
| validate_password_dictionary_file    |       |
| validate_password_length             | 3     |
| validate_password_mixed_case_count   | 0     |
| validate_password_number_count       | 3     |
| validate_password_policy             | LOW   |
| validate_password_special_char_count | 0     |

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA1NDI2OGMyODczOTQ5MTcucG5n?x-oss-process=image/format,png)


#### 15.修改远程登录权限
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM0MDVjYjA1MDRkMDE4NjkucG5n?x-oss-process=image/format,png)

如上图所示：这个是可以成功远程链接得配置
大家默认的%的位置是localhost,即意味着只能本机访问

##### (1)查询当前user表内root的登录权限：
```	shell
select host,user from mysql.user;
```
##### (2)修改权限为所有%：
```shell
update mysql.user set host = '%' where user = 'root';
```
##### (3)刷新缓存：

```shell
flush privileges;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI3OWJhZDUxNjljNDdkOTQucG5n?x-oss-process=image/format,png)
