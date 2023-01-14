---
title: '[Hadoop]-MapReduce实战，简单清洗日志'
date: 2019-01-13 11:00:00
tags: 
- Hadoop
- MapReduce
- Hadoop实战
categories: Hadoop
---

#### 1.情况，利用MapReduce将web.txt文件进行简单的清洗，去除脏数据
#### 2.日志文件web.txt
```txt
194.237.142.21 - - [18/Sep/2013:06:49:18 +0000] "GET /wp-content/uploads/2013/07/rstudio-git3.png HTTP/1.1" 304 0 "-" "Mozilla/4.0 (compatible;)"
183.49.46.228 - - [18/Sep/2013:06:49:23 +0000] "-" 400 0 "-" "-"
163.177.71.12 - - [18/Sep/2013:06:49:33 +0000] "HEAD / HTTP/1.1" 200 20 "-" "DNSPod-Monitor/1.0"
163.177.71.12 - - [18/Sep/2013:06:49:36 +0000] "HEAD / HTTP/1.1" 200 20 "-" "DNSPod-Monitor/1.0"
101.226.68.137 - - [18/Sep/2013:06:49:42 +0000] "HEAD / HTTP/1.1" 200 20 "-" "DNSPod-Monitor/1.0"
101.226.68.137 - - [18/Sep/2013:06:49:45 +0000] "HEAD / HTTP/1.1" 200 20 "-" "DNSPod-Monitor/1.0"
60.208.6.156 - - [18/Sep/2013:06:49:48 +0000] "GET /wp-content/uploads/2013/07/rcassandra.png HTTP/1.0" 200 185524 "http://cos.name/category/software/packages/" "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/29.0.1547.66 Safari/537.36"
222.68.172.190 - - [18/Sep/2013:06:49:57 +0000] "GET /images/my.jpg HTTP/1.1" 200 19939 "http://www.angularjs.cn/A00n" "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/29.0.1547.66 Safari/537.36"
222.68.172.190 - - [18/Sep/2013:06:50:08 +0000] "-" 400 0 "-" "-"
183.195.232.138 - - [18/Sep/2013:06:50:16 +0000] "HEAD / HTTP/1.1" 200 20 "-" "DNSPod-Monitor/1.0"
183.195.232.138 - - [18/Sep/2013:06:50:16 +0000] "HEAD / HTTP/1.1" 200 20 "-" "DNSPod-Monitor/1.0"
66.249.66.84 - - [18/Sep/2013:06:50:28 +0000] "GET /page/6/ HTTP/1.1" 200 27777 "-" "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)"
221.130.41.168 - - [18/Sep/2013:06:50:37 +0000] "GET /feed/ HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/29.0.1547.66 Safari/537.36"
157.55.35.40 - - [18/Sep/2013:06:51:13 +0000] "GET /robots.txt HTTP/1.1" 200 150 "-" "Mozilla/5.0 (compatible; bingbot/2.0; +http://www.bing.com/bingbot.htm)"
50.116.27.194 - - [18/Sep/2013:06:51:35 +0000] "POST /wp-cron.php?doing_wp_cron=1379487095.2510800361633300781250 HTTP/1.0" 200 0 "-" "WordPress/3.6; http://blog.fens.me"
58.215.204.118 - - [18/Sep/2013:06:51:35 +0000] "GET /nodejs-socketio-chat/ HTTP/1.1" 200 10818 "http://www.google.com/url?sa=t&rct=j&q=nodejs%20%E5%BC%82%E6%AD%A5%E5%B9%BF%E6%92%AD&source=web&cd=1&cad=rja&ved=0CCgQFjAA&url=%68%74%74%70%3a%2f%2f%62%6c%6f%67%2e%66%65%6e%73%2e%6d%65%2f%6e%6f%64%65%6a%73%2d%73%6f%63%6b%65%74%69%6f%2d%63%68%61%74%2f&ei=rko5UrylAefOiAe7_IGQBw&usg=AFQjCNG6YWoZsJ_bSj8kTnMHcH51hYQkAA&bvm=bv.52288139,d.aGc" "Mozilla/5.0 (Windows NT 5.1; rv:23.0) Gecko/20100101 Firefox/23.0"
```
![web.txt](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM0MzBmYzEwMzYwYzlkZmQucG5n?x-oss-process=image/format,png)

#### 2.代码
##### (1)pom.xml
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
##### (2).LogBean代码：

```java
package ETLLOG;

import lombok.*;

@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class LogBean {
    //客户端IP
    private String remote_addr;
    //用户名称 忽略<->
    private String remote_user;
    //时间
    private String time_local;
    //URL请求
    private String request;
    //返回状态码
    private String status;
    //文件内容大小
    private String body_bytes_sent;
    //链接页面
    private String http_referer;
    //浏览的相关信息
    private String http_user_agent;

    //判断数据是否合法
    private boolean valid = true;

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append(this.valid);
        sb.append("\001").append(this.remote_addr);
        sb.append("\001").append(this.remote_user);
        sb.append("\001").append(this.time_local);
        sb.append("\001").append(this.request);
        sb.append("\001").append(this.status);
        sb.append("\001").append(this.body_bytes_sent);
        sb.append("\001").append(this.http_referer);
        sb.append("\001").append(this.http_user_agent);

        return sb.toString();
    }
}
```
##### (3).LogDriver代码:

```java
package ETLLOG;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class LogDriver {
    public static void main(String[] args) throws Exception {
        
        //获取配置信息
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf);

        //加载反射类
        job.setJarByClass(LogDriver.class);
        job.setMapperClass(LoggerMapper.class);
        //job.setReducerClass();

        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(NullWritable.class);

        //输入数据和输出数据的路径
        FileInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        //job提交
        job.waitForCompletion(true);
    }
}
```
##### (4).LogMapper代码：
```java
package ETLLOG2;

import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

import java.io.IOException;

public class LoggerMapper extends Mapper<LongWritable,Text,Text,NullWritable>{

    Text k = new Text();
    @Override
    protected void map(LongWritable key,Text value,Context context) throws IOException, InterruptedException {
        //1.Text ===> String
        String line = value.toString();

        //2.解析日志
        LogBean bean = parseLog(line);

        //3.判断非法数据
        if (!bean.isValid()) {
            return;
        }

        k.set(bean.toString());

        //4.输出
        context.write(k,NullWritable.get());
    }

    /**
     * 解析日志
     *
     * @param line
     * @return
     */
    private LogBean parseLog(String line) {
        LogBean logBean = new LogBean();

        //1.截取后的字段属性
        String[] fields = line.split(" ");
        //筛选条件1：字段长度大于11
        if (fields.length > 11) {
            //2.封装数据
            logBean.setRemote_addr(fields[0]);
            logBean.setRemote_user(fields[1]);
            logBean.setTime_local(fields[3].substring(1));
            logBean.setRequest(fields[6]);
            logBean.setStatus(fields[8]);
            logBean.setBody_bytes_sent(fields[9]);
            logBean.setHttp_referer(fields[10]);

            //如果字段长度大于12，就拼接浏览器来源
            //筛选条件2：如果有浏览器来源就拼接上，看是否大于12
            if (fields.length > 12) {
                logBean.setHttp_user_agent(fields[11] + " " + fields[12]);
            }else {
                //浏览的相关信息
                logBean.setHttp_user_agent(fields[11]);
            }

            //判断状态码
            //筛选条件3：状态码大于等于400，是非法数据
            if (Integer.parseInt(logBean.getStatus()) >= 400) {
                logBean.setValid(false);
            }
        }else {
            logBean.setValid(false);
        }
        return logBean;
    }
}
```

#### 3.运行
##### (1)将程序打包，上传到hadoop集群，将web.txt发送到hdfs
##### (2)运行：
```shell
hadoop jar Hadoop-1.0-SNAPSHOT.jar ETLLOG.LogDriver /web.txt /out
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200506210832620.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FmbHlpbmdjYXQ1MjA=,size_16,color_FFFFFF,t_70)

##### (3)运行结果
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200506211632856.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FmbHlpbmdjYXQ1MjA=,size_16,color_FFFFFF,t_70)



