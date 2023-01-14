---
title: 'Mac-idea配置github 并上传项目'
date: 2019-05-20 15:00:00
tags: 
- Mac
- Git
- IDEA
categories: Git
---


#### 1.前提：
* 在github中已有账号
* 在本地环境已经安装过git

#### 2.在IDEA中设置git
在Perferences -> Version Control -> Git中配置git，选择git安装的目录，点击测试

![配置git](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVhYmU3ZjYzYTYxZThlOTQucG5n?x-oss-process=image/format,png)

#### 3.在IDEA中设置github
同理在Perferences -> Version Control -> Github中，输入github的账号密码，登陆

![登陆github](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk1YzVmZDA0ZjY0OTE4OTMucG5n?x-oss-process=image/format,png)

#### 4.创建本地仓库

##### (1)在VSC -> Import into Version Control -> Create Git Repository中创建本地仓库

![创建本地仓库](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ1M2Y4YzMyMGM5Y2ZjYzIucG5n?x-oss-process=image/format,png)

##### (2)选择项目所在目录，点击open，此时项目文件会显示为红色

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTEwNDlkYzgwNmVjN2Y0ZDkucG5n?x-oss-process=image/format,png)

##### (3)选择项目，右键，选择git，选择Add，此时项目文件会显示为绿色

![操作](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBjZjExZjQzNTc5NzljMjcucG5n?x-oss-process=image/format,png)

![绿色](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFjZjMxYzE1MTZiMTMyMDgucG5n?x-oss-process=image/format,png)

#### 5.添加.gitignore文件

选择需要ignore的文件，进行添加操作

![操作](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ3ZDJjMTMwNmJkODY5YTEucG5n?x-oss-process=image/format,png)

![.gitignore](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI4NGFmYmU1ODkzMmIwMjEucG5n?x-oss-process=image/format,png)

#### 6.提交

![提交](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZhYmYxZWY0YzQ3YjQwNjQucG5n?x-oss-process=image/format,png)

![提交2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY1ZmJhOTQ2ZTFlY2FmNWIucG5n?x-oss-process=image/format,png)


#### 7.上传项目到github
##### (1)在VCS -> Import into Version Control ->Share Project on Github里操作
 
![上传到github](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZmOWM3ZjNmYjZjNTk4MWMucG5n?x-oss-process=image/format,png)

![点击share](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTkxZmY1OWNhZjYwNmE5ODkucG5n?x-oss-process=image/format,png)

#### 8.上传github成功

![上传成功](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM5Mzc3NjNlMDUwMTg4MWUucG5n?x-oss-process=image/format,png)

