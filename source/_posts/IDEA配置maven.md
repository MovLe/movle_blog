---
title: 'Mac-IDEA配置Maven'
date: 2019-05-25 15:00:00
tags: 
- Mac
- Maven
- IDEA
categories: Maven
---

#### 1.下载
网址：http://maven.apache.org/download.cgi
![下载maven](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU4Zjc2NGMzZWNhNjFiNzYucG5n?x-oss-process=image/format,png)

#### 2.解压到此文件夹

```shell
/Users/macbook/Documents/maven/apache-maven-3.6.2
```
![解压](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE5ZWJjN2ZiZGEzZDRlMDUucG5n?x-oss-process=image/format,png)

#### 3.配置环境变量：
##### (1)编辑.bash_profile文件

```shell
vim ~/.bash_profile
```
##### (2)添加如下内容：

```shell
export M2_HOME=/Users/macbook/Documents/maven/apache-maven-3.6.2
export PATH=$PATH:$M2_HOME/bin
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFiZDhmYzBkOTQ3ODQwOWYucG5n?x-oss-process=image/format,png)

##### (3)使环境变量生效：

```shell
source ~/.bash_profile
```

##### (4)验证：

```shell
mvn -v
```
![验证](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTllMjM2MjgyODhkYWViNGEucG5n?x-oss-process=image/format,png)

#### 4.idea相关配置：
##### (1)配置Maven的setting.xml文件

```shell
vi /Users/macbook/Documents/maven/apache-maven-3.6.2/conf/settings.xml
```

修改内容为：

###### (a)本地仓库位置：
```xml
<localRepository>/Users/macbook/Documents/maven/apache-maven-3.6.2/repository </localRepository>
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThhMjczYjE2ZmU5OTYwOWEucG5n?x-oss-process=image/format,png)

###### (b)国内镜像
```xml
<mirrors>
<mirror>
<id>ali maven</id>
<name>aliyun maven</name>
<url>https://maven.aliyun.com/repository/public/</url>
<mirrorOf>central</mirrorOf> 
</mirror>
</mirrors> 
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBkZmJlMWQ4ZWU4Mjk0Y2EucG5n?x-oss-process=image/format,png)


##### (2)idea配置
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFjMGE3MzhiMDlmYjFkNDgucG5n?x-oss-process=image/format,png)


