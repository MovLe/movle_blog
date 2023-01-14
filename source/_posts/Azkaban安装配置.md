---
title: 'Azkaban安装配置'
date: 2019-06-02 12:00:00
tags: 
- Azkaban
- 安装部署
categories: Azkaban
---

### 一.安装前准备
#### 1.将Azkaban Web服务器、Azkaban执行服务器、Azkaban的sql执行脚本及MySQL安装包拷贝到bigdata111虚拟机/opt/software目录下
```shell
1) azkaban-web-server-2.5.0.tar.gz
2) azkaban-executor-server-2.5.0.tar.gz
3) azkaban-sql-script-2.5.0.tar.gz
4) mysql-libs.zip
```
#### 2.选择Mysql作为Azkaban数据库，因为Azkaban建立了一些Mysql连接增强功能，以方便Azkaban设置，并增强服务可靠性。


### 二. 安装Azkaban
#### 1.在/opt/module/目录下创建azkaban目录
```shell
mkdir azkaban
```
#### 2.解压azkaban-web-server-2.5.0.tar.gz、azkaban-executor-server-2.5.0.tar.gz、azkaban-sql-script-2.5.0.tar.gz到/opt/module/azkaban目录下
```shell
tar -zxvf azkaban-web-server-2.5.0.tar.gz -C /opt/module/azkaban/

tar -zxvf azkaban-executor-server-2.5.0.tar.gz -C /opt/module/azkaban/

tar -zxvf azkaban-sql-script-2.5.0.tar.gz -C /opt/module/azkaban/
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWUzMmI3YjkwMDlmN2RkMmYucG5n?x-oss-process=image/format,png)

#### 3.对解压后的文件重新命名
```shell
cd /opt/module/azkaban

mv azkaban-web-2.5.0/ server

mv azkaban-executor-2.5.0/ executor
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU2YzBhMmY2OGI1ODc2ZDEucG5n?x-oss-process=image/format,png)

#### 4.azkaban脚本导入
&nbsp;&nbsp;&nbsp;&nbsp;进入mysql，创建azkaban数据库，并将解压的脚本导入到azkaban数据库。
```shell
$ mysql -uroot -p000000
mysql> create database azkaban;
mysql> use azkaban;
mysql> source /opt/module/azkaban/azkaban-2.5.0/create-all-sql-2.5.0.sql
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc0ODVjNzIyNzBjZDFlMzMucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWMzNTRkNjFmZTdmZDZhODMucG5n?x-oss-process=image/format,png)

注：source后跟.sql文件，用于批量处理.sql文件中的sql语句。

### 三. 生成密钥库
Keytool是java数据证书的管理工具，使用户能够管理自己的公/私钥对及相关证书。
```shell
-keystore    指定密钥库的名称及位置(产生的各类信息将不在.keystore文件中)
-genkey      在用户主目录中创建一个默认文件".keystore" 
-alias  对我们生成的.keystore 进行指认别名；如果没有默认是mykey
-keyalg  指定密钥的算法 RSA/DSA 默认是DSA
```
#### 1.生成 keystore的密码及相应信息的密钥库
```shell
cd /opt/module/azkaban
```
```shell
keytool -keystore keystore -alias jetty -genkey -keyalg RSA
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTEzZDdhZjJjYzYxOTZmNGIucG5n?x-oss-process=image/format,png)

注意：
* 密钥库的密码至少必须6个字符，可以是纯数字或者字母或者数字和字母的组合等等
* 密钥库的密码最好和<jetty> 的密钥相同，方便记忆

#### 2.将keystore 拷贝到 azkaban web服务器根目录中
```shell
mv keystore /opt/module/azkaban/server/
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWVlMzZiM2E1MTU4ZTg2M2UucG5n?x-oss-process=image/format,png)

### 四. 时间同步配置
先配置好服务器节点上的时区
#### 1.如果在/usr/share/zoneinfo/这个目录下不存在时区配置文件Asia/Shanghai，就要用 tzselect 生成。

```shell
[root@hadoop1 azkaban]$ tzselect
Please identify a location so that time zone rules can be set correctly.
Please select a continent or ocean.
 1) Africa
 2) Americas
 3) Antarctica
 4) Arctic Ocean
 5) Asia
 6) Atlantic Ocean
 7) Australia
 8) Europe
 9) Indian Ocean
10) Pacific Ocean
11) none - I want to specify the time zone using the Posix TZ format.
#? 5
Please select a country.
 1) Afghanistan           18) Israel                35) Palestine
 2) Armenia               19) Japan                 36) Philippines
 3) Azerbaijan            20) Jordan                37) Qatar
 4) Bahrain               21) Kazakhstan            38) Russia
 5) Bangladesh            22) Korea (North)         39) Saudi Arabia
 6) Bhutan                23) Korea (South)         40) Singapore
 7) Brunei                24) Kuwait                41) Sri Lanka
 8) Cambodia              25) Kyrgyzstan            42) Syria
 9) China                 26) Laos                  43) Taiwan
10) Cyprus                27) Lebanon               44) Tajikistan
11) East Timor            28) Macau                 45) Thailand
12) Georgia               29) Malaysia              46) Turkmenistan
13) Hong Kong             30) Mongolia              47) United Arab Emirates
14) India                 31) Myanmar (Burma)       48) Uzbekistan
15) Indonesia             32) Nepal                 49) Vietnam
16) Iran                  33) Oman                  50) Yemen
17) Iraq                  34) Pakistan
#? 9
Please select one of the following time zone regions.
1) Beijing Time
2) Xinjiang Time
#? 1

The following information has been given:

        China
        Beijing Time

Therefore TZ='Asia/Shanghai' will be used.
Local time is now:      Thu Oct 18 16:24:23 CST 2018.
Universal Time is now:  Thu Oct 18 08:24:23 UTC 2018.
Is the above information OK?
1) Yes
2) No
#? 1

You can make this change permanent for yourself by appending the line
        TZ='Asia/Shanghai'; export TZ
to the file '.profile' in your home directory; then log out and log in again.

Here is that TZ value again, this time on standard output so that you
can use the /usr/bin/tzselect command in shell scripts:
Asia/Shanghai
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdjNDE0MGRjNWNhZjg0NGUucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc4Zjc3ODdiODU1YmZkZWUucG5n?x-oss-process=image/format,png)

#### 2.拷贝该时区文件，覆盖系统本地时区配置
```shell
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime  
```
#### 3.集群时间同步（同时发给三个窗口）
```shell
sudo date -s '2019-12-06 08:39:30'
```

### 五.配置文件
#### 1.Web服务器配置
##### (1).进入azkaban web服务器安装目录 conf目录，打开azkaban.properties文件
```shell
cd /opt/module/azkaban/server/conf

vi azkaban.properties
```
##### (2).按照如下配置修改azkaban.properties文件

```shell
#默认web server存放web文件的目录
web.resource.dir=/opt/module/azkaban/server/web/

#默认时区,已改为亚洲/上海 默认为美国
default.timezone.id=Asia/Shanghai

#用户权限管理默认类（绝对路径）
user.manager.xml.file=/opt/module/azkaban/server/conf/azkaban-users.xml

#Loader for projects
#global配置文件所在位置（绝对路径）
executor.global.properties=/opt/module/azkaban/executor/conf/global.properties


#数据库连接IP
mysql.host=hadoop1
#数据库实例名
mysql.database=azkaban
#数据库用户名
mysql.user=root
#数据库密码
mysql.password=000000


#SSL文件名（绝对路径）
jetty.keystore=/opt/module/azkaban/server/keystore
#SSL文件密码
jetty.password=000000
#Jetty主密码与keystore文件相同
jetty.keypassword=000000
#SSL文件名（绝对路径）
jetty.truststore=/opt/module/azkaban/server/keystore
#SSL文件密码
jetty.trustpassword=000000
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ5MjY4Y2ZlZWNlZTg0ZjUucG5n?x-oss-process=image/format,png)

 ##### (3).web服务器用户配置
&nbsp;&nbsp;&nbsp;&nbsp;在azkaban web服务器安装目录 conf目录，按照如下配置修改azkaban-users.xml 文件，增加管理员用户。

```shell
cd /opt/module/azkaban/server/conf

vi azkaban-users.xml
```
修改内容为：
```xml
<azkaban-users>
	<user username="azkaban" password="azkaban" roles="admin" groups="azkaban" />
	<user username="metrics" password="metrics" roles="metrics"/>
	<user username="admin" password="admin" roles="admin,metrics" />
	<role name="admin" permissions="ADMIN" />
	<role name="metrics" permissions="METRICS"/>
</azkaban-users>
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQzMWRiOTI0ODU2ODFlYTYucG5n?x-oss-process=image/format,png)

#### 2.执行服务器配置
##### (1).进入执行服务器安装目录conf，打开azkaban.properties
```shell
cd /opt/module/azkaban/executor/conf

vi azkaban.properties
```
##### (2).按照如下配置修改azkaban.properties文件。

```shell
#时区
default.timezone.id=Asia/Shanghai

#Loader for projects
executor.global.properties=/opt/module/azkaban/executor/conf/global.properties

mysql.host=hadoop1
mysql.database=azkaban
mysql.user=root
mysql.password=000000
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc2ZTk5MGRlNmI0NDI0ZmQucG5n?x-oss-process=image/format,png)
### 六.启动executor服务器
在executor服务器目录下执行启动命令
```shell
cd /opt/module/azkaban/executor

bin/azkaban-executor-start.sh
```
### 七.启动web服务器
在azkaban web服务器目录下执行启动命令
```shell
cd /opt/module/azkaban/server

bin/azkaban-web-start.sh
```
注意：
先执行executor，再执行web，避免Web Server会因为找不到执行器启动失败。
jps查看进程
```shell
jps
```


![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ5MWM1MDU2NWI2ZDAwYWEucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;启动完成后，在浏览器(建议使用谷歌浏览器)中输入https://服务器IP地址:8443，即可访问azkaban服务了。
&nbsp;&nbsp;&nbsp;&nbsp;在登录中输入刚才在azkaban-users.xml文件中新添加的户用名及密码,即admin和admin，点击 login。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTYwZTVhZTE3NGQxODg4YjYucG5n?x-oss-process=image/format,png)

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ0ZjBiNDYzZDk5NmFhOWIucG5n?x-oss-process=image/format,png)