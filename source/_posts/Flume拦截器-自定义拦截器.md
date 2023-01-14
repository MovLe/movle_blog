---
title: 'Flume拦截器-自定义拦截器'
date: 2019-07-03 23:03:00
tags: 
- Flume
- Flume拦截器
categories: Flume
---
#### 1.写一个小写字母变成大写的拦截器；
#### 2.编写代码：
##### (1)pom.xml
```xml
    <dependencies>
        <!-- flume核心依赖 -->
        <dependency>
            <groupId>org.apache.flume</groupId>
            <artifactId>flume-ng-core</artifactId>
            <version>1.8.0</version>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <!-- 打包插件 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>2.4</version>
                <configuration>
                    <archive>
                        <manifest>
                            <addClasspath>true</addClasspath>
                            <classpathPrefix>lib/</classpathPrefix>
                            <mainClass></mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
            <!-- 编译插件 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>utf-8</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>
```
##### (2)拦截器
```java
import org.apache.flume.Context;
import org.apache.flume.Event;
import org.apache.flume.interceptor.Interceptor;
 
import java.util.ArrayList;
import java.util.List;
 
public class MyInterceptor implements Interceptor {
    @Override
    public void initialize() {
    }
 
    @Override
    public void close() {
    }
 
    /**
     * 拦截source发送到通道channel中的消息
     *
     * @param event 接收过滤的event
     * @return event    根据业务处理后的event
     */
    @Override
    public Event intercept(Event event) {
        // 获取事件对象中的字节数据
        byte[] arr = event.getBody();
        // 将获取的数据转换成大写
        event.setBody(new String(arr).toUpperCase().getBytes());
        // 返回到消息中
        return event;
    }
    // 接收被过滤事件集合
    @Override
    public List<Event> intercept(List<Event> events) {
        List<Event> list = new ArrayList<>();
        for (Event event : events) {
            list.add(intercept(event));
        }
        return list;
    }
 
    public static class Builder implements Interceptor.Builder {
        // 获取配置文件的属性
        @Override
        public Interceptor build() {
            return new MyInterceptor();
        }
 
        @Override
        public void configure(Context context) {
 
        }
    }
```
#### 3.使用Maven做成Jar包，在flume的目录下mkdir jar，上传此jar到jar目录中

#### 4.新建flume配置文件：

##### (1)ToUpCase.conf
```shell
#1.agent 
a1.sources = r1
a1.sinks =k1
a1.channels = c1
 
 
# Describe/configure the source
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /opt/plus
a1.sources.r1.interceptors = i1
#全类名$Builder
a1.sources.r1.interceptors.i1.type = ToUpCase.MyInterceptor$Builder
 
# Describe the sink
a1.sinks.k1.type = hdfs
a1.sinks.k1.hdfs.path = hdfs://hadoop2:9000/ToUpCase1
a1.sinks.k1.hdfs.filePrefix = events-
a1.sinks.k1.hdfs.round = true
a1.sinks.k1.hdfs.roundValue = 10
a1.sinks.k1.hdfs.roundUnit = minute
a1.sinks.k1.hdfs.rollInterval = 3
a1.sinks.k1.hdfs.rollSize = 20
a1.sinks.k1.hdfs.rollCount = 5
a1.sinks.k1.hdfs.batchSize = 1
a1.sinks.k1.hdfs.useLocalTimeStamp = true
#生成的文件类型，默认是 Sequencefile，可用 DataStream，则为普通文本
a1.sinks.k1.hdfs.fileType = DataStream
 
# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100
 
# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```
#### 5.运行：
```shell
bin/flume-ng agent -c conf/ -n a1 -f jar/ToUpCase.conf -C jar/Flume-1.0-SNAPSHOT.jar -Dflume.root.logger=DEBUG,console
```