---
title: 'HBase优化'
date: 2019-04-08 15:00:00
tags: HBase
categories: HBase
---

#### 1.高可用

&nbsp;&nbsp;&nbsp;&nbsp;在HBase中Hmaster负责监控RegionServer的生命周期，均衡RegionServer的负载，如果Hmaster挂掉了，那么整个HBase集群将陷入不健康的状态，并且此时的工作状态并不会维持太久。所以HBase支持对Hmaster的高可用配置。

##### (1).关闭HBase集群(如果没有开启则跳过此步)
```shell
$ bin/stop-hbase.sh
```

##### (2).在conf目录下创建backup-masters文件
```shell
$ touch conf/backup-masters
```
##### (3).在backup-masters文件中配置高可用HMaster节点
```shell
$ echo hadoop2 >  conf/backup-masters
```
##### (4).将整个conf目录scp到其他节点
```shell
$ scp -r conf/ hadoop2:/opt/module/hbase-1.3.1

$ scp -r conf/ hadoop3:/opt/module/hbase-1.3.1
```

##### (5).重新启动HBase后打开页面测试查看
&nbsp;&nbsp;&nbsp;&nbsp;0.98版本之后：http://bigdata111:16010

#### 2.Hadoop的通用性优化

##### (1).NameNode元数据备份使用SSD

##### (2).定时备份NameNode上的元数据

&nbsp;&nbsp;&nbsp;&nbsp;每小时或者每天备份，如果数据极其重要，可以5~10分钟备份一次。备份可以通过定时任务复制元数据目录即可。

##### (3).为NameNode指定多个元数据目录

&nbsp;&nbsp;&nbsp;&nbsp;使用dfs.name.dir或者dfs.namenode.name.dir指定。这样可以提供元数据的冗余和健壮性，以免发生故障。

##### (4).NameNode的dir自恢复

&nbsp;&nbsp;&nbsp;&nbsp;设置dfs.namenode.name.dir.restore为true，允许尝试恢复之前失败的dfs.namenode.name.dir目录，在创建checkpoint时做此尝试，如果设置了多个磁盘，建议允许。

##### (5).HDFS保证RPC调用会有较多的线程数

hdfs-site.xml
```shell
属性：dfs.namenode.handler.count

解释：该属性是NameNode服务默认线程数，的默认值是10，根据机器的可用内存可以调整为50~100

属性：dfs.datanode.handler.count

解释：该属性默认值为10，是DataNode的处理线程数，如果HDFS客户端程序读写请求比较多，可以调高到15~20，设置的值越大，内存消耗越多，不要调整的过高，一般业务中，5~10即可。
```
##### (6).HDFS副本数的调整

hdfs-site.xml
```shell
属性：dfs.replication

解释：如果数据量巨大，且不是非常之重要，可以调整为2~3，如果数据非常之重要，可以调整为3~5。
```
##### (7).HDFS文件块大小的调整

hdfs-site.xml
```shell
属性：dfs.blocksize

解释：块大小定义，该属性应该根据存储的大量的单个文件大小来设置，如果大量的单个文件都小于100M，建议设置成64M块大小，对于大于100M或者达到GB的这种情况，建议设置成256M，一般设置范围波动在64M~256M之间。
```
##### (8).MapReduce Job任务服务线程数调整

mapred-site.xml
```shell
属性：mapreduce.jobtracker.handler.count

解释：该属性是Job任务线程数，默认值是10，根据机器的可用内存可以调整为50~100
```
##### (9).Http服务器工作线程数

mapred-site.xml
```shell
属性：mapreduce.tasktracker.http.threads

解释：定义HTTP服务器工作线程数，默认值为40，对于大集群可以调整到80~100
```
##### (10).文件排序合并优化

mapred-site.xml
```shell
属性：mapreduce.task.io.sort.factor

解释：文件排序时同时合并的数据流的数量，这也定义了同时打开文件的个数，默认值为10，如果调高该参数，可以明显减少磁盘IO，即减少文件读取的次数。
```
##### (11).设置任务并发

mapred-site.xml
```shell
属性：mapreduce.map.speculative

解释：该属性可以设置任务是否可以并发执行，如果任务多而小，该属性设置为true可以明显加快任务执行效率，但是对于延迟非常高的任务，建议改为false，这就类似于迅雷下载。
```
##### (12).MR输出数据的压缩

mapred-site.xml
```shell
属性：mapreduce.map.output.compress、mapreduce.output.fileoutputformat.compress

解释：对于大集群而言，建议设置Map-Reduce的输出为压缩的数据，而对于小集群，则不需要。
```
##### (13).优化Mapper和Reducer的个数

mapred-site.xml
```shell
属性：

mapreduce.tasktracker.map.tasks.maximum

mapreduce.tasktracker.reduce.tasks.maximum

解释：以上两个属性分别为一个单独的Job任务可以同时运行的Map和Reduce的数量。

设置上面两个参数时，需要考虑CPU核数、磁盘和内存容量。假设一个8核的CPU，业务内容非常消耗CPU，那么可以设置map数量为4，如果该业务不是特别消耗CPU类型的，那么可以设置map数量为40，reduce数量为20。这些参数的值修改完成之后，一定要观察是否有较长等待的任务，如果有的话，可以减少数量以加快任务执行，如果设置一个很大的值，会引起大量的上下文切换，以及内存与磁盘之间的数据交换，这里没有标准的配置数值，需要根据业务和硬件配置以及经验来做出选择。

在同一时刻，不要同时运行太多的MapReduce，这样会消耗过多的内存，任务会执行的非常缓慢，我们需要根据CPU核数，内存容量设置一个MR任务并发的最大值，使固定数据量的任务完全加载到内存中，避免频繁的内存和磁盘数据交换，从而降低磁盘IO，提高性能。
```
大概估算公式：

map = 2 + ⅔cpu_core
reduce = 2 + ⅓cpu_core


#### 3.Linux优化

##### (1).开启文件系统的预读缓存可以提高读取速度
```shell
$ sudo blockdev --setra 32768 /dev/sda
```
提示：ra是readahead的缩写

##### (2).关闭进程睡眠池

即不允许后台进程进入睡眠状态，如果进程空闲，则直接kill掉释放资源
```shell
$ sudo sysctl -w vm.swappiness=0
```
##### (3).调整ulimit上限，默认值为比较小的数字
```shell
$ ulimit -n 查看允许最大进程数

$ ulimit -u 查看允许打开最大文件数
```
优化修改：
```shell
$ sudo vi /etc/security/limits.conf 修改打开文件数限制
```
末尾添加：
```shell
*                soft    nofile          1024000

*                hard    nofile          1024000

Hive             -       nofile          1024000

hive             -       nproc           1024000 
```

修改用户打开进程数限制
```shell
$ sudo vi /etc/security/limits.d/20-nproc.conf 
```
修改为：
```shell
#*          soft    nproc     4096

#root       soft    nproc     unlimited

*          soft    nproc     40960

root       soft    nproc     unlimited
```

##### (4).开启集群的时间同步NTP

&nbsp;&nbsp;&nbsp;&nbsp;集群中某台机器同步网络时间服务器的时间，集群中其他机器则同步这台机器的时间。

##### (5).更新系统补丁

&nbsp;&nbsp;&nbsp;&nbsp;更新补丁前，请先测试新版本补丁对集群节点的兼容性。

#### 4.Zookeeper优化

##### (1).优化Zookeeper会话超时时间

hbase-site.xml
```shell
参数：zookeeper.session.timeout

解释：In hbase-site.xml, set zookeeper.session.timeout to 30 seconds or less to bound failure detection (20-30 seconds is a good start).该值会直接关系到master发现服务器宕机的最大周期，默认值为30秒（不同的HBase版本，该默认值不一样），如果该值过小，会在HBase在写入大量数据发生而GC时，导致RegionServer短暂的不可用，从而没有向ZK发送心跳包，最终导致认为从节点shutdown。一般20台左右的集群需要配置5台zookeeper。
```
#### 5.HBase优化

##### (1).预分区

&nbsp;&nbsp;&nbsp;&nbsp;每一个region维护着startRow与endRowKey，如果加入的数据符合某个region维护的rowKey范围，则该数据交给这个region维护。那么依照这个原则，我们可以将数据索要投放的分区提前大致的规划好，以提高HBase性能。

###### (a).手动设定预分区
```shell
hbase> create 'staff','info','partition1',SPLITS => ['1000','2000','3000','4000']
```
###### (b).生成16进制序列预分区
```shell
create 'staff2','info','partition2',{NUMREGIONS => 15, SPLITALGO => 'HexStringSplit'}
```
###### (c ).按照文件中设置的规则预分区

创建splits.txt文件内容如下：
spilts.txt
```txt
aaaa

bbbb

cccc

dddd
```

然后执行：
```shell
create 'staff3','partition3',SPLITS_FILE => '/opt/module/hbase-1.3.1/splits.txt'
```
###### (d).使用JavaAPI创建预分区

```shell
//自定义算法，产生一系列Hash散列值存储在二维数组中

byte[][] splitKeys = 某个散列值函数

//创建HBaseAdmin实例

HBaseAdmin hAdmin = new HBaseAdmin(HBaseConfiguration.create());

//创建HTableDescriptor实例

HTableDescriptor tableDesc = new HTableDescriptor(tableName);

//通过HTableDescriptor实例和散列值二维数组创建带有预分区的HBase表

hAdmin.createTable(tableDesc, splitKeys);
```

##### (2).RowKey设计

&nbsp;&nbsp;&nbsp;&nbsp;一条数据的唯一标识就是rowkey，那么这条数据存储于哪个分区，取决于rowkey处于哪个一个预分区的区间内，设计rowkey的主要目的 ，就是让数据均匀的分布于所有的region中，在一定程度上防止数据倾斜。接下来我们就谈一谈rowkey常用的设计方案。

###### (a).生成随机数,hash、散列值
```shell
比如：

原本rowKey为1001的，SHA1后变成：dd01903921ea24941c26a48f2cec24e0bb0e8cc7

原本rowKey为3001的，SHA1后变成：49042c54de64a1e9bf0b33e00245660ef92dc7bd

原本rowKey为5001的，SHA1后变成：7b61dec07e02c188790670af43e717f0f46e8913

在做此操作之前，一般我们会选择从数据集中抽取样本，来决定什么样的rowKey来Hash后作为每个分区的临界值。
```
###### (b).字符串反转
```shell
20170524000001转成10000042507102

20170524000002转成20000042507102
```
这样也可以在一定程度上散列逐步put进来的数据。

###### (c ).字符串拼接
```
20170524000001_a12e

20170524000001_93i7
```
##### (3).内存优化

&nbsp;&nbsp;&nbsp;&nbsp;HBase操作过程中需要大量的内存开销，毕竟Table是可以缓存在内存中的，一般会分配整个可用内存的70%给HBase的Java堆。但是不建议分配非常大的堆内存，因为GC过程持续太久会导致RegionServer处于长期不可用状态，一般16~48G内存就可以了，如果因为框架占用内存过高导致系统内存不足，框架一样会被系统服务拖死。

##### (4).基础优化

###### (a).允许在HDFS的文件中追加内容

不是不允许追加内容么？没错，请看背景故事：

[http://blog.cloudera.com/blog/2009/07/file-appends-in-hdfs/](http://blog.cloudera.com/)

hdfs-site.xml、hbase-site.xml

```shell
属性：dfs.support.append

解释：开启HDFS追加同步，可以优秀的配合HBase的数据同步和持久化。默认值为true。
```
###### (b).优化DataNode允许的最大文件打开数

hdfs-site.xml
```shell
属性：dfs.datanode.max.transfer.threads

解释：HBase一般都会同一时间操作大量的文件，根据集群的数量和规模以及数据动作，设置为4096或者更高。默认值：4096
```
###### (c ).优化延迟高的数据操作的等待时间

hdfs-site.xml
```shell
属性：dfs.image.transfer.timeout

解释：如果对于某一次数据操作来讲，延迟非常高，socket需要等待更长的时间，建议把该值设置为更大的值（默认60000毫秒），以确保socket不会被timeout掉。
```

###### (d).优化数据的写入效率

mapred-site.xml
```shell
属性：

mapreduce.map.output.compress

mapreduce.map.output.compress.codec

解释：开启这两个数据可以大大提高文件的写入效率，减少写入时间。第一个属性值修改为true，第二个属性值修改为：org.apache.hadoop.io.compress.GzipCodec或者其他压缩方式。
```
###### (e).优化DataNode存储
```shell
属性：dfs.datanode.failed.volumes.tolerated

解释： 默认为0，意思是当DataNode中有一个磁盘出现故障，则会认为该DataNode shutdown了。如果修改为1，则一个磁盘出现故障时，数据会被复制到其他正常的DataNode上，当前的DataNode继续工作。
```
###### (f).设置RPC监听数量

hbase-site.xml
```shell
属性：hbase.regionserver.handler.count

解释：默认值为30，用于指定RPC监听的数量，可以根据客户端的请求数进行调整，读写请求较多时，增加此值。
```
###### (g).优化HStore文件大小

hbase-site.xml
```shell
属性：hbase.hregion.max.filesize

解释：默认值10737418240（10GB），如果需要运行HBase的MR任务，可以减小此值，因为一个region对应一个map任务，如果单个region过大，会导致map任务执行时间过长。该值的意思就是，如果HFile的大小达到这个数值，则这个region会被切分为两个Hfile。
```
###### (h).优化hbase客户端缓存

hbase-site.xml
```shell
属性：hbase.client.write.buffer

解释：用于指定HBase客户端缓存，增大该值可以减少RPC调用次数，但是会消耗更多内存，反之则反之。一般我们需要设定一定的缓存大小，以达到减少RPC次数的目的。
```
###### (i).指定scan.next扫描HBase所获取的行数

hbase-site.xml
```shell
属性：hbase.client.scanner.caching

解释：用于指定scan.next方法获取的默认行数，值越大，消耗内存越大。
```
###### (j).flush、compact、split机制

&nbsp;&nbsp;&nbsp;当MemStore达到阈值，将Memstore中的数据Flush进Storefile；compact机制则是把flush出来的小文件合并成大的Storefile文件。split则是当Region达到阈值，会把过大的Region一分为二。

涉及属性：

即：128M就是Memstore的默认阈值
```shell
hbase.hregion.memstore.flush.size：134217728
```

即：这个参数的作用是当单个HRegion内所有的Memstore大小总和超过指定值时，flush该HRegion的所有memstore。RegionServer的flush是通过将请求添加一个队列，模拟生产消费模型来异步处理的。那这里就有一个问题，当队列来不及消费，产生大量积压请求时，可能会导致内存陡增，最坏的情况是触发OOM。

```shell
hbase.regionserver.global.memstore.upperLimit：0.4

hbase.regionserver.global.memstore.lowerLimit：0.38
```

即：当MemStore使用内存总量达到hbase.regionserver.global.memstore.upperLimit指定值时，将会有多个MemStores flush到文件中，MemStore flush 顺序是按照大小降序执行的，直到刷新到MemStore使用内存略小于lowerLimit
