---
title: '[Hadoop]-HDFS的读写数据流程'
date: 2019-01-01 14:49:00
tags: Hadoop
categories: Hadoop
---
### 一.HDFS写数据流程

#### 1.剖析文件写入
![HDFS的写数据流程](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQxNTJlNGQ2MzQ5MjY0MDIucG5n?x-oss-process=image/format,png)

(1)客户端向namenode请求上传文件，namenode检查目标文件是否已存在，父目录是否存在。

(2)namenode返回是否可以上传。

(3)客户端请求第一个 block上传到哪几个datanode服务器上。

(4)namenode返回3个datanode节点，分别为dn1、dn2、dn3。

(5)客户端请求dn1上传数据，dn1收到请求会继续调用dn2，然后dn2调用dn3，将这个通信管道建立完成

(6)dn1、dn2、dn3逐级应答客户端

(7)客户端开始往dn1上传第一个block（先从磁盘读取数据放到一个本地内存缓存），以packet为单位，dn1收到一个packet就会传给dn2，dn2传给dn3；dn1每传一个packet会放入一个应答队列等待应答

(8)当一个block传输完成之后，客户端再次请求namenode上传第二个block的服务器。（重复执行3-7步）

![文件的写入](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgxNDQxNGNlMWMyNGE2ZWUucG5n?x-oss-process=image/format,png)

```shell
1.	客户端通过调用DistributedFileSystem的create方法创建新文件。

2.	DistributedFileSystem通过RPC调用namenode去创建一个没有blocks关联的新文件，创建前， namenode会做各种校验，比如文件是否存在，客户端有无权限去创建等。如果校验通过， namenode就会记录下新文件，否则就会抛出IO异常。

3.	前两步结束后，会返回FSDataOutputStream的对象，与读文件的时候相似， FSDataOutputStream被封装成DFSOutputStream。DFSOutputStream可以协调namenode和 datanode。客户端开始写数据到DFSOutputStream，DFSOutputStream会把数据切成一个个小的packet，然后排成队 列data quene（数据队列）。

4.	DataStreamer会去处理接受data quene，它先询问namenode这个新的block最适合存储的在哪几个datanode里（比如重复数是3，那么就找到3个最适合的 datanode），把他们排成一个pipeline。DataStreamer把packet按队列输出到管道的第一个datanode中，第一个 datanode又把packet输出到第二个datanode中，以此类推。

5.	DFSOutputStream还有一个对列叫ack quene，也是由packet组成，等待datanode的收到响应，当pipeline中的所有datanode都表示已经收到的时候，这时ack quene才会把对应的packet包移除掉。 
如果在写的过程中某个datanode发生错误，会采取以下几步： 
  (1)pipeline被关闭掉； 
  (2)	为了防止防止丢包ack quene里的packet会同步到data quene里； 
  (3)把产生错误的datanode上当前在写但未完成的block删掉； 
  (4)block剩下的部分被写到剩下的两个正常的datanode中； 
  (5)namenode找到另外的datanode去创建这个块的复制。当然，这些操作对客户端来说是无感知的。

6.	客户端完成写数据后调用close方法关闭写入流。

7.	DataStreamer把剩余得包都刷到pipeline里，然后等待ack信息，收到最后一个ack后，通知datanode把文件标视为已完成。

    注意：客户端执行write操作后，写完的block才是可见的(注:和下面的一致性所对应)，正在写的block对客户端是不可见的，只有 调用sync方法，客户端才确保该文件的写操作已经全部完成，当客户端调用close方法时，会默认调用sync方法。是否需要手动调用取决你根据程序需 要在数据健壮性和吞吐率之间的权衡
```

### 二.HDFS读数据流程

![HDFS读数据流程](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU3NDUxNzNlMmE1NjdjOTcucG5n?x-oss-process=image/format,png)

(1)客户端向namenode请求下载文件，namenode通过查询元数据，找到文件块所在的datanode地址。

(2)挑选一台datanode（就近原则，然后随机）服务器，请求读取数据。

(3)datanode开始传输数据给客户端（从磁盘里面读取数据放入流，以packet为单位来做校验）。

(4)客户端以packet为单位接收，先在本地缓存，然后写入目标文件

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTYwNGExYTJiNmYyMWU2ZjAucG5n?x-oss-process=image/format,png)

```
1.	首先调用FileSystem对象的open方法，其实是一个DistributedFileSystem的实例。
2.	DistributedFileSystem通过rpc获得文件的第一批block的locations，同一个block按照重复数会返回多个locations，这些locations按照hadoop拓扑结构排序，距离客户端近的排在前面。
3.	前两步会返回一个FSDataInputStream对象，该对象会被封装DFSInputStream对象，DFSInputStream可 以方便的管理datanode和namenode数据流。客户端调用read方法，DFSInputStream最会找出离客户端最近的datanode 并连接。
4.	数据从datanode源源不断的流向客户端。
5.	如果第一块的数据读完了，就会关闭指向第一块的datanode连接，接着读取下一块。这些操作对客户端来说是透明的，客户端的角度看来只是读一个持续不断的流。
6.	如果第一批block都读完了， DFSInputStream就会去namenode拿下一批block的locations，然后继续读，如果所有的块都读完，这时就会关闭掉所有的流。 
7.	如果在读数据的时候， DFSInputStream和datanode的通讯发生异常，就会尝试正在读的block的排序第二近的datanode,并且会记录哪个 datanode发生错误，剩余的blocks读的时候就会直接跳过该datanode。 DFSInputStream也会检查block数据校验和，如果发现一个坏的block,就会先报告到namenode节点，然后 DFSInputStream在其他的datanode上读该block的镜像。
8.	该设计就是客户端直接连接datanode来检索数据并且namenode来负责为每一个block提供最优的datanode， namenode仅仅处理block location的请求，这些信息都加载在namenode的内存中，hdfs通过datanode集群可以承受大量客户端的并发访问。
```


### 三.一致性模型
(1)debug调试如下代码

```java
    @Test
	public void writeFile() throws Exception{
		// 1 创建配置信息对象
		Configuration configuration = new Configuration();
		fs = FileSystem.get(configuration);
		
		// 2 创建文件输出流
		Path path = new Path("F:\\date\\H.txt");
		FSDataOutputStream fos = fs.create(path);
		
		// 3 写数据
		fos.write("hello Andy".getBytes());
        // 4 一致性刷新
		fos.hflush();
		
		fos.close();
	}
```
(2)总结
写入数据时，如果希望数据被其他client立即可见，调用如下方法

```java
FSDataOutputStream. hflush ();		//清理客户端缓冲区数据，被其他client立即可见
```