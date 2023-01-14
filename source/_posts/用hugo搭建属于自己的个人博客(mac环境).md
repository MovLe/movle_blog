---
title: '用hugo搭建属于自己的个人博客(mac环境)'
date: 2019-05-28 15:00:00
tags: 
- Mac
- Hugo
categories: Mac
---


#### 1.安装git环境
安装成功后在终端输入git，会有以下显示：
![git安装成功](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTAxYmQ0NmExMDkyZmJmYjYucG5n?x-oss-process=image/format,png)


#### 2.安装Homebrew
网上教程有很多，也很简单,安装成功后，输入brew，会有以下显示：
![brew安装成功](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI1Y2RlZWZiODE0NTgxMmUucG5n?x-oss-process=image/format,png)


#### 3.安装hugo
终端内输入：
```shell 
brew install hugo
```
安装成功后输入：
```shell
hugo version
```
则会显示
![hugo安装成功](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRmMjJmZWY5MzQ2YzI4YWUucG5n?x-oss-process=image/format,png)


#### 4.新建自己的博客站点
终端输入：
```shell
hugo new site YOURBLOGNAME
```
YOURBLOGNAME是自己想要做的博客的名称
成功后会显示：

![新建站点](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNmMGZkYTc2MTc5OGFkN2QucG5n?x-oss-process=image/format,png)

#### 5.设置主题：
##### (1).进入[hugo主题网址](https://themes.gohugo.io/)
##### (2).选择自己喜欢的主题：

![选择自己喜欢的主题](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQyYWMzMjdkMGUzMTZjMDUucG5n?x-oss-process=image/format,png)

##### (3).进入主题详细界面，找到Getting Started，比如下图：

![进入主题详细界面](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZmMDkyNGM3YmQxMjY3YzgucG5n?x-oss-process=image/format,png)

##### (4).按照其指示安装此主题，比如
进入到自己所创建的博客目录：
```shell
cd YOURBLOG
```
输入git克隆命令，将此主题拷贝到本地
```shell
git clone https://github.com/vaga/hugo-theme-m10c.git themes/m10c
```
更改config.toml文件
```shell
theme="m10c"
```
m10c是下载的主题名
#### 6.在本地启动博客
在终端内输入命令：
```shell
hugo server -t m10c --buildDrafts
```
若终端显示：

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY0MTBjYjVmODczMDNmOWEucG5n?x-oss-process=image/format,png)

则证明此博客已经在本地启动成功，在浏览器地址栏输入对应的地址，如上面红线，即可进入该博客

![本地博客](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWYzYzI0MzNjZjY0MDUxOWQucG5n?x-oss-process=image/format,png)

#### 7.新建博客

在终端输入：
```shell
hugo new post/blog.md
```
即可创建一个post文件夹，并在此post文件夹下创建blog.md文件
注意，此时post文件夹位于自己所创建的博客站点下的context文件夹下面

#### 8.在此博客内写自己想写的内容并保存
注意：
title是该博客的名字

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZjMjY2MGY0MzE1NmEyMDcucG5n?x-oss-process=image/format,png)


#### 9.将博客部署到github远端
##### (1).首先在自己的GitHub上创建一个新的仓库

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFhODY1NDEyNmQwOTViYjYucG5n?x-oss-process=image/format,png)

注意：
Repository name必须是自己名称的全英文小写加上.github.io

##### (2).终端输入
```shell
hugo --theme=m10c baseUrl="https://yourgithubname.github.io/" --buildDrafts 
```
注意：yourgithubname就是自己的github名
成功后显示：


![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI0ZGEwN2VkZDFkOTMxNGMucG5n?x-oss-process=image/format,png)
此时，在YOURBLOG文件夹下面会生成一个public文件夹

##### (3).将public文件夹上传到github仓库，
进入YOURBLOG文件夹下面的public文件夹
终端依次输入：
```shell
git init
git add .
git commit -m"第一次提交博客"
git remote add origin https://github.com/yourgithub/yougithub.github.io.git 
git push -u origin master 
```
即可将本地博客搭建到远程github上
##### (4).在浏览器地址输入：
```shell
yourgithub.github.io
```
即可进入搭建到远程github的个人博客。

#### 10.非第一次将博客发布到远程仓库

##### (1)进入博客所在文件夹
终端输入：
```shell
hugo --theme=m10c baseUrl="https://yourgithubname.github.io/" --buildDrafts
```

##### (2)然后进入YOURBLOG文件夹下的public文件夹
终端输入：
```shell
git add .
git commit -m"提交的提示"
git push origin master
```
之后就可以将本地的博客上传到远程仓库

