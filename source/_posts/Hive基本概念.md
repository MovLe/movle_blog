---
title: 'Hive基本概念'
date: 2019-07-15 11:00:00
tags: Hive
categories: Hive
---

### 一.Hive基本概念：
#### 1. Hive
##### (1).官网：http://hive.apache.org/

##### (2).Apache HiveTM数据仓库软件有助于使用SQL读取，编写和管理驻留在分布式存储中的 大型数据集。可以将结构投影到已存储的数据中。提供了命令行工具和JDBC驱动程序以 将用户连接到Hive。

##### (3).hive提供了SQL查询功能 hdfs分布式存储

##### (4).hive本质HQL转化为MapReduce程序。

##### (5).环境前提：

* 启动hdfs集群
* 启动yarn集群

如果想用hive的话，需要提前安装部署好hadoop集群。

#### 2.为什么要学习Hive
##### (1).简化开发

##### (2).优势：

###### (a)操作接口采用类SQL语法，提供快速开发的能力（简单、容易上手）
###### (b)避免了去写MapReduce，减少开发人员的学习成本。

###### (c )Hive的执行延迟比较高，**因此Hive常用于数据分析，对实时性要求不高的场合**
###### (d)Hive优势在于处理大数据，**对于处理小数据没有优势，因为Hive的执行延迟比较高**
###### (e)Hive支持用户自定义函数，用户可以根据自己的需求来实现自己的函数。


#### 3.劣势：
##### (1)Hive的HQL表达能力有限
* 迭代式算法无法表达
* 数据挖掘方面不擅长

##### (2)Hive的效率比较低
* Hive自动生成的MapReduce作业，通常情况下不够智能化
* Hive调优比较困难，粒度较粗


#### 3.Hive架构原理：


sql->转换->mapreduce->job

![Hive架构原理](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTYxZTllNDdlZjQ0MmM2NGIucG5n?x-oss-process=image/format,png)

![Hive架构](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ4MTE5MzczZDk2MmM2MDQucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;如图中所示，Hive通过给用户提供的一系列交互接口，接收到用户的指令(SQL)，使用自己的Driver，结合元数据(MetaStore)，将这些指令翻译成MapReduce，提交到Hadoop中执行，最后，将执行返回的结果输出到用户交互接口。

##### (1).用户接口：Client

CLI(hive shell)、JDBC/ODBC(java访问hive)、WEBUI(浏览器访问hive)

##### (2).元数据：Metastore
&nbsp;&nbsp;&nbsp;&nbsp;元数据包括：表名、表所属的数据库（默认是default）、表的拥有者、列/分区字段、表的类型（是否是外部表）、表的数据所在目录等；
&nbsp;&nbsp;&nbsp;&nbsp;默认存储在自带的derby数据库中，推荐使用MySQL存储Metastore

##### (3).Hadoop
&nbsp;&nbsp;&nbsp;&nbsp;使用HDFS进行存储，使用MapReduce进行计算。

##### (4).驱动器：Driver
###### (a)解析器(SQL Parser)：
将SQL字符串转换成抽象语法树AST，这一步一般都用第三方工具库完成，比如antlr；对AST进行语法分析，比如表是否存在、字段是否存在、SQL语义是否有误。

###### (b)编译器(Physical Plan)：
将AST编译生成逻辑执行计划。

###### (c )优化器(Query Optimizer)：
对逻辑执行计划进行优化。

###### (d)执行器(Execution)：
把逻辑执行计划转换成可以运行的物理计划。对于Hive来说，就是MR/Spark。

#### 4.Hive和数据库比较

&nbsp;&nbsp;&nbsp;&nbsp;由于 Hive 采用了类似SQL 的查询语言 HQL(Hive Query Language)，因此很容易将 Hive 理解为数据库。其实从结构上来看，Hive 和数据库除了拥有类似的查询语言，再无类似之处。本文将从多个方面来阐述 Hive 和数据库的差异。数据库可以用在 Online 的应用中，但是Hive 是为数据仓库而设计的，清楚这一点，有助于从应用角度理解 Hive 的特性。

##### (1).查询语言

&nbsp;&nbsp;&nbsp;&nbsp;由于SQL被广泛的应用在数据仓库中，因此，专门针对Hive的特性设计了类SQL的查询语言HQL。熟悉SQL开发的开发者可以很方便的使用Hive进行开发。

##### (2).数据存储位置

&nbsp;&nbsp;&nbsp;&nbsp;Hive 是建立在 Hadoop 之上的，所有 Hive 的数据都是存储在 HDFS 中的。而数据库则可以将数据保存在块设备或者本地文件系统中。

##### (3). 数据更新

&nbsp;&nbsp;&nbsp;&nbsp;由于Hive是针对数据仓库应用设计的，而数据仓库的内容是读多写少的。因此，Hive中不支持对数据的改写和添加，所有的数据都是在加载的时候确定好的。而数据库中的数据通常是需要经常进行修改的，因此可以使用 INSERT INTO …  VALUES 添加数据，使用 UPDATE … SET修改数据。

##### (4). 索引

&nbsp;&nbsp;&nbsp;&nbsp;Hive在加载数据的过程中不会对数据进行任何处理，甚至不会对数据进行扫描，因此也没有对数据中的某些Key建立索引。Hive要访问数据中满足条件的特定值时，需要暴力扫描整个数据，因此访问延迟较高。由于 MapReduce 的引入， Hive 可以并行访问数据，因此即使没有索引，对于大数据量的访问，Hive 仍然可以体现出优势。数据库中，通常会针对一个或者几个列建立索引，因此对于少量的特定条件的数据的访问，数据库可以有很高的效率，较低的延迟。由于数据的访问延迟较高，决定了 Hive 不适合在线数据查询。

##### (5). 执行

&nbsp;&nbsp;&nbsp;&nbsp;Hive中大多数查询的执行是通过 Hadoop 提供的 MapReduce 来实现的。而数据库通常有自己的执行引擎。

##### (6). 执行延迟

&nbsp;&nbsp;&nbsp;&nbsp;Hive 在查询数据的时候，由于没有索引，需要扫描整个表，因此延迟较高。另外一个导致 Hive 执行延迟高的因素是 MapReduce框架。由于MapReduce 本身具有较高的延迟，因此在利用MapReduce 执行Hive查询时，也会有较高的延迟。相对的，数据库的执行延迟较低。当然，这个低是有条件的，即数据规模较小，当数据规模大到超过数据库的处理能力的时候，Hive的并行计算显然能体现出优势。

##### (7).可扩展性

&nbsp;&nbsp;&nbsp;&nbsp;由于Hive是建立在Hadoop之上的，因此Hive的可扩展性是和Hadoop的可扩展性是一致的(世界上最大的Hadoop 集群在 Yahoo!，2009年的规模在4000 台节点左右)。而数据库由于 ACID 语义的严格限制，扩展行非常有限。目前最先进的并行数据库Oracle在理论上的扩展能力也只有100台左右。

##### (8). 数据规模

&nbsp;&nbsp;&nbsp;&nbsp;由于Hive建立在集群上并可以利用MapReduce进行并行计算，因此可以支持很大规模的数据；对应的，数据库可以支持的数据规模较小。

### 二.Hive数据类型
#### 1.基本数据类型

|Hive数据类型|Java数据类型|长度|例子|
|:-:|:-:|:-:|:-:|
|TINYINT	|byte	|1byte有符号整数|20|
|SMALINT	|short	|2byte有符号整数|20|
|INT	|int|4byte有符号整数|20|
|BIGINT|long|8byte有符号整数|20|
|BOOLEAN|boolean|布尔类型，true或者false|TRUE  FALSE|
|FLOAT|float|	单精度浮点数|3.14159|
|DOUBLE|double|双精度浮点数|3.14159|
|STRING	|string|字符系列。可以指定字符集。可以使用单引号或者双引号|‘now is the time’ “for all good men”|
|TIMESTAMP	||时间类型||
|BINARY||字节数组||	

&nbsp;&nbsp;&nbsp;&nbsp;对于Hive的String类型相当于数据库的varchar类型，该类型是一个可变的字符串，不过它不能 其中最多能存储多少个字符，理论上它可以存储2GB的字符数。

#### 2.集合数据类型

|数据类型	|描述|语法示例|
|:-:|:-:|:-:|
|STRUCT	|和c语言中的struct类似，都可以通过“点”符号访问元素内容。例如，如果某个列的数据类型是STRUCT{first STRING, last STRING},那么第1个元素可以通过字段.first来引用|struct()|
|MAP|MAP是一组键-值对元组集合，使用数组表示法可以访问数据。例如，如果某个列的数据类型是MAP，其中键->值对是’first’->’John’和’last’->’Doe’，那么可以通过字段名[‘last’]获取最后一个元素	|map()|
|ARRAY|数组是一组具有相同类型和名称的变量的集合。这些变量称为数组的元素，每个数组元素都有一个编号，编号从零开始。例如，数组值为[‘John’, ‘Doe’]，那么第2个元素可以通过数组名[1]进行引用|Array()|


&nbsp;&nbsp;&nbsp;&nbsp;Hive有三种复杂数据类型ARRAY、MAP 和 STRUCT。ARRAY和MAP与Java中的Array和Map类似，而STRUCT与C语言中的Struct类似，它封装了一个命名字段集合，复杂数据类型允许任意层次的嵌套。
```shell
create table heheee(`来试试` string,`来试试1` string,`来试试2` string) row format delimited fields terminated by '\t';
```

**案例实操**
##### (1)假设某表有如下一行，我们用JSON格式来表示其数据结构。在Hive下访问的格式为
```json
{
    "name": "songsong",
    "friends": ["bingbing" , "lili"] ,       //列表Array, 
    "children": {                      //键值Map,
        "xiao song": 18 ,
        "xiaoxiao song": 19
    }
    "address": {                      //结构Struct,
        "street": "hai dian qu" ,
        "city": "beijing" 
    }
}
```
##### (2)基于上述数据结构，我们在Hive里创建对应的表，并导入数据。 

创建本地测试文件test.txt

```shell
cd /opt/module/datas/

touch text.txt
```
添加内容：
```shell
songsong,bingbing_lili,xiao song:18_xiaoxiao song:19,hai dian qu_beijing

yangyang,caicai_susu,xiao yang:18_xiaoxiao yang:19,chao yang_beijing
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTcxNmExMWVlMzA4MTQxM2IucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;注意，MAP，STRUCT和ARRAY里的元素间关系都可以用同一个字符表示，这里用“_”。

##### (3)Hive上创建测试表test
```shell
create table test(
name string,
friends array<string>,
children map<string, int>,
address struct<street:string, city:string>
)
row format delimited fields terminated by ','
collection items terminated by '_'
map keys terminated by ':'
lines terminated by '\n';
```

字段解释：
```shell
row format delimited fields terminated by ','     -- 列分隔符
collection items terminated by '_'  	          --MAP STRUCT 和 ARRAY 的分隔符(数据分割符号)
map keys terminated by ':'				          -- MAP中的key与value的分隔符
lines terminated by '\n';					      -- 行分隔符
```

##### (4)导入文本数据到测试表
```shell
load data local inpath '/opt/module/datas/text.txt' into table test;
```
##### (5)访问三种集合列里的数据，以下分别是ARRAY，MAP，STRUCT的访问方式
```shell
select friends[1],children['xiao song'],address.city from test where name="songsong";
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJkZDgzYzNiZjk5ZjM4NjUucG5n?x-oss-process=image/format,png)


如果起别名：
```shell
select friends[1] as pengyou from test where name="songsong";
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFmMWY3MDg3ZmFlMGQ3MTAucG5n?x-oss-process=image/format,png)

#### 3.类型转化

&nbsp;&nbsp;&nbsp;&nbsp;Hive的原子数据类型是可以进行隐式转换的，类似于Java的类型转换，例如某表达式使用INT类型，TINYINT会自动转换为INT类型，但是Hive不会进行反向转化，例如，某表达式使用TINYINT类型，INT不会自动转换为TINYINT类型，它会返回错误，除非使用CAST操作。
##### (1).隐式类型转换规则如下。
###### (a)任何整数类型都可以隐式地转换为一个范围更广的类型，如TINYINT可以转换成INT，INT可以转换成BIGINT。

###### (b)所有整数类型、FLOAT和STRING类型都可以隐式地转换成DOUBLE。

###### (c )TINYINT、SMALLINT、INT都可以转换为FLOAT。

###### (d)BOOLEAN类型不可以转换为任何其它的类型。

##### (2).可以使用CAST操作显示进行数据类型转换，
&nbsp;&nbsp;&nbsp;&nbsp;例如CAST('1' AS INT)将把字符串'1' 转换成整数1；如果强制类型转换失败，如执行CAST('X' AS INT)，表达式返回空值 NULL。


