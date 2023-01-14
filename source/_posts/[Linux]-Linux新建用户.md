---
title: '[Linux]-Linux 新建用户'
date: 2019-05-19 09:13:00
tags: Linux
categories: Linux
---

###### 1.Linux中新建用户命令：

举例：创建一个名字叫 demouser的用户

使用root用户操作如下命令：
```
//-----------创建用户
useradd demouser	

//-----------为用户设置密码
passwd demouser	

//-----------为用户赋予sudo权限
vim /etc/sudoers  
//添加
demouser    ALL=(ALL)       ALL
```
###### 2.修改文件夹及其子文件夹属主命令

```
chown -R demouser ./elasticsearch-6.1.1/
```

