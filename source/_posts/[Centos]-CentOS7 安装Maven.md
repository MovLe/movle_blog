---
title: '[Centos]-CentOS7安装Maven'
date: 2020-02-15 13:35:00
tags: 
- Maven
- 安装部署
- CentOS
categories: Linux
---

###### 1.下载Maven安装包

[Maven下载地址](https://maven.apache.org/download.cgi)

###### 2.解压
```
tar -zxvf apache-maven-3.6.3-bin.tar.gz -C /opt/module/
```

![解压](https://upload-images.jianshu.io/upload_images/4391407-1f35788eb317223f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###### 3.改名
```
mv apache-maven-3.6.3/ maven
```

![](https://upload-images.jianshu.io/upload_images/4391407-afd58278c5895903.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###### 4.配置环境变量
```
vi /etc/profile
```
修改内容
```
export M2_HOME=/opt/module/maven
export PATH=$PATH:$JAVA_HOME/bin:$M2_HOME/bin
```
使环境变量生效
```
source /etc/profile
```
###### 5.验证
```
mvn -v
```

![验证](https://upload-images.jianshu.io/upload_images/4391407-4c4278066ed95905.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
