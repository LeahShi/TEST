---
title: Spring Data ElasticSearch快速入门
date: 2017-05-18 10:51:23
tags: [Java,全文检索]
categories: work
---

接上篇，ElasticSearch通常和Spring ES整合起来使用，前文曾经介绍过Spring data 统一持久层框架，就包括Spring data elasticsearch,Spring data redis等非关系型数据库。
使用与Spring整合，很多东西都放在Spring的配置文件中，会是全文检索变得不那么复杂。所以此篇记录下Spring Data ElasticSearch 的相关用法。
 
<!-- more-->
  
## 入门程序：
### 1、导入jar包：（题主使用的mave）
```
<dependency>
	<groupId>org.elasticsearch</groupId>
	<artifactId>elasticsearch</artifactId>
	<version>2.4.0</version>
</dependency>
<dependency>
	<groupId>org.springframework.data</groupId>
	<artifactId>spring-data-elasticsearch</artifactId>
	<version>2.0.4.RELEASE</version>
</dependency>

<!-- 如果使用到了junit测试，需要导入测试包-->
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>4.12</version>
</dependency>
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-test</artifactId>
	<version>4.3.3.RELEASE</version>
</dependency>

<dependency>
	<groupId>org.slf4j</groupId>
	<artifactId>slf4j-log4j12</artifactId>
	<version>1.7.12</version>
</dependency>
```

### 2、配置Spring配置文件：
需要引入名称空间，配置 Client、elasticsearchTemplate、接口和服务类所在包
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
	xmlns:elasticsearch="http://www.springframework.org/schema/data/elasticsearch"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/data/elasticsearch http://www.springframework.org/schema/data/elasticsearch/spring-elasticsearch-1.0.xsd">

	<!-- 配置扫描repository路径 -->
	<elasticsearch:repositories base-package="com.leahshi.dao" />

	<!-- 配置spring 扫描路径 @Repository @Service @Controller -->
	<context:component-scan base-package="com.leahshi" />

	<!-- 配置elasticSearch连接 此处可以配置集群搜索服务器 -->
	<elasticsearch:transport-client id="client" cluster-nodes="localhost:9300" />
	
	<!-- 配置elasticSearch模板，注意id的值只能是elasticsearchTemplate  -->
	<bean id="elasticsearchTemplate" class="org.springframework.data.elasticsearch.core.ElasticsearchTemplate"> 
		<constructor-arg name="client" ref="client" />
	</bean> 
</beans>
```

### 3、定义实体类：
```
package com.leahshi.domain;

import org.springframework.data.annotation.Id;
import org.springframework.data.elasticsearch.annotations.Document;
import org.springframework.data.elasticsearch.annotations.Field;
import org.springframework.data.elasticsearch.annotations.FieldIndex;
import org.springframework.data.elasticsearch.annotations.FieldType;

// 声明文档对象  indexName:索引名称（必填）  type 文档类型  
@Document(indexName = "index03", type = "article")
public class Article {
	// 声明主键
	@Id
	// 声明字段 是否分词，是否在映射中存储分词信息  建立索引采用的分词器 搜索是采用的分词器 字段类型
	@Field(index = FieldIndex.not_analyzed, store = true, type = FieldType.Integer)
	private Integer id;
	@Field(index = FieldIndex.analyzed, analyzer = "ik", store = true, searchAnalyzer = "ik", type = FieldType.String)
	private String title;
	@Field(index = FieldIndex.analyzed, analyzer = "ik", store = true, searchAnalyzer = "ik", type = FieldType.String)
	private String content;
	
	// getter/setter方法

}

```
以上主要有三个注解：
- @Document：indexName属性指定索引库的名称，相当于指定数据库的名称，必填
- @Id：注意不要和jpa的注解导出 
- @Field：type属性必须指定，还可以指定分词器的类型、搜索分词器类型，是否分词，是否建立索引，是否存储该字段

注意：
建立索引标准，字段为String类型可以考虑建立索引，如果为数值型可以无需分词，因为ElasticSearch只是对中文分词支持很差，数值型和英文支持很好，使用默认分词即可。
index取值：index = FieldIndex.not_analyzed：不分词、index = FieldIndex.analyzed：分词、index = FieldIndex.no：不建立索引
另外，经过测试发现，尽管上面实体主键的映射类型为integer，但在索引库中查看最终还是映射为string类型..

### 4、持久层 XxxRepository：
因为Spring data elasticsearch遵从Spring data规范，所以只要熟悉Spring Data JPA，就可以实现无缝对接操作索引库。命名查询、排序查询、分页条件查询等jpa支持的，它都支持。
所有API都相似，所以用起来很顺手！！XxxRepository也值需要继承一个ElasticSearchRepository接口，就可无需定义实现类，操作文档对象。


### 5、业务层：
和其他业务层规范相似，略

### 6、测试类：
```
package com.leahshi.test; 

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.springframework.data.elasticsearch.core.ElasticsearchTemplate;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import com.leahshi.domain.Article;
import com.leahshi.service.IArticleService;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class ElasticSearch_spring_test { 
	 
	@Autowired
	private IArticleService articleService;

	@Autowired
	private ElasticsearchTemplate elasticsearchTemplate;

	// 创建索引
	@Test
	public void test(){
		elasticsearchTemplate.createIndex(Article.class);
		elasticsearchTemplate.putMapping(Article.class);
	}
	
	// spring data elasticSearch crud操作
	@Test
	public void test01() {
		// 如果存在则执行修改操作，不存在执行保存操作
		Article article = new Article();
		article.setId(1001);
		article.setTitle(i + "Elasticsearch相关介绍");
		article.setContent(i + "Elasticsearch （ES）是一个基于 Lucene 的开源搜索引擎，它不但稳定、可靠、快速，而且也具有良好的水平扩展能力，是专门为分布式环境设计的。");
		
		articleService.save(article);

		// 删除
		// Article article = new Article();
		// article.setId(1001);
		// articleService.delete(article);

	}

	// 查询  - 排序查询
	@Test
	public void fun02() {
		// 根据id查询
		// Article article = articleService.findOne(1001);
		// System.out.println(article);

		// 查询所有
		// Iterable<Article> list = articleService.findAll();
		// for (Article article : list) {
		// System.out.println(article);
		// }

		// 排序查询
		for (int i = 1; i <= 100; i++) {
			Article article = new Article();
			article.setId(i);
			article.setTitle(i + "Elasticsearch相关介绍");
			article.setContent(i + "Elasticsearch （ES）是一个基于 Lucene 的开源搜索引擎，它不但稳定、可靠、快速，而且也具有良好的水平扩展能力，是专门为分布式环境设计的。");
			articleService.save(article);
		}
		
		Iterable<Article> all = articleService.findAll(new Sort(new Sort.Order(Sort.Direction.DESC,"id")));
		for (Article article : all) {
			System.out.println(article);
		}
		
	}
	
	// 分页条件查询
	@Test
	public void fun03(){
		// 查询第二页，每页显示10调数据
		Pageable pageable = new PageRequest(1, 10);
		Page<Article> pageData = articleService.findAll(pageable);
		for (Article article : pageData.getContent()) {
			System.out.println(article);
		}
		
	}
}

```















































