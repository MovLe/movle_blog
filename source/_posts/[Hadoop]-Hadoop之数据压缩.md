---
title: '[Hadoop]-Hadoop之数据压缩'
date: 2019-01-02 14:50:00
tags: Hadoop
categories: Hadoop
---
#### 1.概述

&nbsp;&nbsp;&nbsp;&nbsp;压缩技术能够有效减少底层存储系统(HDFS)读写字节数。压缩提高了网络带宽和磁盘空间的效率。在Hadoop下，尤其是数据规模很大和工作负载密集的情况下，使用数据压缩显得非常重要。在这种情况下，I/O操作和网络数据传输要花大量的时间。还有，Shuffle与Merge过程同样也面临着巨大的I/O压力。

&nbsp;&nbsp;&nbsp;&nbsp;鉴于磁盘I/O和网络带宽是Hadoop的宝贵资源，数据压缩对于节省资源、最小化磁盘I/O和网络传输非常有帮助。不过，尽管压缩与解压操作的CPU开销不高，其性能的提升和资源的节省并非没有代价。

&nbsp;&nbsp;&nbsp;&nbsp;如果磁盘I/O和网络带宽影响了MapReduce作业性能，在任意MapReduce阶段启用压缩都可以改善端到端处理时间并减少I/O和网络流量。

&nbsp;&nbsp;&nbsp;&nbsp;压缩Mapreduce的一种优化策略：通过压缩编码对Mapper或者Reducer的输出进行压缩，以减少磁盘IO，提高MR程序运行速度（但相应增加了cpu运算负担）

&nbsp;&nbsp;&nbsp;&nbsp;注意：压缩特性运用得当能提高性能，但运用不当也可能降低性能。

##### (1)基本原则：
* (a)运算密集型的job，少用压缩
* (b)IO密集型的job，多用压缩

#### 2.MR支持的压缩编码

|压缩格式|hadoop自带?|算法|文件扩展名|是否可切分|换成压缩格式后，原来的程序是否需要修改|
|:-:|:-:|:-:|:-:|:-:|:-:|
|DEFAULT|是，直接使用|DEFAULT|.deflate|否|和文本处理一样，不需要修改|
|Gzip|是，直接使用|DEFAULT|.gz|否|和文本处理一样，不需要修改|
|bzip2|是，直接使用|bzip2|.bz2|是|和文本处理一样，不需要修改|
|LZO|否，需要安装|LZO|.lzo|是|需要建索引，还需要指定输入格式|
|Snappy|否，需要安装|Snappy|.snappy|否|和文本处理一样，不需要修改|

为了支持多种压缩/解压缩算法，Hadoop引入了编码/解码器，如下表所示

|压缩格式|对应的编码/解码器|
|:-:|:-:|
|DEFLATE|org.apache.hadoop.io.compress.DefaultCodec|
|gzip|org.apache.hadoop.io.compress.GzipCodec|
|bzip2|org.apache.hadoop.io.compress.BZip2Codec|
|LZO|com.hadoop.compression.lzo.LzopCodec|
|Snappy|org.apache.hadoop.io.compress.SnappyCodec|

压缩性能的比较

|压缩算法|原始文件大小|压缩文件大小|压缩速度|解压速度|
|:-:|:-:|:-:|:-:|:-:|
|gzip|8.3GB|1.8GB|17.5MB/s|58MB/s|
|bzip2|8.3GB|1.1GB|2.4MB/s|9.5MB/s|
|LZO|8.3GB|2.9GB|49.3MB/s|74.6MB/s|
[http://google.github.io/snappy/](http://google.github.io/snappy/)

#### 3 压缩方式选择

##### (1) Gzip压缩
* 优点：压缩率比较高，而且压缩/解压速度也比较快；hadoop本身支持，在应用中处理gzip格式的文件就和直接处理文本一样；大部分linux系统都自带gzip命令，使用方便。

* 缺点：不支持split。

应用场景：当每个文件压缩之后在130M以内的（1个块大小内），都可以考虑用gzip压缩格式。例如说一天或者一个小时的日志压缩成一个gzip文件，运行mapreduce程序的时候通过多个gzip文件达到并发。hive程序，streaming程序，和java写的mapreduce程序完全和文本处理一样，压缩之后原来的程序不需要做任何修改。

##### (2) Bzip2压缩

* 优点：支持split；具有很高的压缩率，比gzip压缩率都高；hadoop本身支持，但不支持native(java和c互操作的API接口)；在linux系统下自带bzip2命令，使用方便。

* 缺点：压缩/解压速度慢；不支持native。

应用场景：适合对速度要求不高，但需要较高的压缩率的时候，可以作为mapreduce作业的输出格式；或者输出之后的数据比较大，处理之后的数据需要压缩存档减少磁盘空间并且以后数据用得比较少的情况；或者对单个很大的文本文件想压缩减少存储空间，同时又需要支持split，而且兼容之前的应用程序（即应用程序不需要修改）的情况。

##### (3) Lzo压缩

* 优点：压缩/解压速度也比较快，合理的压缩率；支持split，是hadoop中最流行的压缩格式；可以在linux系统下安装lzop命令，使用方便。

* 缺点：压缩率比gzip要低一些；hadoop本身不支持，需要安装；在应用中对lzo格式的文件需要做一些特殊处理（为了支持split需要建索引，还需要指定inputformat为lzo格式）。

应用场景：一个很大的文本文件，压缩之后还大于200M以上的可以考虑，而且单个文件越大，lzo优点越越明显。

##### (4)Snappy压缩

* 优点：高速压缩速度和合理的压缩率。

* 缺点：不支持split；压缩率比gzip要低；hadoop本身不支持，需要安装；

应用场景：当Mapreduce作业的Map输出的数据比较大的时候，作为Map到Reduce的中间数据的压缩格式；或者作为一个Mapreduce作业的输出和另外一个Mapreduce作业的输入。

#### 4 压缩位置选择

压缩可以在MapReduce作用的任意阶段启用。

#### 5 压缩配置参数

要在Hadoop中启用压缩，可以配置如下参数：

|参数|默认值|阶段|建议|
|:-:|:-:|:-:|:-:|
|io.compression.codecs<br>(在core-site.xml中配置)|org.apache.hadoop.io.compress.DefaultCodec, org.apache.hadoop.io.compress.GzipCodec, org.apache.hadoop.io.compress.BZip2Codec|输入压缩|Hadoop使用文件扩展名判断是否支持某种编解码器|
|mapreduce.map.output.compress<br>(在mapred-site.xml中配置)|false|mapper输出|这个参数设为true启用压缩|
|mapreduce.map.output.compress.codec<br>(在mapred-site.xml中配置)|org.apache.hadoop.io.compress.DefaultCodec|mapper输出|使用LZO或snappy编解码器在此阶段压缩数据|
|mapreduce.output.fileoutputformat.compress<br>(在mapred-site.xml中配置)|false|reducer输出|这个参数设为true启用压缩|
|mapreduce.output.fileoutputformat.compress.codec<br>(在mapred-site.xml中配置)|org.apache.hadoop.io.compress. DefaultCodec|reducer输出|使用标准工具或者编解码器，如gzip和bzip2|
|mapreduce.output.fileoutputformat.compress.type<br>(在mapred-site.xml中配置)|RECORD|reducer输出|SequenceFile输出使用的压缩类型：NONE和BLOCK|

#### 6 压缩实战

