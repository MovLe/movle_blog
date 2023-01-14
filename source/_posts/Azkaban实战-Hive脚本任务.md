---
title: 'Azkaban实战-Hive脚本任务'
date: 2019-06-02 17:00:00
tags: 
- Azkaban
- Azkaban实战
- Hive
categories: Azkaban
---

#### 1.创建job描述文件和hive脚本

##### (1)Hive脚本：student.sql
```shell
vim student.sql
```
添加内容：
```shell
use default;
drop table student;
create table student(id int, name string)
row format delimited fields terminated by '\t';
load data local inpath '/opt/module/datas/student.txt' into table student;
insert overwrite local directory '/opt/module/datas/student'
row format delimited fields terminated by '\t'
select * from student;
```


![student.sql](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM5ODhlNTgwYzdmNWU4OGEucG5n?x-oss-process=image/format,png)

##### (2)Job描述文件：hive.job
```shell
vi hive.job
```
添加内容：
```shell
#hive job
type=command
command=/opt/module/hive-1.2.1/bin/hive -f /opt/module/azkaban/jobs/student.sql
```
#### 2.将所有job资源文件打到一个zip包中

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRkY2Y4YWQ1NGMyZjdiZGQucG5n?x-oss-process=image/format,png)

#### 3.在azkaban的web管理界面创建工程并上传zip包

![创建](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTcxZGVkMGRkOTM2ODc3ZTIucG5n?x-oss-process=image/format,png)

![上传](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJiZjRiMzUxMjUwMmU5Y2QucG5n?x-oss-process=image/format,png)

#### 4.启动job：
![执行](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWUyN2FkMzAyMDM4NDJmZDAucG5n?x-oss-process=image/format,png)

![执行成功](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQzZDdjNzYwODM2YWUzM2MucG5n?x-oss-process=image/format,png)

#### 5.查看结果
```shell
cat /opt/module/datas/student/000000_0
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFiNzNiNzAyMzUxMTFhNmUucG5n?x-oss-process=image/format,png)
![数据源](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE3ZWQ5ZjIxY2FlOWViZjUucG5n?x-oss-process=image/format,png)