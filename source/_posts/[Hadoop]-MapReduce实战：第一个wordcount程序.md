---
title: '[Hadoop]-MapReduce实战：第一个wordcount程序'
date: 2019-01-12 11:00:00
tags: 
- Hadoop
- MapReduce
- Hadoop实战
categories: Hadoop
---

#### 1.运行环境：

* linux Hadoop集群
* IDEA
* jdk8

#### 2.在IDEA中创建项目
##### (1)创建项目，添加依赖：

pom.xml
```xml
<properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-common</artifactId>
            <version>2.8.4</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
            <version>2.8.4</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.8.4</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.16.10</version>
        </dependency>

        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.7</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/junit/junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.5.1</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

##### (2)map代码：
WordCountMapper.java

```java
package WordCount;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

import java.io.IOException;

public class WordCountMapper extends Mapper<LongWritable, Text,Text, IntWritable> {

    @Override
    protected void map(LongWritable key,Text value,Context context) throws IOException, InterruptedException {

        //1 将maptask传给我们的文本内容先转换成String
        String line = value.toString();

        // 2 根据空格将这一行切分成单词
        // new String[][I,wish,to,wish]
        String[] words = line.split(" ");

        // 3 将单词输出为<单词，1>
        for(String word:words){
            context.write(new Text(word),new IntWritable(1));
        }

    }
}

```

##### (3)reduce代码
WordCountReducer.java

```java
package WordCount;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

import java.io.IOException;

public class WordCountReducer extends Reducer<Text, IntWritable,Text,IntWritable> {

    @Override
    protected void reduce(Text key,Iterable<IntWritable> values,Context context) throws IOException, InterruptedException {
        int count =0;

        // 1 汇总各个key的个数
        for(IntWritable value:values){
            count+= value.get();
        }

        // 2输出该key的总次数
        context.write(key,new IntWritable(count));
    }
}
```

##### (4)partitioner代码
WordCoutPartitioner.java
```java
package WordCount;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Partitioner;

public class WordCountPartitioner extends Partitioner<Text, IntWritable> {
    @Override
    public int getPartition(Text key, IntWritable value, int numPartitions) {

      // 1.获取单词key
      String word = key.toString();
      // 2.计算单词长度
      int length=word.getLength();
      // 3.按照规则自定义分区
      if(length%2==0){
          return 1;
      }else{
          return 0;
      }
   }
}
```

##### (5)driver代码：
WordCountDriver.java

```java
package WordCount;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;

import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

import java.io.IOException;

/**
 * @ClassName WordCountDriver
 * @MethodDesc: TODO WordCountDriver功能介绍
 * @Author Movle
 * @Date 5/6/20 10:07 上午
 * @Version 1.0
 * @Email movle_xjk@foxmail.com
 **/


public class WordCountDriver {

    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
        //args = new String[]{"/Users/macbook/TestInfo/a.txt","/Users/macbook/TestInfo/WC"};

        //1.获取配置信息，或job对象实例
        Configuration conf = new Configuration();

        Job job = Job.getInstance(conf);

        job.setJarByClass(WordCountDriver.class);
        job.setMapperClass(WordCountMapper.class);
        job.setReducerClass(WordCountReducer.class);

        //map输出的k,v,reduce的输入k,v
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(IntWritable.class);

        //reduce输出的k,v
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        //设置分区，也要设置运算分区的reduce 的task数,因为分区数是2，所以这里设置为2
        job.setPartitionerClass(WordCountPartitioner.class);
        job.setNumReduceTasks(2);

        //指定job的输入原始文件所在目录，以及输出目录
        FileInputFormat.setInputPaths(job,new Path(args[0]));
        FileOutputFormat.setOutputPath(job,new Path(args[1]));

        //提交job，waitForCompletion包含job.submit
        job.waitForCompletion(true);

    }
}
```

#### 3.运行：
##### (1).打包：

##### (2).上传到hadoop集群：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020050611443223.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FmbHlpbmdjYXQ1MjA=,size_16,color_FFFFFF,t_70)

##### (3)执行

注意：下面的路径是hdfs路径


```shell
hadoop jar Hadoop-1.0-SNAPSHOT.jar WordCount.WordCountDriver /a.txt /out
```
![运行](https://img-blog.csdnimg.cn/20200506115633928.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FmbHlpbmdjYXQ1MjA=,size_16,color_FFFFFF,t_70#pic_center)
##### (4)结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200506115757263.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FmbHlpbmdjYXQ1MjA=,size_16,color_FFFFFF,t_70)
![结果](https://img-blog.csdnimg.cn/20200506115943489.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FmbHlpbmdjYXQ1MjA=,size_16,color_FFFFFF,t_70)

