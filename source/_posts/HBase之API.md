---
title: 'HBase之API'
date: 2019-04-08 16:00:00
tags: HBase
categories: HBase
---

#### 0.用途
通过HBase的相关JavaAPI可以实现伴随HBase操作的MapReduce过程，比如使用MapReduce将数据从本地文件系统导入到HBase的表中，比如从HBase中读取一些原始数据后使用MapReduce做数据分析

#### 1.pom.xml
```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-server</artifactId>
    <version>1.3.1</version>
</dependency>
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client</artifactId>
    <version>1.3.1</version>
</dependency>
```

#### 2.编写HBaseAPI
##### (1)获取Configuration对象
```java
public static Configuration conf;
static{
	//使用HBaseConfiguration的单例方法实例化
	conf = HBaseConfiguration.create();
    conf.set("hbase.zookeeper.quorum", "bigdata111");
    conf.set("hbase.zookeeper.property.clientPort", "2181");
	conf.set("zookeeper.znode.parent", "/hbase");
}
```
##### (2)判断表是否存在
```java
public static boolean isTableExist(String tableName) throws MasterNotRunningException, ZooKeeperConnectionException, IOException{
	//在HBase中管理、访问表需要先创建HBaseAdmin对象
    Connection connection = ConnectionFactory.createConnection(conf);
    HBaseAdmin admin = (HBaseAdmin) connection.getAdmin();
	//HBaseAdmin admin = new HBaseAdmin(conf);
	return admin.tableExists(tableName);
}
```
##### (3)创建表
```java
public static void createTable(String tableName, String... columnFamily) throws MasterNotRunningException, ZooKeeperConnectionException, IOException{
	HBaseAdmin admin = new HBaseAdmin(conf);
	//判断表是否存在
	if(isTableExist(tableName)){
		System.out.println("表" + tableName + "已存在");
		//System.exit(0);
	}else{
		//创建表属性对象,表名需要转字节
		HTableDescriptor descriptor = new HTableDescriptor(TableName.valueOf(tableName));
		//创建多个列族
		for(String cf : columnFamily){
			descriptor.addFamily(new HColumnDescriptor(cf));
		}
		//根据对表的配置，创建表
		admin.createTable(descriptor);
		System.out.println("表" + tableName + "创建成功！");
	}
}
```
##### (4)删除表
```java
public static void dropTable(String tableName) throws Exception{
	HBaseAdmin admin = new HBaseAdmin(conf);
	if(isTableExist(tableName)){
		admin.disableTable(tableName);
		admin.deleteTable(tableName);
		System.out.println("表" + tableName + "删除成功！");
	}else{
		System.out.println("表" + tableName + "不存在！");
	}
}
```
##### (5)向表中插入数据
```java
public static void addRowData(String tableName, String rowKey, String columnFamily, String column, String value) throws Exception{
	//创建HTable对象
	HTable hTable = new HTable(conf, tableName);
	//向表中插入数据
	Put put = new Put(Bytes.toBytes(rowKey));
	//向Put对象中组装数据
	put.add(Bytes.toBytes(columnFamily), Bytes.toBytes(column), Bytes.toBytes(value));
	hTable.put(put);
	hTable.close();
	System.out.println("插入数据成功");
}
```
##### (6)删除多行数据
```java
public static void addRowData(String tableName, String rowKey, String columnFamily, String column, String value) throws Exception{
	//创建HTable对象
	HTable hTable = new HTable(conf, tableName);
	//向表中插入数据
	Put put = new Put(Bytes.toBytes(rowKey));
	//向Put对象中组装数据
	put.add(Bytes.toBytes(columnFamily), Bytes.toBytes(column), Bytes.toBytes(value));
	hTable.put(put);
	hTable.close();
	System.out.println("插入数据成功");
}
```
##### (7)得到所有数据
```java
public static void addRowData(String tableName, String rowKey, String columnFamily, String column, String value) throws Exception{
	//创建HTable对象
	HTable hTable = new HTable(conf, tableName);
	//向表中插入数据
	Put put = new Put(Bytes.toBytes(rowKey));
	//向Put对象中组装数据
	put.add(Bytes.toBytes(columnFamily), Bytes.toBytes(column), Bytes.toBytes(value));
	hTable.put(put);
	hTable.close();
	System.out.println("插入数据成功");
}
```
##### (8)得到某一行所有数据
```java
public static void getRow(String tableName, String rowKey) throws IOException{
	HTable table = new HTable(conf, tableName);
	Get get = new Get(Bytes.toBytes(rowKey));
	//get.setMaxVersions();显示所有版本
    //get.setTimeStamp();显示指定时间戳的版本
	Result result = table.get(get);
	for(Cell cell : result.rawCells()){
		System.out.println("行键:" + Bytes.toString(result.getRow()));
		System.out.println("列族" + Bytes.toString(CellUtil.cloneFamily(cell)));
		System.out.println("列:" + Bytes.toString(CellUtil.cloneQualifier(cell)));
		System.out.println("值:" + Bytes.toString(CellUtil.cloneValue(cell)));
		System.out.println("时间戳:" + cell.getTimestamp());
	}
}
```
##### (9)获取某一行指定"列族:列"的数据
```java
public static void getRowQualifier(String tableName, String rowKey, String family, String qualifier) throws IOException{
	HTable table = new HTable(conf, tableName);
	Get get = new Get(Bytes.toBytes(rowKey));
	get.addColumn(Bytes.toBytes(family), Bytes.toBytes(qualifier));
	Result result = table.get(get);
	for(Cell cell : result.rawCells()){
		System.out.println("行键:" + Bytes.toString(result.getRow()));
		System.out.println("列族" + Bytes.toString(CellUtil.cloneFamily(cell)));
		System.out.println("列:" + Bytes.toString(CellUtil.cloneQualifier(cell)));
		System.out.println("值:" + Bytes.toString(CellUtil.cloneValue(cell)));
	}
}
```

##### (10)完整代码HBASE_API.java
```java
package HBase;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.*;
import org.apache.hadoop.hbase.client.*;
import org.apache.hadoop.hbase.util.Bytes;

import java.io.IOException;


public class HBASE_API {
    //获取配置文件
    private static Configuration conf = null;
    /**
     * 初始化
     * */
    static {
        //使用hbaseconfiguration获取conf
        conf = HBaseConfiguration.create();
        //注意使用主机名之前，确保win的hosts配置文件中配置Linux对应的ip和主机名的映射
        conf.set("hbase.zookeeper.quorum","192.168.31.132");
        conf.set("hbase.zookeeper.property.clientPort", "2181");
        conf.set("zookeeper.znode.parent", "/hbase");
    }
    /**
     * 判断表是否存在
     * */
    public static boolean is_table_exists(String table_Name) throws IOException {
        //1.创建hbase的客户端
        Connection connection = ConnectionFactory.createConnection(conf);
        HBaseAdmin admin = (HBaseAdmin) connection.getAdmin();
        //2.判断表是否存在meta
        return admin.tableExists(Bytes.toBytes(table_Name));

    }

    public static void main(String[] args) throws IOException {
        //System.out.println(is_table_exists("aa"));;
        //create_table("idea1","cf1","cf2","cf3");
//        for (int i = 0;i<100;i++){
//            put_table("idea1",String.valueOf(i),"cf1","name","plus"+i);
//        }
        scan_table("idea1");
    }
    /**
     * 创建表:表名，列簇可以是多个
     * */
    public static void create_table(String table_name,String... columnFamily) throws IOException {
        //1.创建hbase的客户端
        Connection connection = ConnectionFactory.createConnection(conf);
        HBaseAdmin admin = (HBaseAdmin) connection.getAdmin();

        if(is_table_exists(table_name)){
            System.out.println(table_name+"已存在");
            return;
        }else {
            //创建表,创建一个表描述对象
            HTableDescriptor hTableDescriptor = new HTableDescriptor(TableName.valueOf(table_name));
            //创建列簇
            for (String cf : columnFamily){
                hTableDescriptor.addFamily(new HColumnDescriptor(cf));
            }
            admin.createTable(hTableDescriptor);
            System.out.println(table_name+"创建成功");
        }
    }
    /**
     * 插入数据:表名，rowkey，列簇，列，value
     * */
    public static void put_table(String name,String row,
                                 String columnfamily,String column,
                                 String value) throws IOException {
        //创建Htable
        HTable hTable = new HTable(conf, name);
        //创建put对象
        Put put = new Put(Bytes.toBytes(row));
        //添加列簇、列、数据
        put.add(Bytes.toBytes(columnfamily),Bytes.toBytes(column),Bytes.toBytes(value));
        hTable.put(put);
        hTable.close();

    }
    /**
     * 扫描数据
     * */
    public static void scan_table(String table_Name) throws IOException {
        HTable hTable = new HTable(conf, table_Name);

        //创建一个scan扫描region
        Scan scan = new Scan();
        //用htable创建resultScanner对象
        ResultScanner scanner = hTable.getScanner(scan);
        for (Result result: scanner){
            Cell[] cells = result.rawCells();
            for (Cell cell:cells){
                System.out.println(Bytes.toString(CellUtil.cloneRow(cell))+"\t"+
                        Bytes.toString(CellUtil.cloneFamily(cell))+","+
                        Bytes.toString(CellUtil.cloneQualifier(cell))+","+
                        Bytes.toString(CellUtil.cloneValue(cell)));
            }
        }

    }
    public static void deleteMultiRow(String tableName,String... rows) throws IOException {
        HTable hTable = new HTable(conf,tableName);
        List<Delete> deleteList = new ArrayList<Delete>();
        
        for(String row:rows){
            Delete delete = new Delete(Bytes.toBytes(row));
            
            deleteList.add(delete);
        }
        hTable.delete(deleteList);
        hTable.close();
    }
}
```