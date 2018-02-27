---
title: ElasticSearch入门
date: 2017-05-15 10:51:23
tags: [Java,全文检索]
categories: work
---

ElasticSearch是基于Lucene的搜索服务器 


<!-- more-->

## 下载及安装
配置JAVA_HOME 

访问 http://localhost:9200  


安装ESHead 可视化工具：%elasticsearch%/bin/plugin.bat install mobz/elasticsearch-head

访问  http://localhost:9200/_plugin/head/


## 主要概念
索引：相当于关系型数据库的表，存储数据的表结构，任何搜索数据存放在索引对象上
文档：存储在数据库中的一行记录（数据，由词条搜索得出），存在索引对象上
文档类型：一个索引对象可以存储多个不同类型数据，数据用文档类型标识
映射：数据如何存放到索引对象上，需要有一个映射配置， 数据类型、是否存储、是否分词 …

建立索引对象 - 建立映射 - 存储数据 - 指定文档类型搜索数据 - 查询数据


ElasticSearch服务端默认连接的是9300
浏览器端浏览端口9200

建立文档，需要指定索引和文档类型


## 搜索文档数据
QueryBuilders.
BoolQuery 组合多个查询条件
fuzzyQuery 相似度查询
matchAllQuery
termQuery 词条查询
wildCardQuery 模糊查询


























