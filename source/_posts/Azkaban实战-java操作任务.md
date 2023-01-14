---
title: 'Azkaban实战-java操作任务'
date: 2019-06-02 15:00:00
tags: 
- Azkaban
- Azkaban实战
categories: Azkaban
---

使用Azkaban调度java程序

#### 1.编写java程序
```java
import java.io.FileOutputStream;
import java.io.IOException;
public class AzkabanTest {

      public void run() throws IOException {

      // 根据需求编写具体代码

          FileOutputStream fos = new FileOutputStream("/opt/module/azkaban/output.txt");

          fos.write("this is a java progress".getBytes());

          fos.close();

      }

      public static void main(String[] args) throws IOException {

          AzkabanTest azkabanTest = new AzkabanTest();

          azkabanTest.run();
      }
}
```

#### 2.将java程序打成jar包，创建lib目录，将jar放入lib内

```shell
mkdir lib

cd lib/

ll
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ2Yzk4MjMzNGZjMTQ1YzQucG5n?x-oss-process=image/format,png)

#### 3.编写job文件
```shell
vi azkabanJava.job
```
添加内容：

```shell
#azkabanJava.job
type=javaprocess
java.class=AzkabanTest
classpath=/opt/module/azkaban/lib/*
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY5OWVkMjJjMzU0MjkxMTkucG5n?x-oss-process=image/format,png)

java.class：全类名

#### 4.将job文件打成zip包
```shell
zip azkabanJava.zip azkabanJava.job 
```
 
#### 5.通过azkaban的web管理平台创建project并上传job压缩包，启动执行该job

![创建项目](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTliNjQxOTI5ZjNiOTQ4NTAucG5n?x-oss-process=image/format,png)

![上传](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU2MDY0YzVhZjNkNTVkZTUucG5n?x-oss-process=image/format,png)

![执行](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTliOTczNTUyY2Q3Mjg1ZGMucG5n?x-oss-process=image/format,png)

#### 6.结果：
```shell
cat /opt/module/azkaban/output.txt
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY4OGE1YjQwN2RlY2JiYzEucG5n?x-oss-process=image/format,png)
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQwYmVmNjdjOTdmZWEzODQucG5n?x-oss-process=image/format,png)