---
title: 'MapReduce实战：将统计结果按照总流量倒序排序(全排序)'
date: 2019-01-14 12:00:00
tags: 
- Hadoop
- MapReduce
- Hadoop实战
categories: Hadoop
---

#### 1.需求：
* 根据需求1产生的结果再次对总流量进行排序

#### 2.数据准备：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY2MmJjMTZhMDMwZjU2YjUucG5n?x-oss-process=image/format,png)

#### 3.分析

##### (1)把程序分两步走，第一步正常统计总流量，第二步再把结果进行排序
##### (2)context.write(总流量，手机号)
##### (3)FlowBean实现WritableComparable接口重写compareTo方法

```java
@Override
public int compareTo(FlowBean o) {
	// 倒序排列，从大到小
	return this.sumFlow > o.getSumFlow() ? -1 : 1;
}
```

 

#### 4.编写代码：
##### (1)编写FlowBean
FlowBean.java

```java
package phoneDataComparable;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
import org.apache.hadoop.io.WritableComparable;

import java.io.DataInput;
import java.io.DataOutput;
import java.io.IOException;

// 1 实现writable接口,因为要排序，所以这里使用的是WritableComparable
@Setter
@Getter
@AllArgsConstructor
@NoArgsConstructor
public class FlowBean implements WritableComparable<FlowBean> {

    private long upFlow;
    private long downFlow;
    private long sumFlow;

    public void set(long upFlow, long downFlow) {
        this.upFlow = upFlow;
        this.downFlow = downFlow;
        this.sumFlow = upFlow + downFlow;
    }

    /**
     * 比较方法
     *
     * @param o
     * @return
     */
    @Override
    public int compareTo(FlowBean o) {
        //降序是-1,升序是1
        return this.sumFlow > o.getSumFlow() ? -1 : 1;
    }

    /**
     * 序列化
     *
     * @param  out
     * @throws IOException
     */
    @Override
    public void write(DataOutput out) throws IOException {
        out.writeLong(upFlow);
        out.writeLong(downFlow);
        out.writeLong(sumFlow);
    }

    /**
     * 反序列化
     *
     * @param in
     * @throws IOException
     */
    @Override
    public void readFields(DataInput in) throws IOException {
        this.upFlow = in.readLong();
        this.downFlow = in.readLong();
        this.sumFlow = in.readLong();
    }
    @Override
    public String toString() {
        return upFlow + "\t" + downFlow + "\t" + sumFlow;
    }
}
```
##### (2)编写Mapper：
FlowCountMapper.java

```java
package phoneDataComparable;

import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

import java.io.IOException;

//<sum,phoneNum>  => <FlowBean,Text>
public class FlowCountMapper extends Mapper<LongWritable, Text, FlowBean, Text> {

    FlowBean k = new FlowBean();
    Text v = new Text();

    @Override
    protected void map(LongWritable key, Text value, Context context)
            throws IOException, InterruptedException {

        // 1 获取一行
        String line = value.toString();

        // 2 切割字段
        String[] fields = line.split("\t");

        // 3 封装对象
        // 取出手机号码
        String phoneNum = fields[0];
        // 取出上行流量和下行流量
        long upFlow = Long.parseLong(fields[1]);
        long downFlow = Long.parseLong(fields[2]);

        k.set(upFlow, downFlow);
        v.set(phoneNum);

        // 4 写出
        context.write(k, v);
    }
}
```
##### (3)编写Reducer：
FlowCountReducer.java

```java
package phoneDataComparable;

import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

import java.io.IOException;
//<Sum,电话号>  ===> <电话号,Sum>
public class FlowCountReducer extends Reducer<FlowBean,Text , Text, FlowBean> {
    @Override
    protected void reduce(FlowBean key, Iterable<Text> values, Context context) throws IOException, InterruptedException {
        for (Text text : values) {
            context.write(text, key);
        }
    }
}
```

##### (4)编写驱动：
FlowsumDriver.java

```java
package phoneDataComparable;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

import java.io.IOException;

public class FlowsumDriver {

    public static void main(String[] args) throws IllegalArgumentException, IOException, ClassNotFoundException, InterruptedException {

        //args = new String[]{"/Users/macbook/TestInfo/MovlePhone1/","/Users/macbook/TestInfo/MovlePhoneCompare"};

        // 1 获取配置信息，或者job对象实例
        Configuration configuration = new Configuration();
        Job job = Job.getInstance(configuration);

        // 6 指定本程序的jar包所在的本地路径
        job.setJarByClass(FlowsumDriver.class);

        // 2 指定本业务job要使用的mapper/Reducer业务类
        job.setMapperClass(FlowCountMapper.class);
        job.setReducerClass(FlowCountReducer.class);

        // 3 指定mapper输出数据的kv类型
        job.setMapOutputKeyClass(FlowBean.class);
        job.setMapOutputValueClass(Text.class);

        // 4 指定最终输出的数据的kv类型
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(FlowBean.class);

        // 5 指定job的输入原始文件所在目录
        FileInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        // 7 将job中配置的相关参数，以及job所用的java类所在的jar包， 提交给yarn去运行
        job.waitForCompletion(true);
    }
}
```

#### 5.运行结果：

![运行结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA1N2NkZjJiZmRkZWM5YWUucG5n?x-oss-process=image/format,png)


