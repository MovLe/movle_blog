---
title: 'MapReduce实战-多表案例：Map端表合并(Distributedcache)'
date: 2019-01-14 13:00:00
tags: 
- Hadoop
- MapReduce
- Hadoop实战
categories: Hadoop
---

#### 1.分析
&nbsp;&nbsp;&nbsp;&nbsp;适用于关联表中有小表的情形；
&nbsp;&nbsp;&nbsp;&nbsp;可以将小表分发到所有的map节点，这样，map节点就可以在本地对自己所读到的大表数据进行合并并输出最终结果，可以大大提高合并操作的并发度，加快处理速度。

#### 2.实战案例：
##### (1)先在驱动模块中添加缓存文件

DistributedCacheDriver.java
```java
package MapJoin;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

import java.net.URI;

public class DistributedCacheDriver {

    public static void main(String[] args) throws Exception {

        //args = new String[]{"/Users/macbook/TestInfo/mapjoin/order.txt","/Users/macbook/TestInfo/mapjoin3"};

        // 1 获取job信息
        Configuration configuration = new Configuration();
        Job job = Job.getInstance(configuration);

        // 2 设置加载jar包路径
        job.setJarByClass(DistributedCacheDriver.class);

        // 3 关联map
        job.setMapperClass(DistributedCacheMapper.class);

        // 4 设置最终输出数据类型
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(NullWritable.class);

        // 5 设置输入输出路径
        FileInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        //这个地方添加的是小表的数据，不是setup

        //job.addCacheFile(new URI("file:///f:/date/A/mapjoin/lov/pd.txt"));
		//这里是hdfs上的pd.txt地址
        job.addCacheFile(new URI("/pd.txt"));

        //无reduce，设置为0
        job.setNumReduceTasks(0);

        // 8 提交
       job.waitForCompletion(true);

    }
}
```

##### (2)读取缓存的文件数据

```java
package MapJoin;


import org.apache.commons.lang.StringUtils;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

import java.io.*;
import java.util.HashMap;
import java.util.Map;

/**
 * 学习点1：两个表join
 * 学习点2：小表加缓存
 * 学习点3：setup方法的使用
 */
public class DistributedCacheMapper extends Mapper<LongWritable, Text, Text, NullWritable> {

    Map<String, String> pdMap = new HashMap<>();

    //ctrl + o 选中两个方法

    /**
     * 初始化方法
     * 把pd.txt加载进来
     *
     * @param context
     * @throws IOException
     * @throws InterruptedException
     */
    @Override
    protected void setup(Context context) throws IOException, InterruptedException {
        //把pd.txt文件加载进来,这里是本地pd.txt地址
        BufferedReader reader = new BufferedReader(new InputStreamReader(
                new FileInputStream(new File("/Users/macbook/TestInfo/lov/pd.txt")), "UTF-8"));

        String line;
        //import org.apache.commons.lang.StringUtils; 导包注意，是common包，不是hadoop的
        while (StringUtils.isNotEmpty(line = reader.readLine())) {
            String[] fields = line.split("\t");
            //产品id
            String pid = fields[0];
            //产品名字
            String pname = fields[1];
            pdMap.put(pid, pname);
        }
        reader.close();
    }

    Text k = new Text();

    /**
     * order.txt的处理
     *
     * @param key
     * @param value
     * @param context
     * @throws IOException
     * @throws InterruptedException
     */
    @Override
    protected void map(LongWritable key,
                       Text value,
                       Context context) throws IOException, InterruptedException {
        //转类型
        String line = value.toString();
        //切分
        String[] fields = line.split("\t");

        //订单id,产品id，产品数量
        String orderId = fields[0];
        String pid = fields[1];
        String amount = fields[2];

        //通过pid（key），拿到pname（value）
        String pname = pdMap.get(pid);

        //数据字段拼接
        k.set(orderId + "\t" + pname + "\t" + amount);

        context.write(k,NullWritable.get());
    }
}
```
#### 3.结果：
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWYzZTg1NWY3ODEzMTJkNGIucG5n?x-oss-process=image/format,png)
