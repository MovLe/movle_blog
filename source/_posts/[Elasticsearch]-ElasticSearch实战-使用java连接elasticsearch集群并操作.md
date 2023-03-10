---
title: '[Elasticsearch]-ElasticSearch实战-使用java连接elasticsearch集群并操作'
date: 2020-01-12 15:00:00
tags: 
- Elasticsearch
- Elasticsearch实战
categories: Elasticsearch
---

#### 1.需求：
使用java连接elasticsearch集群，并进行相关操作
#### 2.代码：
##### (1)pom.xml
```java
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.10</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.elasticsearch</groupId>
            <artifactId>elasticsearch</artifactId>
            <version>5.6.1</version>
        </dependency>

        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>transport</artifactId>
            <version>5.6.1</version>
        </dependency>

        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>2.9.0</version>
        </dependency>

    </dependencies>
```
##### (2)ES_test.java
```java
import java.net.InetAddress;
import java.net.UnknownHostException;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;

import org.elasticsearch.action.admin.indices.mapping.put.PutMappingRequest;
import org.elasticsearch.action.delete.DeleteResponse;
import org.elasticsearch.action.get.GetResponse;
import org.elasticsearch.action.get.MultiGetItemResponse;
import org.elasticsearch.action.get.MultiGetResponse;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.action.index.IndexResponse;
import org.elasticsearch.action.search.SearchResponse;
import org.elasticsearch.action.update.UpdateRequest;
import org.elasticsearch.action.update.UpdateResponse;
import org.elasticsearch.client.Requests;
import org.elasticsearch.client.transport.TransportClient;
import org.elasticsearch.common.settings.Settings;
import org.elasticsearch.common.transport.InetSocketTransportAddress;
import org.elasticsearch.common.xcontent.XContentBuilder;
import org.elasticsearch.common.xcontent.XContentFactory;
import org.elasticsearch.index.query.QueryBuilders;
import org.elasticsearch.search.SearchHit;
import org.elasticsearch.search.SearchHits;
import org.elasticsearch.transport.client.PreBuiltTransportClient;
import org.junit.Before;
import org.junit.Test;

public class ES_test {

    private TransportClient client;

    @SuppressWarnings("unchecked")
    @Before
    public void getClient() throws UnknownHostException {

        // 1 设置连接的集群名称
        Settings settings = Settings.builder().put("cluster.name", "my-application").build();

        // 2 连接集群
        client = new PreBuiltTransportClient(settings);
        client.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("192.168.1.121"), 9300));

        // 3 打印集群名称
         //System.out.println(client.toString());
    }

    @Test
    public void createIndex_blog() {
        // 创建索引

        client.admin().indices().prepareCreate("blog").get();

        client.close();
    }

    @Test
    public void deleteIndex() {
        // 删除索引
        client.admin().indices().prepareDelete("blog").get();

        client.close();
    }

    @Test
    public void insertByJson() {
        // 使用json常见document

        // 1、文档数据准备
        String json = "{" + "\"id\":\"1\"," + "\"title\":\"基于Lucene的搜索服务器\","
                + "\"content\":\"它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口\"" + "}";

        // 2 在es中创建文档
        IndexResponse indexResponse = client.prepareIndex("blog", "article", "1").setSource(json).execute().actionGet();

        // 3 打印返回结果
        System.out.println("index: " + indexResponse.getIndex());
        System.out.println("type: " + indexResponse.getType());
        System.out.println("id: " + indexResponse.getId());
        System.out.println("result: " + indexResponse.getResult());

        client.close();
    }

    @Test
    public void insertByMap() {
        // 使用Map创建document
        // 1、文档数据准备
        Map<String, Object> json = new HashMap<String, Object>();
        json.put("id", "2");
        json.put("title", "基于Lucene的搜索服务器");
        json.put("content", "它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口");

        // 2 创建文档
        IndexResponse indexResponse = client.prepareIndex("blog", "article", "2").setSource(json).execute().actionGet();

        // 3 打印返回结果
        System.out.println("index: " + indexResponse.getIndex());
        System.out.println("type: " + indexResponse.getType());
        System.out.println("id: " + indexResponse.getId());
        System.out.println("result: " + indexResponse.getResult());

        client.close();
    }

    @Test
    public void insertByXContent() throws Throwable {
        // 使用XContentBuilder创建document

        // 1 通过es自带的帮助类，构建json数据
        XContentBuilder builder = XContentFactory.jsonBuilder().startObject().field("id", 5)
                .field("title", "基于Lucene的搜索服务器").field("content", "sdfs它sdfsdfsdf提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。")
                .endObject();

        // 2 创建文档
        IndexResponse indexResponse = client.prepareIndex("blog", "article", "3").setSource(builder).get();

        // 3 打印返回结果
        System.out.println("index: " + indexResponse.getIndex());
        System.out.println("type: " + indexResponse.getType());
        System.out.println("id: " + indexResponse.getId());
        System.out.println("result: " + indexResponse.getResult());

        client.close();

    }

    @Test
    public void getData() {
        // 查询文档
        GetResponse response = client.prepareGet("blog", "article", "1").get();

        System.out.println(response.getSourceAsString());

        client.close();

    }

    @Test
    public void getMultiData() {
        // 查询多个文档

        MultiGetResponse response = client.prepareMultiGet()
                .add("blog", "article", "1")
                .add("blog", "article", "2", "3")
                .add("blog", "article", "2").get();

        //遍历返回结果
        for (MultiGetItemResponse multiGetItemResponse : response) {
            GetResponse getResponse = multiGetItemResponse.getResponse();

            //获取查询结果
            if (getResponse.isExists()) {
                String sourceAsString = getResponse.getSourceAsString();
                System.out.println(sourceAsString);

            }
        }

        client.close();
    }
    @Test
	public void updateData() throws Throwable {

		// 1 创建更新数据的请求对象
		UpdateRequest updateRequest = new UpdateRequest();
		updateRequest.index("blog");
		updateRequest.type("article");
		updateRequest.id("3");

		updateRequest.doc(XContentFactory.jsonBuilder().startObject()
				// 对没有的字段添加, 对已有的字段替换
				.field("title", "基于Lucene的搜索服务器")
				.field("content", "它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。大数据前景无限")
				.field("createDate", "2017-8-22")
				.endObject());

		// 2 获取更新后的值
		UpdateResponse indexResponse = client.update(updateRequest).get();

		// 3 打印返回的结果
		System.out.println("index:" + indexResponse.getIndex());
		System.out.println("type:" + indexResponse.getType());
		System.out.println("id:" + indexResponse.getId());
		System.out.println("version:" + indexResponse.getVersion());
		System.out.println("create:" + indexResponse.getResult());// NOOP = NO
																	// OPeration
																	// ;
																	// result=updated
																	// 表示更新成功

		// 4 关闭连接
		client.close();
	}

	@Test
	public void testUpsert() throws Exception {

		// 设置查询条件, 查找不到则添加
		IndexRequest indexRequest = new IndexRequest("blog", "article", "5")
				.source(XContentFactory.jsonBuilder().startObject().field("title", "搜索服务器")
						.field("content",
								"它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。设计用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。")
				.endObject());

		// 设置更新, 查找到更新下面的设置
		UpdateRequest upsert = new UpdateRequest("blog", "article", "5")
				.doc(XContentFactory.jsonBuilder().startObject().field("user", "李四").endObject()).upsert(indexRequest);

		client.update(upsert).get();
		client.close();
	}

	@Test
	public void deleteData() {

		// 1 删除文档数据
		DeleteResponse indexResponse = client.prepareDelete("blog", "article", "5").get();

		// 2 打印返回的结果
		System.out.println("index:" + indexResponse.getIndex());
		System.out.println("type:" + indexResponse.getType());
		System.out.println("id:" + indexResponse.getId());
		System.out.println("version:" + indexResponse.getVersion());
		System.out.println("found:" + indexResponse.getResult());

		// 3 关闭连接
		client.close();
	}

	@Test
	public void matchAllQuery() {

		// 查询所有
		// 1 执行查询
		SearchResponse searchResponse = client.prepareSearch("blog").setTypes("article")
				.setQuery(QueryBuilders.matchAllQuery()).get();

		// 2 打印查询结果
		SearchHits hits = searchResponse.getHits(); // 获取命中次数，查询结果有多少对象
		System.out.println("查询结果有：" + hits.getTotalHits() + "条");

		for (SearchHit hit : hits) {
			System.out.println(hit.getSourceAsString());// 打印出每条结果
		}

		// 3 关闭连接
		client.close();
	}

	@Test
	public void query() {
		
		// 1 条件查询
		SearchResponse searchResponse = client.prepareSearch("blog").setTypes("article")
				.setQuery(QueryBuilders.queryStringQuery("大数据")).get();

		// 2 打印查询结果
		SearchHits hits = searchResponse.getHits(); // 获取命中次数，查询结果有多少对象
		System.out.println("查询结果有：" + hits.getTotalHits() + "条");

		for (SearchHit hit : hits) {
			System.out.println(hit.getSourceAsString());// 打印出每条结果
		}

		// 3 关闭连接
		client.close();
	}

	@Test
	public void wildcardQuery() {

		// 1 通配符查询
		SearchResponse searchResponse = client.prepareSearch("blog").setTypes("article")
				.setQuery(QueryBuilders.wildcardQuery("content", "*全*")).get();

		// 2 打印查询结果
		SearchHits hits = searchResponse.getHits(); // 获取命中次数，查询结果有多少对象
		System.out.println("查询结果有：" + hits.getTotalHits() + "条");

		for (SearchHit hit : hits) {
			System.out.println(hit.getSourceAsString());// 打印出每条结果
		}

		// 3 关闭连接
		client.close();
	}

	@Test
	public void termQuery() {

		// 类似于 mysql 中 = 

		// 1 第一field查询
		SearchResponse searchResponse = client.prepareSearch("blog").setTypes("article")
				.setQuery(QueryBuilders.termQuery("content", "全文")).get();

		// 2 打印查询结果
		SearchHits hits = searchResponse.getHits(); // 获取命中次数，查询结果有多少对象
		System.out.println("查询结果有：" + hits.getTotalHits() + "条");

		for (SearchHit hit : hits) {
			System.out.println(hit.getSourceAsString());// 打印出每条结果
		}

		// 3 关闭连接
		client.close();
	}

	@Test
	public void fuzzy() {

		// 1 模糊查询
		SearchResponse searchResponse = client.prepareSearch("blog").setTypes("article")
				.setQuery(QueryBuilders.fuzzyQuery("title", "lucene")).get();

		// 2 打印查询结果
		SearchHits hits = searchResponse.getHits(); // 获取命中次数，查询结果有多少对象
		System.out.println("查询结果有：" + hits.getTotalHits() + "条");

		Iterator<SearchHit> iterator = hits.iterator();

		while (iterator.hasNext()) {
			SearchHit searchHit = iterator.next(); // 每个查询对象

			System.out.println(searchHit.getSourceAsString()); // 获取字符串格式打印
		}

		// 3 关闭连接
		client.close();
	}

	@Test
	public void createMapping() throws Exception {

		// 1设置mapping
		XContentBuilder builder = XContentFactory.jsonBuilder().startObject().startObject("article")
				.startObject("properties")
				.startObject("id1")
				.field("type", "text")
				.field("store", "true").endObject()
				.startObject("title2")
				.field("type", "text").field("store", "false").endObject()
				.startObject("content")
				.field("type", "text").field("store", "true").endObject()
				.endObject().endObject().endObject();

		// 2 添加mapping
		PutMappingRequest mapping = Requests.putMappingRequest("blog4").type("article").source(builder);

		client.admin().indices().putMapping(mapping).get();

		// 3 关闭资源
		client.close();
	}

	// 创建使用ik分词器的mapping
	@Test
	public void createMapping_ik() throws Exception {

		// 1设置mapping
		XContentBuilder builder = XContentFactory.jsonBuilder().startObject().startObject("article")
				.startObject("properties")
				.startObject("id1")
				.field("type", "text").field("store", "true")
				.field("analyzer", "ik_smart")
				.endObject()
				.startObject("title2").field("type", "text")
				.field("store", "false").field("analyzer", "ik_smart").
				endObject().startObject("content")
				.field("type", "text").field("store", "true")
				.field("analyzer", "ik_smart").endObject().endObject()
				.endObject().endObject();

		// 2 添加mapping
		PutMappingRequest mapping = Requests.putMappingRequest("blog4").type("article").source(builder);
		client.admin().indices().putMapping(mapping).get();

		// 3 关闭资源
		client.close();
	}

	// 创建文档,以map形式
	@Test
	public void createDocumentByMap_forik() {

		HashMap<String, String> map = new HashMap<String, String>();
		map.put("id1", "2");
		map.put("title2", "Lucene");
		map.put("content", "它提供了一个分布式的web接口");

		IndexResponse response = client.prepareIndex("blog4", "article", "3").setSource(map).execute().actionGet();

		// 打印返回的结果
		System.out.println("结果:" + response.getResult());
		System.out.println("id:" + response.getId());
		System.out.println("index:" + response.getIndex());
		System.out.println("type:" + response.getType());
		System.out.println("版本:" + response.getVersion());

		// 关闭资源
		client.close();
	}

	// 词条查询
	@Test
	public void queryTerm_forik() {

		// 分析结果：因为是默认被standard analyzer分词器分词，大写字母全部转为了小写字母，并存入了倒排索引以供搜索。term是确切查询， 必须要匹配到大写的Name。
		SearchResponse response = client.prepareSearch("blog4")
				.setTypes("article")
				.setQuery(QueryBuilders.termQuery("content", "web"))
				.get();

		// 获取查询命中结果
		SearchHits hits = response.getHits();

		System.out.println("结果条数:" + hits.getTotalHits());

		for (SearchHit hit : hits) {
			System.out.println(hit.getSourceAsString());
		}
	}

}


```

#### 3.结果：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNjNjJkNDdkMTk1ODc2OTAucG5n?x-oss-process=image/format,png)

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFjZTEyY2RjODU3NTRiYmEucG5n?x-oss-process=image/format,png)

