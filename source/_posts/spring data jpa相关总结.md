---
title: spring data jpa相关总结
date: 2017-09-15 10:51:23
tags: [Java,数据持久层]
categories: work
---

JPA最早是由sum公司提出的一套java持久化规范，sum公司最早的野心是统一持久层框架，可惜最终被收购，而且只统一了关系型框架（hibernate等）。
spring公司再次基础上又提出spring data规范，不仅是关系型框架，非关系型redis数据库，也被统一规范。此处总结的spring data jpa就是spring data接口规范实现之一。
 
<!-- more -->

用过spring data jpa后，个人体验的确是大大提高了对持久层操作的开发效率，并且简化了很多数据访问操作。
特别是它的命名查询，只需要按照规范取方法名称，继承jpa接口，就可以做到无需实现类，无需sql语句！！话不多说，开始总结~


## 一、基本介绍
### 1.1、继承体系
Repository（空接口） ————  CrudRepository（接口） ———— PagingAndSortingRepository（接口） ———— JpaRepository（接口） --- SimpleJpaRepository（实现类）
JpaSpecificationExecutor（接口） ---  SimpleRepository（实现类）   

### 1.2、主要对象详解
- Repository：空接口，仅仅是一个标识，表明任何继承它的均为仓库接口类
- CrudRepository：定义了简单的增删改查的方法
- PagingAndSortingRepository：定义了分页和排序的方法
- JpaRepository：继承了以上两者，并且定义了批量删除的方法，开发中最常用。
- JpaSpecificationExecutor：传入一个Specification构造条件对象，进行条件、分页、排序查询
- SimpleRepository：这个实现类很强大，它实现了以上接口所有定义的方法，我们没有直接的使用到它，但有简洁使用到。


### 1.3、spring data jpa不需要实现类的原因：
使用spring data jpa 时，根据对数据操作的需求，dao接口继承对应的接口时，可以不需要添加该接口的实现类就可以直接在service注入该接口，原因是因为：
    若我们定义的接口继承了Repository，则该接口会被IOC容器识别为一个Repository Bean，纳入到IOC容器中，进而可以在该接口中定义满足一定规范的方法。
在spring配置文件中配置了base-package指定了Repository Bean所在的位置，在这个包下的所有的继承了Repository的接口都会被IOC容器识别并纳入到容器中，
如果没有继承Repository则IOC容器无法识别。 
**当我们在service中可以直接注入定义的接口（没有实现类），是因为IOC容器中实际存放了继承了Repository的接口的实现类，而这个实现类由spring aop 返回代理对象完成。
没有编写实现类时，返回的实现类和SimpleRepository实现同一个接口，相当于jdk动态代理，有接口的情况下，返回的是该接口的子类，
没有接口，使用的是cglib,返回的是该接口的实现类，即SimpleJpaRepository的子类**

### 1.4、如果在特殊情况需要使用到repository的实现类：
以UserRepository为例：
- 在UserRepository接口同一个包下创建其实现类UserRepositoryImpl（实现类命名规则为： 接口名称+Impl）
- 在UserRepository接口中定义实现类UserRepositoryImpl中需要实现的login方法，无需实现对应的接口（否则接口中的所有方法都要实现）
- 注入是依然是注入的UserRepository接口
 
**由于Spring data jpa的命名查询很强大，可以解决一些简单的查询，可以无需使用到sql语句，所以基本上无需使用到实现类，所以就不需要担心在同一个包既有接口，又有实现类的问题** 

## 二、简单使用方式
### 2.1、配置文件 
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:jdbc="http://www.springframework.org/schema/jdbc" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:jpa="http://www.springframework.org/schema/data/jpa" xmlns:task="http://www.springframework.org/schema/task"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
		http://www.springframework.org/schema/data/jpa 
		http://www.springframework.org/schema/data/jpa/spring-jpa.xsd">
    
    <!-- 扫描 @Server @Controller @Repository -->
    <context:component-scan base-package="spring 注解扫描包" />
    
    <!-- 加载properties文件 -->
    <context:property-placeholder location="classpath:config.properties" />
    	
    	
	<!-- 配置c3p0连接池 -->
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="user" value="${jdbc.user}" />
		<property name="password" value="${jdbc.password}" />
		<property name="driverClass" value="${jdbc.driver}" />
		<property name="jdbcUrl" value="${jdbc.url}"></property>
	</bean>

	<!-- 整合JPA配置 -->
	<bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
		<property name="dataSource" ref="dataSource" />
		<property name="packagesToScan" value="实体扫描包" />
		<property name="persistenceProvider">
			<bean class="org.hibernate.ejb.HibernatePersistence" />
		</property>
		<property name="jpaVendorAdapter">
			<bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter">
				<!-- 自动生成表结构、数据库方言 -->
				<property name="generateDdl" value="true" />
				<property name="database" value="ORACLE" />
				<property name="databasePlatform" value="org.hibernate.dialect.Oracle10gDialect" />
				<property name="showSql" value="true" />
				
			</bean>
		</property>
		<!-- jpa方言设置 -->
		<property name="jpaDialect">
			<bean class="org.springframework.orm.jpa.vendor.HibernateJpaDialect" />
		</property>
		<property name="jpaPropertyMap">
			<map>
				<entry key="hibernate.query.substitutions" value="true 1, false 0" />
				<entry key="hibernate.default_batch_fetch_size" value="16" />
				<entry key="hibernate.max_fetch_depth" value="2" />
				<entry key="hibernate.generate_statistics" value="true" />
				<entry key="hibernate.bytecode.use_reflection_optimizer" value="true" />
				<entry key="hibernate.cache.use_second_level_cache" value="false" />
				<entry key="hibernate.cache.use_query_cache" value="false" />
			</map>
		</property>
	</bean>
	
	<!-- 配置事务管理器 -->
	<bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
		<property name="entityManagerFactory" ref="entityManagerFactory"></property>
	</bean>
	
	<tx:annotation-driven transaction-manager="transactionManager"/>
	
	<!-- 整合spring data jpa  -->
	<jpa:repositories base-package="持久化层包扫描"></jpa:repositories>
</beans>
```
### 2.2、简单总结 
1、持久化层操作时，都会有一个对象帮助我们操作数据库，从最原始的jdbc的statement对象，c3p0连接池的QueryRunner对象，
hibernate中的session，spring data jpa中称为entityManagerFactory，mybatis中的sqlSession对象...
2、通过 <jpa:repositories base-package="持久化层包扫描"></jpa:repositories> 扫描 Repository所在的包全名，就可以找到对应的接口，无需使用注解！
3、配置事务管理器，根据不同的数据持久层操作对象，事务管理器对象也不同：
Hibernate中是HibernateTransactionManager，spring data jpa中是JpaTransactionManager

## 三、命名查询和自定义sql
上文提及spring data jpa非常强大，可以无需实现类无需sql语句，只要方法的命名符合规范，就可以实现相应的功能。
比如，用户登录时，校验用户名密码，只需要在UserRepository中定义以下方法即可：
User findByUsernameAndPassword(对应参数);
以上username 和password是User这个实体的一个字段。通过findByXxx即可根据字段xxx查询。

### 3.1、其他规则：
- findByUsernameOrPassword
- findByStartDateBetween 
- findByAgeLessThan(Equal) 查询年纪小于(等于)
- findByAgeGreaterThan(Equal) 查询年纪大于(等于)
- findByAgeIs(Not)Null	   非空/为空判断
- findByUsername(Not)Like  模糊查询 
- findByUsernameOrderByAgeDesc 根据年龄的降序查询用户名
- findByAge(Not)In(Collection<Age> ages) 年龄在/不再某个范围
...

注意：
1、以上命名查询只针对**单表查询**，如果涉及多表查询，需要使用下文提到的JpaSpecificationExecutor接口进行条件查询！
2、如果使用findByXxx，而xxx并不是实体类中的字段，并且没有自定义sql语句，会报PropertyReferenceException: No property xx for type..的错误


### 3.2、自定义sql
当执行其他增删改或复杂查询方法时，spring data jpa也提供了自定义sql操作数据库的能力，比如：  
```
@Query(value="update TakeTime set status=?2 where id=?1",nativeQuery=false)
@Modifying
public void batchUpdateDel(Integer id,Integer status);
```

使用@Query注解在对应的方法上面，value属性后面值为响应的SQL/HQL语句，spring data jpa默认采用的和hibernate一样HQL语句，
当参数列表的顺序和HQL中所需参数顺序不同时，可以在？后面加入数字来对应相应的参数！
nativeQuery 属性值为一个布尔类型，默认值为false,当为true是，value后面的值应该为SQL语句
**当对表进行修改操作时，需要多加一个@Modifying注解**  



## 四、多表查询，分页条件查询，排序查询
使用Spring data jpa使得分页条件查询（多表）变的很容易，（采用的是Criteria语句查询）：

```
@Action(value = "findAllOrderByPage", results = { @Result(name = "success", type = "json") })
public String findAllOrderByPage() {
	// 构造Pageable分页对象 
	// 参数列表：传入当前页，每页显示条数，根据某个字段升降序排序
	Pageable pageable = new PageRequest(page - 1, rows,new Sort(new Sort.Order(Sort.Direaction.DESC,"age")));
	// 分页条件查询 使用Specification 接口
	Specification<Order> specification = new Specification<Order>() {
	 
		@Override
		public Predicate toPredicate(Root<Order> root, CriteriaQuery<?> query, CriteriaBuilder cb) {
			// 定义List集合容纳条件对象
			List<Predicate> list = new ArrayList<Predicate>();
			// 1、Order表中 单表操作 以下是比较字段的值而不是名称！！
			// 判断工号是否非空 模糊查询
			if (StringUtils.isNotBlank(model.getOrderNum())) {
				Predicate p1 = cb.like(root.get("orderNum").as(String.class), "%" + model.getOrderNum() + "%");
				list.add(p1);
			}

			// 判断商品名称 模糊查询，%号不能漏掉
			if (StringUtils.isNotBlank(model.getName())) {
				Predicate p2 = cb.like(root.get("name").as(String.class), "%" + model.getName() + "%");
				list.add(p2);
			}

			// 判断类型 等值查询
			if (StringUtils.isNotBlank(model.getType())) {
				Predicate p3 = cb.equal(root.get("type").as(String.class), model.getType());
				list.add(p3);
			}

			// 2、Order和Standard多表操作 此处的standard必须为Order表中字段名称
			// 返回的是Standard表中的root，不写连接方式默认是内连接！！
			Join<Object, Object> standardRoot = root.join("standard", JoinType.INNER);
			if (model.getStandard() != null && StringUtils.isNotBlank(model.getStandard().getName())) {
				Predicate p4 = cb.equal(standardRoot.get("name").as(String.class), model.getStandard().getName());
				list.add(p4);
			}

			// 四个条件去并集 接受的是array数组，需要将集合转化为数组
			return cb.and(list.toArray(new Predicate[0]));
		}
	};
	
	// 在service中直接调用 OrderRepository中findAll(specification, pageable); 方法即可
	Page<Courier> pageData = orderService.findAllOrderByPage(specification, pageable);
	
	// 压入值栈
	Map<String, Object> result = new HashMap<String, Object>();
	result.put("total", pageData.getTotalElements());
	result.put("rows", pageData.getContent());

	ActionContext.getContext().getValueStack().push(result); 
	return SUCCESS;
}
```

以上主要有Specification和Predicate两个对象

## 五、常见问题：
spring data jpa延迟加载问题：
```
Caused by: org.hibernate.LazyInitializationException: failed to lazily initialize a collection of role: com.leahshi.domain.XXX, could not initialize proxy - no Session
```
解决方案：
1、在实体类上对应字段的get方法上面加上@JSON(serialize = false)注解，返回数据时不返回该字段
2、在web.xml中配置OpenSessionInViewFilter将session关闭放在web层，并且在service层要加上事务控制@Tractional






























  