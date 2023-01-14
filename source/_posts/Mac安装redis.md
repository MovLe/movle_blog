---
title: 'Mac安装Redis'
date: 2020-10-15 16:08:00
tags: 
- Mac
- Redis
- 安装部署
categories: Redis
---

#### 1.查询可安装的版本
```
brew search redis
```

#### 2.安装redis
```
brew install redis

brew install redis@3.2
```

#### 3.卸载redis
```
brew uninstall redis
```

#### 4.启动redis服务
```
brew services start redis
```
#### 5.关闭redis服务
```
brew services stop redis
```
#### 6.重启redis服务
```
brew service restart redis
```
#### 7.通过bin目录启动
```
cd /usr/local/Cellar/redis/6.0.8/bin

./redis-server
```
#### 8.配置开机启动redis
```
ln -sfv /usr/local/opt/redis/*plist ~/Library/LaunchAgents
```