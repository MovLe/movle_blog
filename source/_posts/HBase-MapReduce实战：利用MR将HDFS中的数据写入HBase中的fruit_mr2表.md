---
title: 'HBase-MapReduce实战：利用MR将HDFS中的数据写入HBase中的fruit_mr2表'
date: 2019-04-09 14:00:00
tags: 
- HBase
- HBase实战
categories: HBase
---

#### 1.构建mapper
ReadFruitFromHDFSMapper.java
```java
package HBaseMR.HDFSToHBase;

import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

import java.io.IOException;

/**
 * @ClassName ReadFruitFromHDFSMapper
 * @MethodDesc: TODO ReadFruitFromHDFSMapper功能介绍
 * @Author Movle
 * @Date 5/10/20 10:16 下午
 * @Version 1.0
 * @Email movle_xjk@foxmail.com
 **/


public class ReadFruitFromHDFSMapper extends Mapper<LongWritable, Text, ImmutableBytesWritable, Put> {
    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        //从HDFS中读取的数据
        String lineValue = value.toString();
        //读取出来的每行数据使用\t进行分割，存于String数组
        String[] values = lineValue.split("\t");

        //根据数据中值的含义取值
        String rowKey = values[0];
        String name = values[1];
        String color = values[2];

        //初始化rowKey
        ImmutableBytesWritable rowKeyWritable = new ImmutableBytesWritable(Bytes.toBytes(rowKey));

        //初始化put对象
        Put put = new Put(Bytes.toBytes(rowKey));

        //参数分别:列族、列、值
        put.add(Bytes.toBytes("info"), Bytes.toBytes("name"),  Bytes.toBytes(name));
        put.add(Bytes.toBytes("info"), Bytes.toBytes("color"),  Bytes.toBytes(color));

        context.write(rowKeyWritable, put);
    }
}
```
#### 2.构建reducer
WriteFruitMRFromTxtReducer.java
```java
package HBaseMR.HDFSToHBase;

import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.mapreduce.TableReducer;
import org.apache.hadoop.io.NullWritable;

import java.io.IOException;

/**
 * @ClassName WriteFruitMRFromTxtReducer
 * @MethodDesc: TODO WriteFruitMRFromTxtReducer功能介绍
 * @Author Movle
 * @Date 5/10/20 10:18 下午
 * @Version 1.0
 * @Email movle_xjk@foxmail.com
 **/


public class WriteFruitMRFromTxtReducer extends TableReducer<ImmutableBytesWritable, Put, NullWritable> {

    @Override
    protected void reduce(ImmutableBytesWritable key, Iterable<Put> values, Context context) throws IOException, InterruptedException {
        //读出来的每一行数据写入到fruit_hdfs表中
        for(Put put: values){
            context.write(NullWritable.get(), put);
        }
    }
}
```
#### 3.构建runner
Txt2FruitRunner.java
```java
package HBaseMR.HDFSToHBase;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.mapreduce.TableMapReduceUtil;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

import java.io.IOException;

/**
 * @ClassName Txt2FruitRunner
 * @MethodDesc: TODO Txt2FruitRunner功能介绍
 * @Author Movle
 * @Date 5/10/20 10:19 下午
 * @Version 1.0
 * @Email movle_xjk@foxmail.com
 **/

public class Txt2FruitRunner extends Configured implements Tool {

    @Override
    public int run(String[] strings) throws Exception {
        //得到Configuration
        Configuration conf = this.getConf();
        
        //创建Job任务
        Job job = Job.getInstance(conf, this.getClass().getSimpleName());
        job.setJarByClass(Txt2FruitRunner.class);
        Path inPath = new Path("hdfs://hadoop2:9000/input_fruit/fruit.tsv");
        FileInputFormat.addInputPath(job, inPath);

        //设置Mapper
        job.setMapperClass(ReadFruitFromHDFSMapper.class);
        job.setMapOutputKeyClass(ImmutableBytesWritable.class);
        job.setMapOutputValueClass(Put.class);

        //设置Reducer
        TableMapReduceUtil.initTableReducerJob("fruit_mr2", WriteFruitMRFromTxtReducer.class, job);

        //设置Reduce数量，最少1个
        job.setNumReduceTasks(1);

        boolean isSuccess = job.waitForCompletion(true);
        if (!isSuccess) {
            throw new IOException("Job running with error");
        }
        return isSuccess ? 0 : 1;
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = HBaseConfiguration.create();
        int status = ToolRunner.run(conf, new Txt2FruitRunner(), args);
        System.exit(status);
    }
}
```
#### 4.打包上传到Hbase集群并运行：
```shell
/opt/module/hadoop-2.8.4/bin/yarn jar /opt/module/hbase-1.3.1/HBase-1.0-SNAPSHOT.jar  HBaseMR.HDFSToHBase.Txt2FruitRunner
```
#### 5.查看结果：
```shell
scan 'fruit_mr2'
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZjMzllZGIzZTc3MzFmYzcucG5n?x-oss-process=image/format,png)
