---
title: 'HBase-MapReduce实战：利用MR将HBase中的fruit表导入到HBase中的fruit_mr表中'
date: 2019-04-09 13:00:00
tags: 
- HBase
- HBase实战
categories: HBase
---

#### 1.构建mapper类
ReadFruitMapper.java
```java
package HBaseMR.HBaseToHBase;

import java.io.IOException;
import org.apache.hadoop.hbase.Cell;
import org.apache.hadoop.hbase.CellUtil;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.mapreduce.TableMapper;
import org.apache.hadoop.hbase.util.Bytes;
/**
 * @ClassName ReadFruitMapper
 * @MethodDesc:
 * @Author Movle
 * @Date 5/10/20 9:27 下午
 * @Version 1.0
 * @Email movle_xjk@foxmail.com
 **/


public class ReadFruitMapper extends TableMapper<ImmutableBytesWritable, Put> {


    @Override
    protected void map(ImmutableBytesWritable key, Result value, Context context)
            throws IOException, InterruptedException {
        //将fruit的name和color提取出来，相当于将每一行数据读取出来放入到Put对象中。
        Put put = new Put(key.get());
        //遍历添加column行
        for(Cell cell: value.rawCells()){
            //添加/克隆列族:info
            if("info".equals(Bytes.toString(CellUtil.cloneFamily(cell)))){
                //添加/克隆列：name
                if("name".equals(Bytes.toString(CellUtil.cloneQualifier(cell)))){
                    //将该列cell加入到put对象中
                    put.add(cell);
                    //添加/克隆列:color
                }else if("color".equals(Bytes.toString(CellUtil.cloneQualifier(cell)))){
                    //向该列cell加入到put对象中
                    put.add(cell);
                }
            }
        }
        //将从fruit读取到的每行数据写入到context中作为map的输出
        context.write(key, put);
    }
}
```

#### 2.构建reduce类
WriteFruitMRReducer .java
```java
package HBaseMR.HBaseToHBase;

import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.mapreduce.TableReducer;
import org.apache.hadoop.io.NullWritable;

import java.io.IOException;

/**
 * @ClassName WriteFruitMRReducer
 * @MethodDesc: TODO WriteFruitMRReducer功能介绍
 * @Author Movle
 * @Date 5/10/20 9:29 下午
 * @Version 1.0
 * @Email movle_xjk@foxmail.com
 **/

public class WriteFruitMRReducer extends TableReducer<ImmutableBytesWritable, Put,NullWritable> {
    @Override
    protected void reduce(ImmutableBytesWritable key, Iterable<Put> values, Context context)
            throws IOException, InterruptedException {
        //读出来的每一行数据写入到fruit_mr表中
        for(Put put: values){
            context.write(NullWritable.get(), put);
        }
    }

}
```
#### 3.构建runner
FruitToFruitMRRunner.java
```java
package HBaseMR.HBaseToHBase;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.mapreduce.TableMapReduceUtil;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

import java.io.IOException;

/**
 * @ClassName FruitToFruitMRRunner
 * @MethodDesc: TODO FruitToFruitMRRunner功能介绍
 * @Author Movle
 * @Date 5/10/20 9:32 下午
 * @Version 1.0
 * @Email movle_xjk@foxmail.com
 **/


public class FruitToFruitMRRunner extends Configured implements Tool {

    @Override
    public int run(String[] strings) throws Exception {
        //得到Configuration
        Configuration conf = this.getConf();
        //创建Job任务
        Job job = Job.getInstance(conf, this.getClass().getSimpleName());
        job.setJarByClass(FruitToFruitMRRunner.class);

        //配置Job,创建一个扫描器
        Scan scan = new Scan();
        scan.setCacheBlocks(false);
        scan.setCaching(500);

        //设置Mapper，注意导入的是mapreduce包下的，不是mapred包下的，后者是老版本
        TableMapReduceUtil.initTableMapperJob(
                "fruit",
                scan,
                ReadFruitMapper.class,
                ImmutableBytesWritable.class,
                Put.class,
                job
        );
//        TableMapReduceUtil.initTableMapperJob(
//                "fruit",  //读数据的表
//                scan,  //扫描器
//                ReadFruitMapper.class, //设置map类
//                ImmutableBytesWritable.class,  //设置输出的key类型
//                Put.class,  //设置Mapper输出value值类型
//                job  //配置的job
//        );
        //设置Reducer
        TableMapReduceUtil.initTableReducerJob(
                "fruit_mr",
                WriteFruitMRReducer.class,
                job);
//        TableMapReduceUtil.initTableReducerJob(
//                "fruit_mr",  //将数据戏写入的表
//                WriteFruitMRReducer.class,  //设置reduce类
//                job);

        //设置Reduce数量，最少1个
        job.setNumReduceTasks(1);


        boolean isSuccess = job.waitForCompletion(true);
        if(!isSuccess){
            throw new IOException("Job running with error");
        }
        return isSuccess ? 0 : 1;
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = HBaseConfiguration.create();
        int status = ToolRunner.run(conf, new FruitToFruitMRRunner(), args);
        System.exit(status);
    }
}
```

#### 4.打包上传到HBase集群并运行
```shell
/opt/module/hadoop-2.8.4/bin/yarn jar /opt/module/hbase-1.3.1/HBase-1.0-SNAPSHOT.jar  HBaseMR.HBaseToHBase.FruitToFruitMRRunner
```
提示：运行任务前，如果待数据导入的表不存在，则需要提前创建之

#### 5.查看结果：
```shell
scan 'fruit_mr'
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUwNGNjMWMzZTE3NDc3MmQucG5n?x-oss-process=image/format,png)
