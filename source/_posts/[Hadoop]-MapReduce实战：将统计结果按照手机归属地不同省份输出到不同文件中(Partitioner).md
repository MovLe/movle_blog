---
title: '[Hadoop]-MapReduce实战：将统计结果按照手机归属地不同省份输出到不同文件中(Partitioner)'
date: 2019-01-14 15:00:00
tags: 
- Hadoop
- MapReduce
- Hadoop实战
categories: Hadoop
---

#### 1.需求：
* 将上次实战(统计手机号耗费的总上行流量和下行流量)的统计结果按照手机归属地不同省份输出到不同文件中（分区）

#### 2.分析：
(1)Mapreduce中会将map输出的kv对，按照相同key分组，然后分发给不同的reducetask。默认的分发规则为：根据key的hashcode%reducetask数来分发

(2)如果要按照我们自己的需求进行分组，则需要改写数据分发（分组）组件Partitioner

&nbsp;&nbsp;&nbsp;&nbsp;自定义一个CustomPartitioner继承抽象类：Partitioner
(3)在job驱动中，设置自定义partitioner：

#### 3.编写代码：
##### (1)在实战(统计手机号耗费的总上行流量和下行流量)需求的基础上，增加一个分区类
ProvincePartitioner.java

```java
package phoneData;

import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Partitioner;

public class ProvincePartitioner extends Partitioner<Text, FlowBean> {

    @Override
    public int getPartition(Text key, FlowBean value, int numPartitions) {
        // 1 获取电话号码的前三位
        String preNum = key.toString().substring(0, 3);

        //注：如果设置的分区数小于下面的分区数，如3、则最后一个分区是混数据分区
        //注：如何设置的分区数大于下面的分区数，如3
        int partition = 4;

        // 2 判断是哪个省
        if ("136".equals(preNum)) {
            partition = 0;
        }else if ("137".equals(preNum)) {
            partition = 1;
        }else if ("138".equals(preNum)) {
            partition = 2;
        }else {
            partition = 3;
        }
        return partition;
    }
}
```
##### (2)编写Mapper，Reducer，和实战(统计手机号耗费的总上行流量、下行流量、总流量(序列化))的Mapper,Reducer一样。
##### (3)编写驱动：

FlowsumDriver.java

```java
package phoneData;

import java.io.IOException;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class FlowsumDriver {

    public static void main(String[] args) throws IllegalArgumentException, IOException, ClassNotFoundException, InterruptedException {

        //args = new String[]{"/Users/macbook/TestInfo/phone_data.txt", "/Users/macbook/TestInfo/MovlePhone2"};

        // 1 获取配置信息，或者job对象实例
        Configuration configuration = new Configuration();
        Job job = Job.getInstance(configuration);

        // 6 指定本程序的jar包所在的本地路径
        job.setJarByClass(FlowsumDriver.class);

        // 2 指定本业务job要使用的mapper/Reducer业务类
        job.setMapperClass(FlowCountMapper.class);
        job.setReducerClass(FlowCountReducer.class);

        // 3 指定mapper输出数据的kv类型
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(FlowBean.class);

        // 4 指定最终输出的数据的kv类型
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(FlowBean.class);

        job.setPartitionerClass(ProvincePartitioner.class);
        job.setNumReduceTasks(6);

        // 5 指定job的输入原始文件所在目录
        FileInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        // 7 将job中配置的相关参数，以及job所用的java类所在的jar包， 提交给yarn去运行
        job.waitForCompletion(true);
    }
}
```
#### 4.运行结果：

![运行结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZhZjEwNmYzZmNkMmRmYTkucG5n?x-oss-process=image/format,png)
