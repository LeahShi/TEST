---
title: ElasticSearch入门
date: 2017-07-15 10:51:23
tags: [Java,全文检索]
categories: work
---

ElasticSearch和solr一样都是基于Lucene的搜索服务器，在Lucene提供一套全文检索的API。ElasticSearch用于解决数据量庞大时，关系型数据库查询效率低下的问题。并且它提供分布式多用户能力的开源搜索引擎。
ElasticSearch为每个词条创建索引，这些文档中所有词条列表构成倒排索引。当搜索某个词条时，直接找到对应的索引，就像我们再查找字典时，先查找索引中的首字母或者偏旁一样。
ElasticSearch采用Restful风格，所以都是采用json格式数据传输
 
<!-- more-->

## 一、下载及安装
ElasticSearch是基于java的开发的，所以在安装jdk配置配置JAVA_HOME后，安装很简单，在[官网]( https://www.elastic.co/products/elasticsearch)下载完成后，
直接解压，双击bin/elasticsearch.bat 启动服务，访问```http://localhost:9200```，会在浏览器中打印一段关于ES的json信息，表示已经安装成功。
 
安装可视化化工具ES head： 
在elasticsearch-x.x.x/bin/plugin.bat 目录下执行**install mobz/elasticsearch-head**命令，安装完成后，重启服务，访问```http://localhost:9200/_plugin/head/```完成安装

ElasticSearch服务器默认的端口是9300，在浏览器中的端口是9200

## 二、主要概念
在ElasticSearch搜索服务器中存在着和传统的关系型数据库类似的概念。
- 索引：相当于关系型数据库的表，存储数据的表结构，任何搜索数据存放在索引对象上
- 文档：存储在数据库中的一行记录（数据，由词条搜索得出），存在索引对象上
- 文档类型：一个索引对象可以存储多个不同类型数据，数据用文档类型标识
- 映射：数据如何存放到索引对象上，需要有一个映射配置， 数据类型、是否存储、是否分词 …

开发ElasticSearch程序顺序：
建立索引对象 - 建立映射 - 存储数据 - 指定文档类型搜索数据 - 查询数据 

* 注：创建文档时需要指定索引和文档类型，会自动创建索引和添加映射


## 三、快速入门  
引入jar包：
```
<dependency>
	<groupId>org.elasticsearch</groupId>
	<artifactId>elasticsearch</artifactId>
	<version>2.4.0</version>
</dependency>
```

### 1、自动创建索引和文档对象
文档对象格式和映射关系不可自定义，引入IK分词器支持后还需要在文档对象中引用IK分词器！！！ 

```
@Test 
public void test() throws Exception {
	// 创建连接搜索服务器对象  
	Client client = TransportClient.builder().build()
			.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("127.0.0.1"), 9300));
			// 可以采用练市编程创建集群搜索服务器
			// .addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("127.0.0.1"), 9300));
	// 使用XContentBuilder对象 构造json数据
	// {id:1,title:"",content:""}
	XContentBuilder contentBuilder = XContentFactory.jsonBuilder()
			.startObject()
				.field("id", 1)
				.field("title", "ElasticSearch是一个基于Lucene的搜索服务器")
				.field("content",
					"它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。设计用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。")
			.endObject();

	// 创建文档对象 直接在ElasticSearch中建立文档，自动创建索引
	client.prepareIndex("idx01", "article", "1").setSource(contentBuilder).get();

	// 关闭连接
	client.close(); 
	
}

``` 


### 2、基于各种搜索对象搜索文档

```
@Test
public void test01() throws Exception {
	// 创建连接所有服务器对象
	Client client = TransportClient.builder().build().addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("127.0.0.1"), 9300));
 
	/**
	 *  搜索数据:	
	 *		matchAllQuery ：查询所有匹配条件的结果
	 * 		queryStringQuery : 基于分词查询 ，可以根据多个词条进行查询，并对其取交集或者并集 
	 * 		fuzzyQuery : 相似度查询
	 * 		BoolQuery ： 组合多个查询条件 
	 *		wildcardQuery ： 模糊查询，在左右两侧加上 * 而不是 % ！！！
	 * 		termQuery() 和  wildcardQuery 类似基于词条查询  并且是基于默认词条查询 一个字一个字匹配
	 */
	 
	SearchResponse response = client.prepareSearch("idx01").setTypes("article").setQuery(QueryBuilders.matchAllQuery()).get();  	
	 
	// SearchResponse response = client.prepareSearch("idx01").setTypes("article").setQuery(QueryBuilders.queryStringQuery("搜索")).get();

	// SearchResponse response = client.prepareSearch("idx01").setTypes("article").setQuery(QueryBuilders.wildcardQuery("content", "*搜索*")).get();

	// SearchResponse response = client.prepareSearch("idx01").setTypes("article").setQuery(QueryBuilders.termQuery("content", "搜索")).get();

	// 打印并输出
	SearchHits hits = response.getHits();
	System.out.println("总条数" + hits.getTotalHits());
	Iterator<SearchHit> iterator = hits.iterator();
	while (iterator.hasNext()) {
		// 获取每个查询对象
		SearchHit hit = iterator.next();
		// 获取每个对象内容
		System.out.println(hit.getSourceAsString());
		// 获取具体的某个字段
		System.out.println("title:" + hit.getSource().get("title"));
	}
}
```

以上测试类，只有matchAllQuery可以 返回所有数据，queryStringQuery也可以返回数据，wildcardQuery和termQuery均不能返回结果。
按照定义，"搜索"这个词条应该存在ElasticSearch索引库中，为什么查不到呢？这是因为ElasticSearch对中文支持很差，基本上是按照一个字一个字分词，所以需要引入第三方分词器对中文进行分词。
 

## 四、分词器介绍 
### 1、安装与配置
在开发中我们常用的是ik分词器集成ElasticSearch来使用，ik分词器是一个开源的基于java开发的轻量级中文分词工具包。
第一步：[戳这里下载](https://github.com/medcl/elasticsearch-analysis-ik/tree/2.x) 
第二步：下载完成后，进入 target/release 目录，将除了 elasticsearch-analysis-ik-x.x.x 目录外所有文件；拷贝到 %es%/plugins/analysis-ik下
第三步：进入 target/release/config 目录，将所有配置文件，复制 %es%/config 下；
第四步：配置 config/elasticsearch.yml，在最后加上 【index.analysis.analyzer.ik.type: "ik"】

### 2、ik分词器集成ElasticSearch

```
@Test
public void test02() throws Exception {
	Client client = TransportClient.builder().build()
			.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("127.0.0.1"), 9300));

	// 1、手动创建索引 
	client.admin().indices().prepareCreate("idx02").get(); 
	// 删除索引
	// client.admin().indices().prepareDelete("idx02").get();
	
	// 2、添加映射 凭借json数据 {"mappding":{"article":{"properties":{"id":{"type":"","store",""},"title":{"type":"","store",""},"content":{"type":"","store",""}}}}}
	XContentBuilder builder = XContentFactory.jsonBuilder()
			.startObject()
				.startObject("article")
					.startObject("properties")
						.startObject("id").field("type", "integer").field("store", "yes").endObject()
						.startObject("title").field("type", "string").field("store", "yes").field("analyzer", "ik").endObject()
						.startObject("content").field("type", "string").field("store", "yes").field("analyzer", "ik").endObject()
					.endObject()
				.endObject()
			.endObject();

	PutMappingRequest mapping = Requests.putMappingRequest("idx01").type("article").source(builder);
	client.admin().indices().putMapping(mapping).get();
	
	
	// 文档对象
	client.close();

}
```

以上测试方法执行完成后，在http://localhost:9200/_plugin/head/ 可以看到，已经创建了一个idx02索引库

```
// 文档操作
@Test
public void test03() throws Exception {
	// 创建连接搜索服务器对象
	Client client = TransportClient.builder().build()
			.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("127.0.0.1"), 9300));

	// 根据返回实体转换json封装索引
	Article article = new Article();
	article.setId(2);
	article.setTitle("Elasticsearch相关介绍");
	article.setContent("Elasticsearch （ES）是一个基于 Lucene 的开源搜索引擎，它不但稳定、可靠、快速，而且也具有良好的水平扩展能力，是专门为分布式环境设计的。");
	
	// jackson
	ObjectMapper mapper = new ObjectMapper();

	// 创建文档
	client.prepareIndex("idx02", "article", article.getId().toString()).setSource(mapper.writeValueAsString(article)).get();

	// 删除文档
	//client.prepareDelete("idx02", "article", article.getId().toString()).get();
	//client.delete(new DeleteRequest("idx02", "article", article.getId().toString())).get();
	
	
	// 修改文档
	//client.prepareUpdate("idx02", "article", article.getId().toString()).setDoc(mapper.writeValueAsString(article)).get();
	//client.update(new UpdateRequest("idx02", "article", article.getId().toString())).get();
	client.close();
}
	
```

在idx02索引库中可以看到已经有一条数据已经插入

```
// 分页搜索
@Test
public void test04() throws Exception{
	// 创建连接搜索服务器对象
	Client client = TransportClient.builder().build()
			.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("127.0.0.1"), 9300));
	
	ObjectMapper mapper = new ObjectMapper();
	
	// 批量查询记录
	for (int i = 1; i <= 100; i++) {
		// 描述json 数据
		Article article = new Article();
		article.setId(i);
		article.setTitle(i+"Elasticsearch相关介绍");
		article.setContent(i+"Elasticsearch （ES）是一个基于 Lucene 的开源搜索引擎，它不但稳定、可靠、快速，而且也具有良好的水平扩展能力，是专门为分布式环境设计的。");
	
		// 建立文档 
		client.prepareIndex("idx02", "article", article.getId().toString())
				.setSource(mapper.writeValueAsString(article)).get();
	} 
	 
	 // 根据title字段中 包含"搜索" 这个词条 的结果集进行分页查询
	SearchRequestBuilder requestBuilder = client.prepareSearch("idx01").setTypes("article").setQuery(QueryBuilders.termQuery("title", "搜索"));
	
	// 查询第二页 每页显示20条  不进行分页的情况下默认是返回10条
	int pageNo = 1;
	int pageSize = 20;
	requestBuilder.setFrom((pageNo-1)*pageSize).setSize(pageSize);
	
	SearchResponse response = requestBuilder.get();
	
	
	// 打印并输出
	SearchHits hits = response.getHits();
	System.out.println("总条数" + hits.getTotalHits());
	Iterator<SearchHit> iterator = hits.iterator();
	while (iterator.hasNext()) {
		// 获取每个查询对象
		SearchHit hit = iterator.next();
		// 获取每个对象内容
		System.out.println(hit.getSourceAsString());
		// 获取具体的某个字段
		System.out.println("title:" + hit.getSource().get("title"));
	}
	
	client.close();
}
```


在控制台中就返回了20条数据，因为没有排序，所以返回的id不是连续的
在很多全文搜索服务中，会对关键字进行高亮显示，原理很简单，就是在搜索关键在的周围定义一个标签，并对标签定义样式

```

// 高亮显示处理
@Test
public void test05() throws Exception {
	// 创建连接搜索服务器对象
	Client client = TransportClient.builder().build()
			.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("127.0.0.1"), 9300));

	ObjectMapper objectMapper = new ObjectMapper();

	// 搜索数据
	SearchRequestBuilder searchRequestBuilder = client.prepareSearch("idx01").setTypes("article")
			.setQuery(QueryBuilders.termQuery("content", "搜索"));

	// 高亮定义
	searchRequestBuilder.addHighlightedField("content"); // 对title字段进行高亮
	searchRequestBuilder.setHighlighterPreTags("<em>"); // 前置元素
	searchRequestBuilder.setHighlighterPostTags("</em>");// 后置元素

	SearchResponse searchResponse = searchRequestBuilder.get();

	SearchHits hits = searchResponse.getHits(); // 获取命中次数，查询结果有多少对象
	System.out.println("查询结果有：" + hits.getTotalHits() + "条");
	Iterator<SearchHit> iterator = hits.iterator();
	while (iterator.hasNext()) {
		SearchHit searchHit = iterator.next(); // 每个查询对象

		// 将高亮处理后内容，替换原有内容 （原有内容，可能会出现显示不全 ）
		Map<String, HighlightField> highlightFields = searchHit.getHighlightFields();
		HighlightField titleField = highlightFields.get("content");

		// 获取到原有内容中 每个高亮显示 集中位置 fragment 就是高亮片段
		Text[] fragments = titleField.fragments();
		String title = "";
		for (Text text : fragments) {
			// text将一条数据的该字段切分
			title += text;
		}
		// 将查询结果转换为对象
		Article article = objectMapper.readValue(searchHit.getSourceAsString(), Article.class);

		// 用高亮后内容，替换原有内容
		article.setTitle(title);

		System.out.println(article);
	}

	// 关闭连接
	client.close();
}
```








































