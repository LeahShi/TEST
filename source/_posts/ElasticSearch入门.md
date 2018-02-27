---
title: ElasticSearch入门
date: 2017-05-15 10:51:23
tags: [Java,全文检索]
categories: work
---

ElasticSearch和solr一样都是基于Lucene的搜索服务器，在Lucene提供一套全文检索的API。ElasticSearch用于解决数据量庞大时，关系型数据库查询效率低下的问题。并且它提供分布式多用户能力的开源搜索引擎。
ElasticSearch为每个词条创建索引，这些文档中所有词条列表构成倒排索引。当搜索某个词条时，直接找到对应的索引，就像我们再查找字典时，先查找索引中的首字母或者偏旁一样。
 
<!-- more-->

## 一、下载及安装
ElasticSearch是基于java的开发的，所以在安装jdk配置配置JAVA_HOME后，安装很简单，在[官网]( https://www.elastic.co/products/elasticsearch)下载完成后，
直接解压，双击bin/elasticsearch.bat 启动服务，访问http://localhost:9200，会在浏览器中打印一段关于ES的json信息，表示已经安装成功。
 
安装可视化化工具ES head： 
在elasticsearch-x.x.x/bin/plugin.bat 目录下执行**install mobz/elasticsearch-head**命令，安装完成后，重启服务，访问http://localhost:9200/_plugin/head/完成安装

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

## 三、相关对象介绍
### 3.1、搜索文档数据:
使用
QueryBuilders.
BoolQuery 组合多个查询条件
fuzzyQuery 相似度查询
matchAllQuery
termQuery 词条查询
wildCardQuery 模糊查询
### 分词器介绍

在做ElasticSearch的几种Query查询API后发现









