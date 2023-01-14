---
title: '[Spark]-Spark实战-用Scala编写WordCount程序'
date: 2020-03-12 13:00:00
tags: 
- Spark
- Spark实战
categories: Spark
---

### 一.添加pom依赖：

pom.xml
```xml
<!-- https://mvnrepository.com/artifact/org.apache.spark/spark-core -->
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-core_2.11</artifactId>
            <version>2.1.0</version>
        </dependency>

    <build>
        <plugins>

            <plugin>
                <groupId>net.alchim31.maven</groupId>
                <artifactId>scala-maven-plugin</artifactId>
                <version>3.2.2</version>
                <executions>
                    <execution>
                        <id>compile-scala</id>
                        <phase>compile</phase>
                        <goals>
                            <goal>add-source</goal>
                            <goal>compile</goal>
                        </goals>
                    </execution>
                    <execution>
                        <id>test-compile-scala</id>
                        <phase>test-compile</phase>
                        <goals>
                            <goal>add-source</goal>
                            <goal>testCompile</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <scalaVersion>2.11.4</scalaVersion>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

### 二.编写代码：
#### 1.本地模式：

WordCount.scala
```java
package WordCoutScala

import org.apache.spark.SparkContext
import org.apache.spark.SparkConf

object WordCount {

  //定义主方法
  def main(args: Array[String]): Unit = {

    //创建SparkConf对象
    //如果Master是local，表示运行在本地模式上，即可以在开发工具中直接运行
    //如果要提交到集群中运行，不需要设置Master
    //集群模式
    //val conf = new SparkConf().setAppName("My Scala Word Count")

    //本地模式
    val conf = new SparkConf().setAppName("My Scala Word Count").setMaster("local")

    //创建SparkContext对象
    val sc = new SparkContext(conf)

        val result = sc.textFile("hdfs://192.168.1.120:9000/TestFile/test_WordCount.txt")
                        .flatMap(_.split(" "))
                        .map((_,1))
                        .reduceByKey(_+_)

         result.foreach(println)

    //集群模式
//    val result = sc.textFile(args(0))
//      .flatMap(_.split(" "))
//      .map((_,1))
//      .reduceByKey(_+_)
//      .saveAsTextFile(args(1))

    sc.stop()
  }
}
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU3Nzk3MjRkYzUwZjA0MmIucG5n?x-oss-process=image/format,png)


#### 2.集群模式：

##### (1)编写WordCount.scala
```java
import org.apache.spark.SparkContext
import org.apache.spark.SparkConf

object WordCount {
  
  //定义主方法
  def main(args: Array[String]): Unit = {
    
    //创建SparkConf对象
    //如果Master是local，表示运行在本地模式上，即可以在开发工具中直接运行
    //如果要提交到集群中运行，不需要设置Master
    //集群模式
    val conf = new SparkConf().setAppName("My Scala Word Count")
    
    //本地模式
    //val conf = new SparkConf().setAppName("My Scala Word Count").setMaster("local")
    
    //创建SparkContext对象
    val sc = new SparkContext(conf)
    
//    val result = sc.textFile("hdfs://192.168.1.120:9000/TestFile/test_WordCount.txt")  
//                    .flatMap(_.split(" "))
//                    .map((_,1))
//                    .reduceByKey(_+_)
//                    
//     result.foreach(println)
    
    //集群模式
    val result = sc.textFile(args(0))  
                .flatMap(_.split(" "))
                .map((_,1)) 
                .reduceByKey(_+_)
                .saveAsTextFile(args(1))

    sc.stop()
  }
}
```

##### (2)打包
![打包](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZlYzg4YmQxMjY2ZjZjMTMucG5n?x-oss-process=image/format,png)


##### (3)上传到Spark节点：
![上传](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTIzMDY1YzNiMzc2MjI5MzEucG5n?x-oss-process=image/format,png)


##### (4)运行：
```shell
bin/spark-submit --master spark://hadoop:7077 --class WordCoutScala.WordCount /opt/TestFile/ScalaProject-1.0-SNAPSHOT.jar hdfs://hadoop:9000/TestFile/test_WordCount.txt hdfs://hadoop:9000/output/1209/demo1
```
![运行](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTYxMTQxNWZiMjdmZTBkYzAucG5n?x-oss-process=image/format,png)

##### (5)结果：
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWViNGU4YTY5NWVjOTc4ZjcucG5n?x-oss-process=image/format,png)

