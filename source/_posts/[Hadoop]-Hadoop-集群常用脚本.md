---
title: '[Hadoop]-Hadoop-集群常用脚本'
date: 2023-01-15 12:49:00
tags: 
- Hadoop
categories: Hadoop
---

### 1.Hadoop集群群启停脚本

(1)新建脚本文件
```shell
cd /root/bin

vim myhadoop.sh
```

(2)脚本内容
```shell
#!/bin/bash

if [ $# -lt 1 ]
then
    echo "No Args Input..."
    exit ;
fi

case $1 in
"start")
        echo " =================== 启动 hadoop集群 ==================="

        echo " --------------- 启动 hdfs ---------------"
        ssh bigdata101 "/opt/module/hadoop-3.1.3/sbin/start-dfs.sh"
        echo " --------------- 启动 yarn ---------------"
        ssh bigdata102 "/opt/module/hadoop-3.1.3/sbin/start-yarn.sh"
        echo " --------------- 启动 historyserver ---------------"
        ssh bigdata101 "/opt/module/hadoop-3.1.3/bin/mapred --daemon start historyserver"
;;
"stop")
        echo " =================== 关闭 hadoop集群 ==================="

        echo " --------------- 关闭 historyserver ---------------"
        ssh bigdata101 "/opt/module/hadoop-3.1.3/bin/mapred --daemon stop historyserver"
        echo " --------------- 关闭 yarn ---------------"
        ssh bigdata102 "/opt/module/hadoop-3.1.3/sbin/stop-yarn.sh"
        echo " --------------- 关闭 hdfs ---------------"
        ssh bigdata101 "/opt/module/hadoop-3.1.3/sbin/stop-dfs.sh"
;;
*)
    echo "Input Args Error..."
;;
esac
```
(3)将脚本赋权限

```shell
chmod 777 myhadoop.sh
```

### 2.查看多服务器Java进程脚本

(1)新建脚本文件

```shell
cd /root/bin

vim jpsall
```

(2)脚本内容
```shell
#!/bin/bash

for host in bigdata101 bigdata102 bigdata103
do
        echo =============== $host ===============
        ssh $host jps 
done
```

(3)赋权限
```shell
chmod 777 jpsall
```
