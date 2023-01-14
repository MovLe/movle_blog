---
title: '[Hive]-Hive之函数'
date: 2019-07-15 20:00:00
tags: Hive
categories: Hive
---


### 一.系统自带的函数
#### 1.查看系统自带的函数
```shell
hive> show functions;
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThjNDgzZWU2Y2QwYmZhNGMucG5n?x-oss-process=image/format,png)

#### 2.显示自带的函数的用法
```shell
hive> desc function upper;
```
#### 3.详细显示自带的函数的用法
```shell
hive> desc function extended upper;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU5NTk5OTI2ZDE1OGFkMzIucG5n?x-oss-process=image/format,png)

### 二. 自定义函数
#### 1.Hive 自带了一些函数，比如：max/min等，但是数量有限，自己可以通过自定义UDF来方便的扩展。

#### 2.当Hive提供的内置函数无法满足你的业务处理需要时，此时就可以考虑使用用户自定义函数（UDF：user-defined function）。

#### 3.根据用户自定义函数类别分为以下三种：
##### (1)UDF(User-Defined-Function)
		一进一出
##### (2)UDAF(User-Defined Aggregation Function)
		聚集函数，多进一出
		类似于：count/max/min
##### (3)UDTF(User-Defined Table-Generating Functions)
		一进多出
		如lateral view explore()
#### 4.官方文档地址
https://cwiki.apache.org/confluence/display/Hive/HivePlugins
#### 5.编程步骤：
##### (1)继承org.apache.hadoop.hive.ql.UDF

##### (2)需要实现evaluate函数；evaluate函数支持重载；
##### (3)在hive的命令行窗口创建函数
###### (a)添加jar
```shell
add jar linux_jar_path
```
###### (b)创建function，
```shell
create [temporary] function [dbname.]function_name AS class_name;
```
##### (4)在hive的命令行窗口删除函数
```shell
Drop [temporary] function [if exists] [dbname.]function_name;
```
#### 6.注意事项
##### (1)UDF必须要有返回类型，可以返回null，但是返回类型不能为void；

### 三. 自定义UDF函数开发案例
#### 1.创建一个maven工程:
#### 2.将hive的jar包解压后，将apache-hive-1.2.1-bin\lib文件下的jar包都拷贝到java工程中。

#### 3.添加pomy依赖：

pom.xml
```xml
 <dependencies>
        <!-- https://mvnrepository.com/artifact/org.apache.hive/hive-metastore -->
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-metastore</artifactId>
            <version>2.3.0</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.apache.hive/hive-jdbc -->
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-jdbc</artifactId>
            <version>2.3.0</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.apache.hive/hive-exec -->
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>2.3.0</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.apache.hive/hive-common -->
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-common</artifactId>
            <version>2.3.0</version>
        </dependency>

    </dependencies>
```

#### 4.创建一个类
```java
package HiveUDF;
import org.apache.hadoop.hive.ql.exec.UDF;

public class Lower extends UDF {

	public String evaluate (final String s) {
		
		if (s == null) {
			return null;
		}
		
		return s.toString().toLowerCase();
	}
}
```

#### 5.打成jar包上传到服务器/opt/module/jars/Hive-1.0-SNAPSHOT.jar 

##### (1)打包：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZhNDEzZmY4YTBhZDRiN2UucG5n?x-oss-process=image/format,png)

##### (2)上传：

![上传](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFlNWU1YTE2YWIyZjJkMjQucG5n?x-oss-process=image/format,png)

#### 6.将jar包添加到hive的classpath
```shell
hive (default)> add jar /opt/module/jars/Hive-1.0-SNAPSHOT.jar ;
```
#### 7.创建临时函数与开发好的java class关联(全类名)
```shell
hive (default)> create temporary function udf_lower as "HiveUDF.Lower";
```
#### 8.即可在hql中使用自定义的函数strip 
```shell
hive (default)> select ename, udf_lower(ename) lowername from emp;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM5NjY1NmY5Yzc4MTJiODgucG5n?x-oss-process=image/format,png)