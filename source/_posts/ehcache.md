---
title: Ehcache
date: 2016-11-15 13:50:37
tags: [Java,缓存]
categories: work
---

接上篇使用完shiro完成动态菜单的制作后发现每次访问一个需要被权限控制资源时，调用 Realm 的授权方法，根据当前用户查询角色（role）和权限（permission）信息，
每次调用 都会查询一次数据库从数据库查找菜单的信息，这样会增加数据库的压力，为了提高查询速率，查询数据后，我们可以将数据保存在内存中，提高查询数据性能，
对同一批数据进行多次查询时， 第一次查询走数据库，之后的查询可以直接从内存获取数据，而不需要和数据库进行交互。

所以在在权限控制后还需要根据当前登录用对应的权限进行缓存。 缓存服务器有很多，因为shiro内置对ehcache的支持，所以通常情况下使用ehcache缓存相关授权数据。

<!-- more--> 

## 一、授权数据的缓存：
1、导入jar包
```
<dependency>
	<groupId>net.sf.ehcache</groupId>
	<artifactId>ehcache-core</artifactId>
	<version>2.6.11</version>
</dependency>
```
 

3、创建ehcache.xml配置文件
缓存区可以配置多个，可以根据具体的业务配置不同的缓冲区，该配置文件可以在ehcache-core.jar包中找到
```
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="../config/ehcache.xsd">
    <!-- 内存中缓存数据 过多，写入硬盘，diskStore 用来定义硬盘保存缓存数据位置 -->
	<diskStore path="java.io.tmpdir"/>
    
	<!-- 
		默认缓存配置策略：
			maxElementsInMemory 内存中允许存放对象最大数量
			eternal 缓存数据是否是永久的
			maxElementsOnDisk 硬盘中缓存最大对象数量 -->
    <defaultCache
            maxElementsInMemory="10000"
            eternal="false"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            maxElementsOnDisk="10000000"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
        <persistence strategy="localTempSwap"/>
    </defaultCache>
    
	<!-- 权限菜单配置缓存区-->
    <cache name="menu" 
            maxElementsInMemory="10000"
            eternal="false"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            maxElementsOnDisk="10000000"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
        <persistence strategy="localTempSwap"/>
    </cache>
    
	<!-- 可以根据不同的业务模块配置不同的缓存区 -->
    <cache name="standard" 
            maxElementsInMemory="10000"
            eternal="false"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            maxElementsOnDisk="10000000"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
        <persistence strategy="localTempSwap"/>
    </cache>
</ehcache>

```
 
3、spring配置文件
将application.xml抽取出application-cache.xml配置文件，并且配置cache名称空间

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:cache="http://www.springframework.org/schema/cache"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans 
		http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context 
		http://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/cache
		http://www.springframework.org/schema/cache/spring-cache.xsd">


	<!-- 1、spring管理缓存管理器 并加载缓存配置文件 -->
	<bean id="ehCacheManager"
		class="org.springframework.cache.ehcache.EhCacheManagerFactoryBean">
		<property name="configLocation" value="classpath:ehcache.xml"></property>
	</bean>

	<!-- 2、shiro封装缓存管理器 -->
	<bean id="shiroCacheManager" class="org.apache.shiro.cache.ehcache.EhCacheManager">
		<property name="cacheManager" ref="ehCacheManager"></property>
	</bean>

	<!-- 3、spring封装缓存管理器 -->
	<bean id="springCacheManager" class="org.springframework.cache.ehcache.EhCacheCacheManager">
		<property name="cacheManager" ref="ehCacheManager" />
	</bean>

	<!-- 激活Spring注解扫描 -->
	<cache:annotation-driven cache-manager="springCacheManager" />
</beans>
```
 
4、修改application-shiro.xml配置文件

将 shiro 的缓存管理器，注入到安全管理器中，并且向real中注入
```
<!-- 配置安全管理器 -->
<bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
	<property name="realm" ref="bosRealm" />
	<!-- 将 shiro 的缓存管理器，注入到安全管理器中 -->
	<property name="cacheManager" ref="shiroCacheManager" />
</bean>

<!-- 配置realm -->
<bean id="uhoemRealm" class="com.leahshi.realm.UhoemRealm">
	<!-- 此处配置ehcache配置文件中的name "authorizationCacheName" 权限认证 而不是 身份认证 -->
	<property name="authorizationCacheName" value="menu"></property>
</bean>
```
 
5、常见问题：
被缓存到内存中的实体，需要实现 Serializable序列化接口，否则在传输过程中会报错

## 三、业务数据的缓存：
有些业务数据在操作过程中经常在查询，但是改动不是很大的情况下，我们也可以考虑对业务数据进行缓存。
spring提供了一些缓存数据的注解：
- @Cacheable(value="")：负责将方法的返回值加入到缓存中，通常加载查询方法上
- @CacheEvict(value="")：负责清除缓存，通常加载增删改的方法上

注意：
1、注解的value值，应该填写ehcache.xml文件中定义的缓冲区定义的name值。
2、当查询方法中传入参数时，需要在@Cacheable注解中加上key属性，key的值使用spring框架的SpEl表达式，传入方法参数标志每次缓存的数据都不一样。
比如，在分页列表查询中，需要传入每页显示条数和当前页码进行标识

```
// 对于有参数的查询
@Override
@Cacheable(value = "standard", key = "#pageable.pageNumber+'_'+#pageable.pageSize")
public Page<Standard> findAllStandardByPage(Pageable pageable) {
	Page<Standard> findAll = standardRepository.findAll(pageable);
	return findAll;
}
```


 
 
 
 
 
 
 
 













