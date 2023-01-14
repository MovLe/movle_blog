---
title: '[Hive]-Hive之压缩和存储'
date: 2019-07-15 22:00:00
tags: Hive
categories: Hive
---

### 一.Hadoop源码编译支持Snappy压缩

#### 1.资源准备

##### (1).CentOS联网

配置CentOS能连接外网。Linux虚拟机ping www.baidu.com是畅通的

注意：采用root角色编译，减少文件夹权限出现问题

###### (2).jar包准备(hadoop源码、JDK8 、maven、protobuf)
```
(a)hadoop-2.8.4-src.tar.gz
(b)jdk-8u144-linux-x64.tar.gz
(c)snappy-1.1.3.tar.gz
(d)apache-maven-3.0.5-bin.tar.gz
(e)protobuf-2.5.0.tar.gz
```
#### 2.jar包安装

##### (0).注意：
所有操作必须在root用户下完成

##### (1).JDK解压、配置环境变量JAVA_HOME和PATH，验证java-version(如下都需要验证是否配置成功)

解压：
```shell
tar -zxf jdk-8u144-linux-x64.tar.gz -C /opt/module/
```
修改环境变量
```shell
vi /etc/profile
```
修改内容：
```shell
#JAVA_HOME
export JAVA_HOME=/opt/module/jdk1.8.0_144
export PATH=$PATH:$JAVA_HOME/bin
```
使环境变量生效
```shell
source /etc/profile
```
验证命令：java -version

##### (2).Maven解压、配置  MAVEN_HOME和PATH。

解压
```shell
tar -zxvf apache-maven-3.0.5-bin.tar.gz -C /opt/module/
```
修改环境变量
```shell
vi /etc/profile
```
修改内容
```shell
#MAVEN_HOME
export MAVEN_HOME=/opt/module/apache-maven-3.0.5
export PATH=$PATH:$MAVEN_HOME/bin
```
使环境变量生效
```shell
source /etc/profile
```
验证命令：mvn -version

#### 3. 编译源码

##### (1).准备编译环境
```shell
[root@bigdata111 software]# yum install svn

[root@bigdata111 software]# yum install autoconf automake libtool cmake

[root@bigdata111 software]# yum install ncurses-devel

[root@bigdata111 software]# yum install openssl-devel

[root@bigdata111 software]# yum install gcc*
```
##### (2).编译安装snappy
```shell
[root@bigdata111 software]# tar -zxvf snappy-1.1.3.tar.gz -C /opt/module/

[root@bigdata111 module]# cd snappy-1.1.3/

[root@bigdata111 snappy-1.1.3]# ./configure

[root@bigdata111 snappy-1.1.3]# make

[root@bigdata111 snappy-1.1.3]# make install
```
查看snappy库文件
```shell
[root@bigdata111 snappy-1.1.3]# ls -lh /usr/local/lib |grep snappy
```
##### (3).编译安装protobuf
```shell
[root@bigdata111 software]# tar -zxvf protobuf-2.5.0.tar.gz -C /opt/module/

[root@bigdata111 module]# cd protobuf-2.5.0/

[root@bigdata111 protobuf-2.5.0]# ./configure 

[root@bigdata111 protobuf-2.5.0]#  make 

[root@bigdata111 protobuf-2.5.0]#  make install
```
查看protobuf版本以测试是否安装成功
```shell
[root@bigdata111 protobuf-2.5.0]# protoc --version
```
##### (4).编译hadoop native
```shell
[root@bigdata111 software]# tar -zxvf hadoop-2.8.4-src.tar.gz

[root@bigdata111 software]# cd hadoop-2.8.4-src/

[root@bigdata111 software]# mvn clean package -DskipTests -Pdist,native -Dtar -Dsnappy.lib=/usr/local/lib -Dbundle.snappy
```

&nbsp;&nbsp;&nbsp;&nbsp;执行成功后，/opt/software/hadoop-2.8.4-src/hadoop-dist/target/[hadoop](applewebdata://9D206B90-1CCC-4328-98EC-98839FAA89F0/_blank)-2.8.4.tar.gz即为新生成的支持snappy压缩的二进制安装包。

### 二.Hadoop压缩配置

#### 1.MR支持的压缩编码

| 压缩格式| 工具| 算法 | 文件扩展名 | 是否可切分|
|:-:|:-:|:-:|:-:|:-:|
| DEFAULT| 无| DEFAULT|.deflate| 否|
| Gzip| gzip| DEFAULT| .gz|否|
| bzip2| bzip2|bzip2|.bz2| 是|
| LZO|lzop|LZO|.lzo|是|
|Snappy|无|Snappy| .snappy|否|

&nbsp;&nbsp;&nbsp;&nbsp;为了支持多种压缩/解压缩算法，Hadoop引入了编码/解码器，如下表所示

|压缩格式| 对应的编码/解码器|
|:-:|:-:|
|DEFLATE| org.apache.hadoop.io.compress.DefaultCodec|
|gzip|org.apache.hadoop.io.compress.GzipCodec|
|bzip2| org.apache.hadoop.io.compress.BZip2Codec|
|LZO|com.hadoop.compression.lzo.LzopCodec|
|Snappy|org.apache.hadoop.io.compress.SnappyCodec|


压缩性能的比较

|压缩算法|原始文件大小| 压缩文件大小|压缩速度|解压速度|
|:-:|:-:|:-:|:-:|:-:|
|gzip|8.3GB|1.8GB|17.5MB/s| 58MB/s|
|bzip2|8.3GB|1.1GB|2.4MB/s|9.5MB/s|
|LZO|8.3GB|2.9GB|49.3MB/s|74.6MB/s|



On a single core of a Core i7 processor in 64-bit mode, Snappy compresses at about 250 MB/sec or more and decompresses at about 500 MB/sec or more.

#### 2.压缩参数配置

要在Hadoop中启用压缩，可以配置如下参数（mapred-site.xml文件中）：

|参数| 默认值|阶段|建议|
|:-:|:-:|:-:|:-:|
|io.compression.codecs(在core-site.xml中配置)| org.apache.hadoop.io.compress.DefaultCodec<br>org.apache.hadoop.io.compress.GzipCodec<br>org.apache.hadoop.io.compress.BZip2Codec<br>org.apache.hadoop.io.compress.Lz4Codec|输入压缩|Hadoop使用文件扩展名判断是否支持某种编解码器|
|mapreduce.map.output.compress|false|mapper输出|这个参数设为true启用压缩|
|mapreduce.map.output.compress.codec|org.apache.hadoop.io.compress.DefaultCodec|mapper输出|使用LZO、LZ4或snappy编解码器在此阶段压缩数据|
|mapreduce.output.fileoutputformat.compress|false|reducer输出|这个参数设为true启用压缩|
|mapreduce.output.fileoutputformat.compress.codec|org.apache.hadoop.io.compress. DefaultCodec|reducer输出|使用标准工具或者编解码器，如gzip和bzip2|
|mapreduce.output.fileoutputformat.compress.type|RECORD|reducer输出|SequenceFile输出使用的压缩类型：NONE和BLOCK|

### 三.开启Map输出阶段压缩

&nbsp;&nbsp;&nbsp;&nbsp;开启map输出阶段压缩可以减少job中map和Reduce task间数据传输量。具体配置如下：

**案例实操：**
##### (1)开启hive中间传输数据压缩功能,默认为false
```shell
hive (default)>set hive.exec.compress.intermediate=true;
```
##### (2)开启mapreduce中map输出压缩功能,默认为false
```shell
hive (default)>set mapreduce.map.output.compress=true;
```
##### (3)设置mapreduce中map输出数据的压缩方式
```shell
hive (default)>set mapreduce.map.output.compress.codec= org.apache.hadoop.io.compress.SnappyCodec;
```
##### (4)执行查询语句
```shell
hive (default)> select count(ename) name from emp;
```
### 四.开启Reduce输出阶段压缩

&nbsp;&nbsp;&nbsp;&nbsp;当Hive将输出写入到表中时，输出内容同样可以进行压缩。属性hive.exec.compress.output控制着这个功能。用户可能需要保持默认设置文件中的默认值false，这样默认的输出就是非压缩的纯文本文件了。用户可以通过在查询语句或执行脚本中设置这个值为true，来开启输出结果压缩功能。

**案例实操：**
##### (1)
```shell
hive (default)> set mapreduce.job.reduces=3;
```
##### (2)开启hive最终输出数据压缩功能,默认为false
```shell
hive (default)>set hive.exec.compress.output=true;
```
##### (3)开启mapreduce最终输出数据压缩,默认为false
```shell
hive (default)>set mapreduce.output.fileoutputformat.compress=true;
```
##### (4)设置mapreduce最终数据输出压缩方式
```shell
hive (default)> set mapreduce.output.fileoutputformat.compress.codec = org.apache.hadoop.io.compress.SnappyCodec;
```
##### (5)设置mapreduce最终数据输出压缩为块压缩
```shell
hive (default)> set mapreduce.output.fileoutputformat.compress.type=BLOCK;
```
##### (6)测试一下输出结果是否是压缩文件
```shell
hive (default)> insert overwrite local directory '/opt/module/datas/distribute-result' select * from emp distribute by deptno sort by empno desc;
```
测试:不设置reduce，结果是否是压缩格式
```shell
hive (default)> set mapreduce.job.reduces=-1;

insert overwrite local directory '/opt/module/datas/distribute-result' select * from test;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTkwMGI2ZjI4MTA4YTBkYjIucG5n?x-oss-process=image/format,png)

### 五. 文件存储格式

Hive支持的存储数的格式主要有：TEXTFILE 、SEQUENCEFILE、ORC、PARQUET。

简写：
* 行储存：textFile 、 sequencefile 、
* 列储存：orc 、parquet

#### 1.列式存储和行式存储
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWUyYmMyYzJlMDhjZGI3YTIucG5n?x-oss-process=image/format,png)

上图左边为逻辑表，右边第一个为行式存储，第二个为列式存储。
##### (1).行存储的特点： 
&nbsp;&nbsp;&nbsp;&nbsp;查询满足条件的一整行数据的时候，列存储则需要去每个聚集的字段找到对应的每个列的值，行存储只需要找到其中一个值，其余的值都在相邻地方，所以此时行存储查询的速度更快。
##### (2).列存储的特点：
&nbsp;&nbsp;&nbsp;&nbsp;因为每个字段的数据聚集存储，在查询只需要少数几个字段的时候，能大大减少读取的数据量；每个字段的数据类型一定是相同的，列式存储可以针对性的设计更好的设计压缩算法。

&nbsp;&nbsp;&nbsp;&nbsp;**TEXTFILE和SEQUENCEFILE的存储格式都是基于行存储的；**
&nbsp;&nbsp;&nbsp;&nbsp;**ORC和PARQUET是基于列式存储的**

#### 2.TextFile格式
&nbsp;&nbsp;&nbsp;&nbsp;默认格式，数据不做压缩，磁盘开销大，数据解析开销大。可结合Gzip、Bzip2使用，但使用Gzip这种方式，hive不会对数据进行切分，从而无法对数据进行并行操作。

#### 3.Orc格式

&nbsp;&nbsp;&nbsp;&nbsp;Orc (Optimized Row Columnar)是Hive 0.11版里引入的新的存储格式。

&nbsp;&nbsp;&nbsp;&nbsp;可以看到每个Orc文件由1个或多个stripe组成，每个stripe250MB大小，这个Stripe实际相当于RowGroup概念，不过大小由4MB->250MB，这样应该能提升顺序读的吞吐率。每个Stripe里有三部分组成，分别是Index Data，Row Data，Stripe Footer：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUzMTU4ZTYyMGM0MGE2ZTYucG5n?x-oss-process=image/format,png)



 ##### (1)Index Data：
 一个轻量级的index，默认是每隔1W行做一个索引。这里做的索引应该只是记录某行的各字段在Row Data中的offset。

##### (2)Row Data：
存的是具体的数据，先取部分行，然后对这些行按列进行存储。对每个列进行了编码，分成多个Stream来存储。

##### (3)Stripe Footer：
存的是各个Stream的类型，长度等信息。

&nbsp;&nbsp;&nbsp;&nbsp;每个文件有一个File Footer，这里面存的是每个Stripe的行数，每个Column的数据类型信息等；每个文件的尾部是一个PostScript，这里面记录了整个文件的压缩类型以及FileFooter的长度信息等。在读取文件时，会seek到文件尾部读PostScript，从里面解析到File Footer长度，再读FileFooter，从里面解析到各个Stripe信息，再读各个Stripe，即从后往前读。

#### 4.Parquet格式

&nbsp;&nbsp;&nbsp;&nbsp;Parquet是面向分析型业务的列式存储格式，由Twitter和Cloudera合作开发，2015年5月从Apache的孵化器里毕业成为Apache顶级项目。

&nbsp;&nbsp;&nbsp;&nbsp;Parquet文件是以二进制方式存储的，所以是不可以直接读取的，文件中包括该文件的数据和元数据，因此Parquet格式文件是自解析的。

&nbsp;&nbsp;&nbsp;&nbsp;通常情况下，在存储Parquet数据的时候会按照Block大小设置行组的大小，由于一般情况下每一个Mapper任务处理数据的最小单位是一个Block，这样可以把每一个行组由一个Mapper任务处理，增大任务执行并行度。Parquet文件的格式如下图所示。


![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJjMzI5Mzc4Mzc3OWI1NWYucG5n?x-oss-process=image/format,png)


&nbsp;&nbsp;&nbsp;&nbsp;上图展示了一个Parquet文件的内容，一个文件中可以存储多个行组，文件的首位都是该文件的Magic Code，用于校验它是否是一个Parquet文件，Footer length记录了文件元数据的大小，通过该值和文件长度可以计算出元数据的偏移量，文件的元数据中包括每一个行组的元数据信息和该文件存储数据的Schema信息。除了文件中每一个行组的元数据，每一页的开始都会存储该页的元数据，在Parquet中，有三种类型的页：**数据页、字典页和索引页**。数据页用于存储当前行组中该列的值，字典页存储该列值的编码字典，每一个列块中最多包含一个字典页，索引页用来存储当前行组下该列的索引，目前Parquet中还不支持索引页。

#### 5.主流文件存储格式对比实验

从存储文件的压缩比和查询速度两个角度对比。

(一)**存储文件的压缩比测试：**

##### (0).测试数据(log.data 大小为18.1MB)

##### (1).TextFile

###### (a)创建表，存储数据格式为TEXTFILE
```shell
create table log_text (

track_time string,

url string,

session_id string,

referer string,

ip string,

end_user_id string,

city_id string

)

row format delimited fields terminated by '\t'

stored as textfile ;
```
###### (b)向表中加载数据
```shell
hive (default)> load data local inpath '/opt/module/datas/log.data' into table log_text ;
```

###### (c )查看表中数据大小
```shell
hive (default)> dfs -du -h /user/hive/warehouse/log_text;
```

18.1 M  /user/hive/warehouse/log_text/log.data

##### (2).ORC

###### (a)创建表，存储数据格式为ORC
```shell
create table log_orc(

track_time string,

url string,

session_id string,

referer string,

ip string,

end_user_id string,

city_id string

)

row format delimited fields terminated by '\t'

stored as orc ;
```
###### (b)向表中加载数据
```shell
hive (default)> insert into table log_orc select * from log_text ;
```
###### (c )查看表中数据大小
```shell
hive (default)> dfs -du -h /user/hive/warehouse/log_orc/ ;
```
2.8 M  /user/hive/warehouse/log_orc/000000_0

##### (3).Parquet

###### (a)创建表，存储数据格式为parquet
```shell
create table log_parquet(

track_time string,

url string,

session_id string,

referer string,

ip string,

end_user_id string,

city_id string

)

row format delimited fields terminated by '\t'

stored as parquet ;  
```
###### (b)向表中加载数据
```shell
hive (default)> insert into table log_parquet select * from log_text ;
```
###### (c )查看表中数据大小
```shell
hive (default)> dfs -du -h /user/hive/warehouse/log_parquet/ ;
```
13.1 M  /user/hive/warehouse/log_parquet/000000_0


存储文件的压缩比总结：

ORC >  Parquet >  textFile

(二)**存储文件的查询速度测试：**

##### (1).TextFile
```shell
hive (default)> select count(*) from log_text;
```
_c0
100000
Time taken: 21.54 seconds, Fetched: 1 row(s)
Time taken: 21.08 seconds, Fetched: 1 row(s)

##### (2).ORC
```shell
hive (default)> select count(*) from log_orc;
```
_c0
100000
Time taken: 20.867 seconds, Fetched: 1 row(s)
Time taken: 22.667 seconds, Fetched: 1 row(s)

##### (3).Parquet
```shell
hive (default)> select count(*) from log_parquet;
```
_c0
100000
Time taken: 22.922 seconds, Fetched: 1 row(s)
Time taken: 21.074 seconds, Fetched: 1 row(s)

**存储文件的查询速度总结：查询速度相近。**

### 六.存储和压缩结合

#### 1.修改Hadoop集群具有Snappy压缩方式

##### (1).查看hadoop checknative命令使用

```shell
hadoop checknative [-a|-h]  check native hadoop and compression libraries availability
```
##### (2).查看hadoop支持的压缩方式
```shell
hadoop checknative
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc2OWQyOGMyOTNjY2YzOTkucG5n?x-oss-process=image/format,png)

##### (3).将编译好的支持Snappy压缩的hadoop-2.8.4.tar.gz包导入到bigdata111的/opt/software中

##### (4).解压hadoop-2.8.4.tar.gz到当前路径
```shell
tar -zxvf hadoop-2.8.4.tar.gz
```
##### (5).进入到/opt/software/hadoop-2.8.4/lib/native路径可以看到支持Snappy压缩的动态链接库
```shell
pwd

ll
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZiZmQwYzUyZjVkODc4MzUucG5n?x-oss-process=image/format,png)


##### (6).拷贝/opt/software/hadoop-2.8.4/lib/native里面的所有内容到开发集群的/opt/module/hadoop-2.8.4/lib/native路径上
```shell
cp ../native/* /opt/module/hadoop-2.8.4/lib/native/
```
##### (7).分发集群 scp 到其他集群目录
```shell
scp - r ./native/ root@主机名:绝对路径
```
##### (8).再次查看hadoop支持的压缩类型
```shell
hadoop checknative
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU4YjM0OGYzODg3OTA1MzMucG5n?x-oss-process=image/format,png)


##### (9).重新启动hadoop集群和hive

#### 2.测试存储和压缩

官网:https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC

ORC存储方式的压缩：

|Key|Default|Notes|
|:-:|:-:|:-:|
|orc.compress|ZLIB|high level compression (one of NONE, ZLIB, SNAPPY)|
|orc.compress.size|262,144|number of bytes in each compression chunk|
|orc.stripe.size|67,108,864|number of bytes in each stripe|
|orc.row.index.stride|10,000|number of rows between index entries (must be >= 1000)|
|orc.create.index|true|whether to create row indexes|
|orc.bloom.filter.columns|""|comma separated list of column names for which bloom filter should be created|
|orc.bloom.filter.fpp|0.05| false positive probability for bloom filter (must >0.0 and <1.0)|

##### (1).创建一个非压缩的的ORC存储方式

###### (a)建表语句
```shell
create table log_orc_none(

track_time string,

url string,

session_id string,

referer string,

ip string,

end_user_id string,

city_id string

)

row format delimited fields terminated by '\t'

stored as orc tblproperties ("orc.compress"="NONE");
```
###### (b)插入数据
```shell
hive (default)> insert into table log_orc_none select * from log_text ;
```
###### (c )查看插入后数据
```shell
hive (default)> dfs -du -h /user/hive/warehouse/log_orc_none/ ;
```
7.7 M  /user/hive/warehouse/log_orc_none/000000_0

##### (2).创建一个SNAPPY压缩的ORC存储方式

###### (a)建表语句
```shell
create table log_orc_snappy(

track_time string,

url string,

session_id string,

referer string,

ip string,

end_user_id string,

city_id string

)

row format delimited fields terminated by '\t'

stored as orc tblproperties ("orc.compress"="SNAPPY");
```
###### (b)插入数据
```shell
hive (default)> insert into table log_orc_snappy select * from log_text ;
```
###### (c )查看插入后数据
```shell
hive (default)> dfs -du -h /user/hive/warehouse/log_orc_snappy/ ;
```

3.8 M  /user/hive/warehouse/log_orc_snappy/000000_0

##### (3).上一节中默认创建的ORC存储方式，导入数据后的大小为

2.8 M  /user/hive/warehouse/log_orc/000000_0

&nbsp;&nbsp;&nbsp;&nbsp;比Snappy压缩的还小。原因是orc存储文件默认采用ZLIB压缩。比snappy压缩的小。

##### (4).存储方式和压缩总结:

&nbsp;&nbsp;&nbsp;&nbsp;在实际的项目开发当中，hive表的数据存储格式一般选择：orc或parquet。压缩方式一般选择snappy，lzo。
