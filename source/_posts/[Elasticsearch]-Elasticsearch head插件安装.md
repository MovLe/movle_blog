---
title: '[Elasticsearch]-Elasticsearch head插件安装'
date: 2020-01-12 14:00:00
tags: 
- Elasticsearch
- 安装部署
categories: Elasticsearch
---

#### 1.下载插件：
* https://github.com/mobz/elasticsearch-head

#### 2.下载nodejs：
* nodejs官网下载安装包：https://nodejs.org/dist/
* node-v6.9.2-linux-x64.tar.xz

#### 3.安装nodejs：
##### (1)解压
##### (2)配置环境变量

```shell
export NODE_HOME=/usr/local/node-v6.9.2-linux-x64

export PATH=$PATH:$NODE_HOME/bin
```
##### (3)查看node和npm版本：

```shell
node -v

npm -v
```
#### 4.解压head插件到/opt/module目录下：

```shell
unzip elasticsearch-head-master.zip
```
#### 5.查看当前head插件目录下有无node_modules/grunt目录：
* 如果没有，执行命令创建：

```shell
npm install grunt --save --registry=https://registry.npm.taobao.org
```
#### 6.安装head插件：

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

#### 7.安装grunt

```shell
npm install -g grunt-cli --registry=https://registry.npm.taobao.org
```
#### 8.编辑Gruntfile.js

```shell
vim Gruntfile.js
```
文件93行添加：

```shell
hostname:'0.0.0.0'
```
#### 9.检查head根目录下是否存在base文件夹
* 没有：将 _site下的base文件夹及其内容复制到head根目录下

```shell
mkdir base

cp base/* ../base/
```

#### 10.启动grunt server：

```shell
grunt server -d
```
#### 11.如果提示grunt的模块没有安装：

```txt
Local Npm module “grunt-contrib-clean” not found. Is it installed? 

Local Npm module “grunt-contrib-concat” not found. Is it installed? 

Local Npm module “grunt-contrib-watch” not found. Is it installed? 

Local Npm module “grunt-contrib-connect” not found. Is it installed? 

Local Npm module “grunt-contrib-copy” not found. Is it installed? 

Local Npm module “grunt-contrib-jasmine” not found. Is it installed? 
```

执行命令：

```shell
npm install grunt-contrib-clean -registry=https://registry.npm.taobao.org

npm install grunt-contrib-concat -registry=https://registry.npm.taobao.org

npm install grunt-contrib-watch -registry=https://registry.npm.taobao.org 

npm install grunt-contrib-connect -registry=https://registry.npm.taobao.org

npm install grunt-contrib-copy -registry=https://registry.npm.taobao.org 

npm install grunt-contrib-jasmine -registry=https://registry.npm.taobao.org
```
* **最后一个模块可能安装不成功，但是不影响使用。**

#### 12.浏览器访问head插件：http://192.168.109.133:9100