---
title: ActiveMQ消息中间件
date: 2018-03-14 17:58:19
tags: [Java,中间件]
categories: work
---

ActiveMQ是一种消息中间件技术，它也是Apache组织开源的消息总线，同时也支持JMS规范。试想当实现注册功能时，通常需要用户接受手机验证码，防止恶意攻击，
当用户量很大的情况下，执行注册操作都需要程序去向邮箱服务器发送邮件，这个过程很容易产生高并发，所以需要引入消息中间件，
当需要发送邮件是，业务程序只需要向消息中间件产生一条消息，主要程序无需在等待调用邮件服务器发送邮件这个过程，这时候可以去处理其他请求了。
而在ActiveMQ消息队列平台获取短信信息，调用SMS短信服务平台向用户发送邮件。从而解决了服务间的耦合性，增加系统并发处理量。

<!-- more-->

## 下载与安装
在[官网](http://activemq.apache.org/download.html)中下载完成后，双击activemq.bat，即可启动。和ElasticSearch一样需要配置jdk，并配置环境变量JAVA_HOME。
ActiveMQ默认的浏览器访问端口号是8161，服务器端口是61616，访问http://localhost:8161，使用admin/admin登录即可。

在http://localhost:8161/admin/queues.jsp页面中可以看到一个表格显示消息信息：
- Name : 消息队列名称
- Number Of Pending Messages : 待消费信息（未出队列的数量）
- Number Of Consumers ： 消费者个数
- Messages Enqueued  ：进入队列总消息数
- Messages Dequeued  ：消费掉的数量

## 两种数据结构
ActiveMQ中存在两种模型，生产者和消费者；也有两种数据结构，queue队列和topic话题，俗称点对点和发布订阅。
### queue和topic区别
queue队列一条消息只能由一个消费者消费，但同一个队列可以关联多个消息生产者和消息消费者。如果多个消息消费者正在监听队列上的消息，
JMS消息服务器将根据“先来者优先”的原则确定由哪个消息消费者接收下一条消息。如果没有消息消费者在监听队列，消息将保留在队列中，直至消息消费者连接到队列为止。

topic消息订阅一条消息可以有多个消费者消费，并且默认存在失效时间，必须在指定时间生效（可以手动设置）。
同时topic模式分为持久化消息订阅和非持久化消息订阅，经过题主测试，在不配置持久化时，默认是非持久化的，**即当接受者不处于receive等待状态，就再也接受不到该topic消息了**

## Spring整合ActiveMQ开发
### queue点对点模式
1、导入jar包
```
<!-- activemq所需jar包-->
<dependency>
    <groupId>org.apache.activemq</groupId>
    <artifactId>activemq-all</artifactId>
    <version>5.14.0</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jms</artifactId>
    <version>4.3.3.RELEASE</version>
</dependency>

<!-- spring框架所需jar包 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>4.3.3.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>4.3.3.RELEASE</version>
</dependency>
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
```

2、生产者配置文件
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:jdbc="http://www.springframework.org/schema/jdbc" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:jpa="http://www.springframework.org/schema/data/jpa" xmlns:task="http://www.springframework.org/schema/task"
	xmlns:amq="http://activemq.apache.org/schema/core" xmlns:jms="http://www.springframework.org/schema/jms"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.1.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.1.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.1.xsd
		http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc-4.1.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.1.xsd
		http://www.springframework.org/schema/data/jpa 
		http://www.springframework.org/schema/data/jpa/spring-jpa.xsd
		http://www.springframework.org/schema/jms
        http://www.springframework.org/schema/jms/spring-jms.xsd
		http://activemq.apache.org/schema/core
        http://activemq.apache.org/schema/core/activemq-core-5.8.0.xsd ">

	<!-- 扫描包 -->
	<context:component-scan base-package="${扫描的包路径}" />

	<!-- ActiveMQ 连接工厂 -->
	<!-- 真正可以产生Connection的ConnectionFactory，由对应的 JMS服务厂商提供 -->
	<!-- 如果连接网络：tcp://ip:61616；未连接网络：tcp://localhost:61616 以及用户名，密码 -->
	<!-- <amq:connectionFactory id="amqConnectionFactory" brokerURL="tcp://localhost:61616" 
		userName="admin" password="admin" /> -->

	<bean id="amqConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
		<property name="brokerURL" value="tcp://localhost:61616"/>
		<property name="userName" value="admin"/>
		<property name="password" value="admin"/>
	</bean>

	<!-- Spring Caching连接工厂 -->
	<!-- Spring用于管理真正的ConnectionFactory的ConnectionFactory -->
	<bean id="connectionFactory" class="org.springframework.jms.connection.CachingConnectionFactory">
		<!-- 目标ConnectionFactory对应真实的可以产生JMS Connection的ConnectionFactory -->
		<property name="targetConnectionFactory" ref="amqConnectionFactory"></property>
		<!-- Session缓存数量 -->
		<property name="sessionCacheSize" value="100" />
	</bean>

	<!-- Spring JmsTemplate 的消息生产者 --> 
	<!-- Queue类型 -->
	<bean id="jmsQueueTemplate" class="org.springframework.jms.core.JmsTemplate">
		<!-- 这个connectionFactory对应的是我们定义的Spring提供的那个ConnectionFactory对象 -->
		<constructor-arg ref="connectionFactory" />
		<!-- 非pub/sub模型（发布/订阅），即队列模式 -->
		<property name="pubSubDomain" value="false" />
		<!-- 不持久1 持久化 2 -->
        <property name="deliveryMode" value="2"/>
        <!-- 存活时间 -->
        <!-- <property name="timeToLive" value="6000000"/> -->
        <!-- 开启服务质量的开关   开启之后上面的配置才会生效 -->
        <property name="explicitQosEnabled" value="true"/>
	</bean>

	<!-- Topic类型 -->
	<bean id="jmsTopicTemplate" class="org.springframework.jms.core.JmsTemplate">
		<!-- 这个connectionFactory对应的是我们定义的Spring提供的那个ConnectionFactory对象 -->
		<constructor-arg ref="connectionFactory" />
		<!-- <property name="defaultDestination" ref=""></property> -->
		<!-- pub/sub模型（发布/订阅） -->
		<property name="pubSubDomain" value="true" />
		<!-- 持久化 -->
        <property name="deliveryMode" value="2"/>
        <!-- 存活时间 -->
        <!-- <property name="timeToLive" value="6000000"/> -->
        <!-- 开启服务质量的开关   开启之后上面的配置才会生效 -->
        <property name="explicitQosEnabled" value="true"/>
	</bean>
  
</beans>
```

3、消费者配置文件
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:jdbc="http://www.springframework.org/schema/jdbc" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:jpa="http://www.springframework.org/schema/data/jpa" xmlns:task="http://www.springframework.org/schema/task"
	xmlns:amq="http://activemq.apache.org/schema/core" xmlns:jms="http://www.springframework.org/schema/jms"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.1.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.1.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.1.xsd
		http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc-4.1.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.1.xsd
		http://www.springframework.org/schema/data/jpa 
		http://www.springframework.org/schema/data/jpa/spring-jpa.xsd
		http://www.springframework.org/schema/jms
        http://www.springframework.org/schema/jms/spring-jms.xsd
		http://activemq.apache.org/schema/core
        http://activemq.apache.org/schema/core/activemq-core-5.8.0.xsd ">

	<!-- 扫描包 在项目中可能已经配过就无需在配置-->
	<context:component-scan base-package="${消费者所在包}" />

	<!-- ActiveMQ 连接工厂 -->
	<!-- 真正可以产生Connection的ConnectionFactory，由对应的 JMS服务厂商提供 -->
	<!-- 如果连接网络：tcp://ip:61616；未连接网络：tcp://localhost:61616 以及用户名，密码 -->
	<!-- <amq:connectionFactory id="amqConnectionFactory" brokerURL="tcp://localhost:61616" 
		userName="admin" password="admin" /> -->

	<bean id="amqConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
		<property name="brokerURL" value="tcp://localhost:61616" />
		<property name="userName" value="admin" />
		<property name="password" value="admin" />
	</bean>
	<!-- Spring Caching连接工厂 -->
	<!-- Spring用于管理真正的ConnectionFactory的ConnectionFactory -->
	<bean id="connectionFactory"
		class="org.springframework.jms.connection.CachingConnectionFactory">
		<!-- 目标ConnectionFactory对应真实的可以产生JMS Connection的ConnectionFactory -->
		<property name="targetConnectionFactory" ref="amqConnectionFactory"></property>
		<!-- 必须要配置这个 topic 消息订阅持久化才能生效 -->
		<property name="clientId" value="leahshi" />
		<!-- Session缓存数量 -->
		<property name="sessionCacheSize" value="100" />
	</bean>

	<!-- 事务管理器 -->
	<bean id="jmsTransactionManager"
		class="org.springframework.jms.connection.JmsTransactionManager">
		<property name="connectionFactory" ref="amqConnectionFactory" />
	</bean>


	<!-- 消息消费者 start -->
	<!-- 定义Queue监听器 -->
	<jms:listener-container destination-type="queue"
		container-type="default" connection-factory="connectionFactory"
		acknowledge="auto">
		<!-- 默认注册bean名称，应该是类名首字母小写 -->
		<jms:listener destination="spring_queue" ref="queueConsumer1" />
		<jms:listener destination="spring_queue" ref="queueConsumer2" />
	</jms:listener-container>

	<!-- 定义Topic监听器 -->
	<!-- <jms:listener-container destination-type="topic"
		container-type="default" connection-factory="connectionFactory"
		acknowledge="auto">
		<jms:listener destination="spring_topic" ref="topicConsumer1" />
		<jms:listener destination="spring_topic" ref="topicConsumer2" />
	</jms:listener-container> -->
	
	<jms:listener-container destination-type="durableTopic"
		container-type="default" connection-factory="connectionFactory"
		acknowledge="auto">
		<jms:listener destination="spring_topic" ref="topicConsumer1" subscription="test.customer"/>
		<jms:listener destination="spring_topic" ref="topicConsumer2" subscription="test.customer2" />
	</jms:listener-container>
	<!-- 消息消费者 end -->


</beans>
```


4、定义消费者（可以多定义几个）

```
@Component
public class QueueConsumer1 implements MessageListener{
	public void onMessage(Message message) {
		try {
			TextMessage textMessage = (TextMessage) message;
			System.out.println(textMessage.getText());
		} catch (JMSException e) {
			e.printStackTrace();
		}
	}
}
```

5、定义生产者

```
package com.activemq.producer;

import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.Session;
import javax.jms.TextMessage;

import org.junit.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.jms.core.JmsTemplate;
import org.springframework.jms.core.MessageCreator;
import org.springframework.stereotype.Component;

/**
 * 生产队列
 */
@Component
public class ProducerTest {
	
	@Autowired
	// 如果是topic队列 注入jmsTopicTemplate
	@Qualifier("jmsQueueTemplate")
	private JmsTemplate jmsTemplate;
	
	public void send(String queueName,final String message){
		jmsTemplate.send(queueName,new MessageCreator() {
			public Message createMessage(Session session) throws JMSException {
			 TextMessage textMessage = session.createTextMessage(message);
				return textMessage;
			}
		});
	}
}

```

6、测试与总结
```
package com.activemq.test;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import com.activemq.producer.ProducerTest1;
import com.activemq.producer.ProducerTest2;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext-mq-produce.xml")
public class ActivemqProducerTest {
	 
	@Autowired
	private ProducerTest queueProducer;
	 
	@Test
	public void fun(){
		queueProducer.send("spring_queue", "hello,2017_queue");
	}
}

```

运行完生产者方法后，在http://localhost:8161/admin/queues.jsp 列表上看到‘spring_queue’队列已产生一条信息，
消费者测试，只需要写个死循环（保证线程开启状态，在项目中SMS系统会一致保持启动状态），并注入消费者配置文件，即可在控制台中输入‘hello,2017_queue’

**以上queue队列只发送了一条消息，尽管jms模板中配置了两个消费者，但只打印一条，证明了queue队列中一条消息只能有一个消费者消费**

### topic消息订阅模式
topic消息订阅模式，jar包、配置文件、消费者代码同上，生产者，只需要注入生产者配置文件中的jmsTopicTemplate即可。
需要注意的是前面提及，如果没有配置消息订阅持久化，测试时候，需要先将消费者启动，在启动生产者，否则将接受不到消息。但是在spring配置文件生产者中配置如下后就可以实现消息持久化：
在配置JmsTemplate是开启持久化
```
<!--开启持久化，topic模式发送的消息，会保存在broker中，是topic持久化的前提 默认是1 不持久化-->
<property name="deliveryMode" value="2"/>
```

测试结果打印出两条信息，虽然topic只发送一条信息，但是JmsTemplate中配置了两个消费者，所以打印两条信息，也证明了topic中，一条信息可以有多个人消费

## 应用场景分析
topic适用于群发、消息订阅，信息都是一致的，queue队列使用与发送验证码等唯一性的信息。