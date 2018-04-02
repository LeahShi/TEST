---
title: spring data redis使用
date: 2018-01-18 19:48:32
tags: [Redis]
categories: work
---

接上篇，spring框架对很多优秀的技术进行了封装，此篇总结项目中使用到了spring data redis

<!-- more-->

## 1、导入pom依赖
```
<!-- spring相关jar包-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>4.3.6.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>4.3.6.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>4.3.6.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
    <version>4.3.3.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>4.3.6.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>4.3.2.RELEASE</version>
</dependency>

<!-- redis相关jar包-->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.8.1</version>
</dependency>
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-redis</artifactId>
    <version>1.7.2.RELEASE</version>
</dependency>
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
```

## 2、spring配置文件
因为redis本质也是数据库，所以也需要配置连接工厂、模板，redis连接相关配置

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 扫描属性文件-->
    <context:property-placeholder location="classpath*:config/*.properties"/>

    <!-- redis 相关配置 -->
    <bean id="poolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <!-- 最大空闲数-->
        <property name="maxIdle" value="${redis.maxIdle}"/>
        <!-- 连接最长等待时间-->
        <property name="maxWaitMillis" value="${redis.maxWait}"/>
        <!-- 在提取一个 jedis 实例时，是否提前进行验证操作；如果为 true，
            则得到的 jedis 实例均是可用的-->
        <property name="testOnBorrow" value="${redis.testOnBorrow}"/>
    </bean>

    <!-- 配置连接工厂-->
    <bean id="JedisConnectionFactory" 
          class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory"
          p:host-name="${redis.host}" p:port="${redis.port}" p:password="${redis.pass}"
          p:pool-config-ref="poolConfig"/>

    <!-- 配置模板-->
    <bean id="redisTemplate"
          class="org.springframework.data.redis.core.RedisTemplate">
        <property name="connectionFactory" ref="JedisConnectionFactory"/>
    </bean>

</beans>
```

## 3、属性文件redis-config.properties，定义redis配置中的常量

```
<!-- 连接ip和端口 -->
redis.host=127.0.0.1
redis.port=6379
<!-- 配置密码 -->
redis.pass=
<!-- 指定存储的数据库，在redis.conf文件中定义了16个数据库 -->
redis.database=0
<!-- 最大空闲数 -->
redis.maxIdle=300
<!-- 连接最长等待时间 -->
redis.maxWait=3000
<!-- 开启验证 -->
redis.testOnBorrow=true
```


## 4、操作五种数据类型
### A.String类型
```
/**
 * 使用redis 存储 string
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext-redis.xml")
public class StoreString {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    public void add(){
        redisTemplate.boundValueOps("name").set("本上神");
    }

    @Test
    public void getValue(){
        String value = (String) redisTemplate.boundValueOps("name").get();
        System.out.println(value);
    }

    @Test
    public void delete(){
        redisTemplate.delete("name");
    }

}
```

### B.Hash类型
```
/**
 * 存储Hash类型的集合
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext-redis.xml")
public class StoreHash {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    public void add(){
        redisTemplate.boundHashOps("hashTest").put("name","jack");
        redisTemplate.boundHashOps("hashTest").put("age",23);
        redisTemplate.boundHashOps("hashTest").put("gender","boy");
    }

    @Test
    public void get(){
        // 获取所有的key
        Set keys = redisTemplate.boundHashOps("hashTest").keys();
        List values = redisTemplate.boundHashOps("hashTest").values();
        System.out.println(keys);
        System.out.println(values);

        // 根据key获取value
        String str = (String) redisTemplate.boundHashOps("hashTest").get("name");
        System.out.println(str);
    }


    @Test
    public void remove(){
        redisTemplate.boundHashOps("hashTest").delete("age","gender");
    }

    @Test
    public void delete(){
        redisTemplate.delete("hashTest");
    }
}
```

### C.List类型

``` 
/**
 * 存储List类型的集合
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext-redis.xml")
public class StoreList {

    @Autowired
    private RedisTemplate redisTemplate;

    /**
     *  左压栈 - 相当于 栈内存存储方式 先入后出，后入先出
     */
    @Test
    public void leftPush(){
        redisTemplate.boundListOps("student").leftPush("sl");
        redisTemplate.boundListOps("student").leftPush("zyt");
        redisTemplate.boundListOps("student").leftPush("zzy");
    }

    /**
     *  右压栈 -- 相当于 队列数据结构 先入先出，后入后出
     */
    @Test
    public void rightPush(){
        redisTemplate.boundListOps("classes").rightPush("class 01");
        redisTemplate.boundListOps("classes").rightPush("class 02");
        redisTemplate.boundListOps("classes").rightPush("class 03");
        redisTemplate.boundListOps("classes").rightPush("class 01");
    }

    @Test
    public void get(){
        List student = redisTemplate.boundListOps("student").range(0, 10);
        List classes = redisTemplate.boundListOps("classes").range(0, 10);
        System.out.println(student);
        System.out.println(classes);
    }

    // 查找某个元素
    @Test
    public void searchByIndex(){
        String student = (String) redisTemplate.boundListOps("student").index(0);
        System.out.println(student);
    }

    // 移除List集合的元素 ，可以指定删除数量
    @Test
    public void remove(){
        redisTemplate.boundListOps("classes").remove(1,"class01");
    }

    // 移除整个集合
    @Test
    public void delete(){
        redisTemplate.delete("classes");
    }
}
```

### D.Set类型
```
/**
 * 使用redis存储set集合
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext-redis.xml")
public class StoreSet {

    @Autowired
    private RedisTemplate redisTemplate;


    @Test
    public void add(){
        redisTemplate.boundSetOps("friend").add("zz","gg","tt","aa");
    }



    // 调用 members()方法 列举所有成员
    @Test
    public void get(){
        Set friends = (Set) redisTemplate.boundSetOps("friend").members();
        System.out.println(friends);
    }

    /**
     * 删除Set集合中值
     */
    @Test
    public void remove(){
        redisTemplate.boundSetOps("friend").remove("aa","老高");
    }

    /**
     * 删除增个Set集合
     */
    @Test
    public void delete(){
        redisTemplate.delete("friend");
    }
}

```

## 5、总结与注意
- 需要注入redisTemplate(前提已经在spring配置文件中已经配置)，通过redisTemplate.bound"+${redis支持存储的五种数据类型}+"Ops("${redis存储的key}")方法调用增删改方法进行操作，除了String类型的方法名为boundValueOps()
- 删除整个value直接调用redisTemplate.delete("${key}");
- List数据类型可以允许有重复的值，所以在删除的时候也可以指定删除的个数；并且因为是有序的，可以调用index(idx)来获取对应的元素
- List数据类型很特殊，可以模拟栈（左压栈）和队列（右压栈）两种数据类型
- Hash类型，可以向HashSet集合那样获取所有的key和value，根据key来获取value
- Set类型，获取所有的元素调用的是members()方法..




