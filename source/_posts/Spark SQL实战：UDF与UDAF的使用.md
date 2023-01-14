---
title: 'Spark SQL实战之UDF与UDAF的使用'
date: 2020-03-13 19:00:00
tags: 
- Spark
- Spark实战
- Spark SQL
categories: Spark
---

#### 1.概念：
UDF就是用户自定义的函数
UDAF就是用户自定义的聚合函数

#### 2.代码：
##### (1)pom.xml
```xml
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-core_2.11</artifactId>
    <version>2.1.0</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.apache.spark/spark-sql -->
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-sql_2.11</artifactId>
    <version>2.1.0</version>
</dependency>
```
##### (2)SparkSQLUDFUDAF.scala
```java
package spark.sqlshizhan
import org.apache.spark.SparkConf
import org.apache.spark.SparkContext
import org.apache.spark.sql.{ Row, SQLContext }
import org.apache.spark.sql.types.StructType
import org.apache.spark.sql.types.StructField
import org.apache.spark.sql.types.StringType
import org.apache.spark.sql.expressions.UserDefinedAggregateFunction
import org.apache.spark.sql.expressions.MutableAggregationBuffer
import org.apache.spark.sql.types.DataType
import org.apache.spark.sql.types.IntegerType
/**
 * @ClassName SparkSQLUDFUDAF
 * @MethodDesc: SparkSQL UDF与UDAF的使用
 * @Author Movle
 * @Date 5/18/20 10:44 下午
 * @Version 1.0
 * @Email movle_xjk@foxmail.com
 **/
object SparkSQLUDFUDAF {

  def main(args: Array[String]): Unit = {

    val conf = new SparkConf().setMaster("local").setAppName("SparkSQLUDFUDAF")

    val sc = new SparkContext(conf)

    val sqlContext = new SQLContext(sc)

    val bigData = Array("Spark", "Spark", "Hadoop", "spark", "Hadoop", "spark", "Hadoop", "Hadoop", "spark", "spark")

    //创建Dataframe
    val bigDataRDD = sc.parallelize(bigData)

    val bigDataRDDRow = bigDataRDD.map(item => Row(item))

    val structType = StructType(Array(
      new StructField("word", StringType)))

    val bigDataDF = sqlContext.createDataFrame(bigDataRDDRow, structType)

    bigDataDF.createOrReplaceTempView("bigDataTable")

    //UDF 最多22个输入参数
    sqlContext.udf.register("computeLength",(input:String,input2:String) => input.length())

    sqlContext.sql("select word,computeLength(word,word) from bigDataTable").show()

    sqlContext.udf.register("wordcount", new MyUDAF)

    sqlContext.sql("select word,wordcount(word) as count from bigDataTable group by word").show()


    sc.stop()

  }
}

class MyUDAF extends UserDefinedAggregateFunction{

  /**
   * 该方法指定具体输入数据的类型
   * @return
   */
  override def inputSchema: StructType = StructType(Array(StructField("input", StringType, true)))

  /**
   * 在进行聚合操作的时候所要处理的数据的结果的类型
   * @return
   */
  override def bufferSchema: StructType = StructType(Array(StructField("count", IntegerType, true)))

  /**
   * 指定UDAF函数计算后返回的结果类型
   * @return
   */
  override def dataType: DataType = IntegerType

  /**
   * 确保一致性，一般都用true
   * @return
   */
  override def deterministic: Boolean = true

  /**
   * 在Aggregate之前每组数据的初始化结果
   * @param buffer
   */
  override def initialize(buffer: MutableAggregationBuffer): Unit = { buffer(0) = 0 }

  /**
   * 在进行聚合的时候，每当有新的值进来，对分组后的聚合如何进行计算
   * 本地的聚合操作，相当于Hadoop MapReduce模型中的Combiner
   * @param buffer
   * @param input
   */
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Int](0) + 1
  }

  /**
   * 最后在分布式节点进行Local Reduce完成后需要进行全局级别的Merge操作
   * @param buffer1
   * @param buffer2
   */
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Int](0) + buffer2.getAs[Int](0)
  }

  /**
   * 返回UDAF最后的计算结果
   * @param buffer
   * @return
   */
  override def evaluate(buffer: Row): Any = buffer.getAs[Int](0)
}
```