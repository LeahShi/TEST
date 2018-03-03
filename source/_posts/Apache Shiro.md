---
title: Apache Shiro
date: 2017-09-15 10:51:23
tags: [Java,权限控制]
categories: work
---

权限控制和用户认证在管理系统中是很常见也很重要的一块，根据系统中使用到的Apache Shiro安全框架加以总结。
Apache Shiro是一款强大、灵活的开源安全管理框架，以十分优雅的方式处理authentication（身份验证）、authorization（授权）、
Session Management（会话管理）、Cryptography（安全数据加密）、 Web Integration web 系统集成等...
 
<!-- more -->
 
## 一、概述 
任何的权限控制，大致分为粗粒度的URL级别和细粒度方法级别，在shiro中也不例外。
在shiro中，URL级别的权限控制其实现方式是有一个shiroFilter过滤器来实现；
而细粒度的权限控制具体是由一个@RequiresPermissions(value = {"",""})注解实现，其本质还是aop面向切面的思想，返回代理对象，但是在shiro中必须要使用cglib的动态代理
原因是因为jdk动态代理，代理对象和原对象实现同一个接口，相当于兄弟关系，所以注解会失效；而采用cglib方式，代理对象和原对象是有继承关系的。
 


## 二、使用shiro实现用户身份验证
### 2.1、需求：
用户未登录状态无法访问其他页面，只能跳转登录页

### 2.2、实现：
1、导入jar包
题主使用的maven坐标，直接引入shiro-all，在[官网](http://shiro.apache.org/download.html)中详细介绍了其他jar包的作用，基本上所有jar包都有涉及，所以直接引入一个总的jar包
```
<dependency>
	<groupId>org.apache.shiro</groupId>
	<artifactId>shiro-all</artifactId>
	<version>${shiro.version}</version>
</dependency>
```
 
2、配置web.xml
上面提到shiro的粗粒度URL权限控制是基于Filter的，所以需要在web.xml文件里配置一个shiroFilter的过滤器（注意名称必须为这个，如果项目中引入了struts的过滤器，需要将shiroFilter放在其上，struts才不会拦截这个资源）
```
<!-- shiro 权限控制 -->
<filter>
	<filter-name>shiroFilter</filter-name>
	<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
</filter>
<filter-mapping>
	<filter-name>shiroFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```
 
3、配置spring配置文件
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



	<!-- 
		配置核心过滤器:
			注意：此处的id和web.xml中的过滤器名称要保持一致
	 -->
	<bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
		<!-- 安全管理器 -->
		<property name="securityManager" ref="securityManager" />
		<!-- 未认证 -->
		<property name="loginUrl" value="/login.html" />
		<!-- 已登录 -->
		<property name="successUrl" value="/index.html" />
		<!-- 认证后无权限 -->
		<property name="unauthorizedUrl" value="/unauthorized.html" />
		<!-- 
			权限控制：角色 用户 权限 URL控制过滤器规则: 
				anon: 未登录状态也可以访问的资源 
				authc:已登录状态访问的资源 
				roles：特定角色才能访问 
				perms：特定权限才能访问 
				user：特定用户才能访问 
				port 需要特定端口才能访问 
				reset 根据指定
				HTTP 请求访问才能访问 
		-->
		<property name="filterChainDefinitions">
			<value>
				<!-- "**" 表示所有层级目录 -->
				/css/** = anon
				/js/** = anon
				/images/** = anon
				/login.html* = anon
				/services/** = anon
				/validatecode.jsp* = anon
				/userLogin = anon
				<!-- 只有在权限表中关键字中为 courier:list 的角色才有权限查看该页面 -->
				/pages/base/courier.html* = perms[courier:list]
				<!-- 只有在角色表中关键字为base的角色才有权限查看该页面 -->
				/pages/base/area.html* = roles[base]
				/** = authc
			</value>
		</property>
	</bean>


	<!-- 配置安全管理器 -->
	<bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
		<property name="realm" ref="uhoemRealm" />
	</bean>
	
	<!-- 配置realm -->
	<bean id="uhoemRealm" class="com.leahshi.realm.uhoemRealm"></bean>

	<!-- 生命周期后处理器 -->
	<bean id="lifecycleBeanPostProcessor" class="org.apache.shiro.spring.LifecycleBeanPostProcessor"></bean> 
	
</beans>
``` 

以上配置文件中，需要配置shiroFilter，该名称和web.xml中名称一致。这个对象注入了安全管理器这个核心对象，并且配置了定义了权限控制的一些规则，
未登录状态下跳转页面，登录成功后的跳转页面，权限不足的页面，以及配置了一些特定用户可以访问的资源以及权限信息【重点，见注释】



4、表现层UserAction，创建登录方法
```
@Action(value = "userLogin", results = { @Result(name = "success", type = "redirect", location = "index.html"),
			@Result(name = "login", type = "redirect", location = "login.html") })
public String userLogin() {
	// 获取subject对象，相当于User
	Subject subject = SecurityUtils.getSubject();
	
	// 创建token
	AuthenticationToken token = new UsernamePasswordToken(user.getUsername(), user.getPassword());

	try {
		subject.login(token); 
		return SUCCESS;
	} catch (AuthenticationException e) {
		e.printStackTrace();
		return LOGIN;
	}
}
```

以上调用subject的login方法后，shiro底层会操作核心对象SecurityManager安全管理器，在spring配置文件中为SecurityManager注意了一个Realm对象，所以会紧接着调用自动一的Realm对象

5、创建Reaml对象
```
package com.leahshi.bos.realm;

import java.util.List;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.IncorrectCredentialsException;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authc.UnknownAccountException;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.subject.Subject;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.leahshi.bos.domain.system.Permission;
import com.leahshi.bos.domain.system.Role;
import com.leahshi.bos.domain.system.User;
import com.leahshi.bos.service.system.IRoleService;
import com.leahshi.bos.service.system.IUserService;
 
public class BosRealm extends AuthorizingRealm {
	@Autowired
	private IUserService userService;

	@Autowired
	private IRoleService roleService;

	/**
	 * 认证信息
	 */
	@Override
	public AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException { 
		// 将token强转为所需的子类token
		UsernamePasswordToken usernamePasswordToken = (UsernamePasswordToken) token;
		 
		User user = userService.findByUsername(usernamePasswordToken.getUsername());
		try {
			if (user == null) {
				// 未查到该用户
				System.out.println("该用户不存在");
				return null;
			} else {
				 
				/**
				 * 参数分别为：登录后保存到subject中的信息，用户的密码，realm 名称
				 */
				return new SimpleAuthenticationInfo(user, user.getPassword(), getName());
			}
		} catch (IncorrectCredentialsException e) {
			e.printStackTrace();
			System.out.println("密码错误失败！");
			return null;
		} catch (UnknownAccountException e) {
			e.printStackTrace();
			System.out.println("该账户不存在！");
			return null;
		}

	} 
}

```

因为此次登录时按照用户名和密码登录的，所以首先需要将token强转一下；shiro和通常的做法不同，只需要根据对应的用户名查找出用户，如果账户存在 realm自动根据用户输入的密码 匹配 查询出来的密码  
另外，在用户名不存在是会报一个UnknownAccountException的异常，密码错误报IncorrectCredentialsException异常，需要留意。
最后千万别忘记，将自动一的Realm放入到spring容器中，并将Realm注入到SecurityManager中！！！

6、业务层和数据访问层，只有一个查询的方法，略

### 2.3、总结：
在这个认证（登录）案例中，并没有体现出shiro权限控制框架的强大之处，可能我们自定义一个filter也可以实现这些功能，拦截某些页面。
初学者可能还会觉得很麻烦，在表现层调用业务层之前还要在依次操作Subject - SecurityManager - Realm，最后还需要调用业务层和dao，
但是接下来的授权管理会真正体现它的强大之处！

我们可以根据用户认证完成后，存储到subject中的信息，传入到授权的方法中，
根据当前登录的用户在数据库中查询其对应的所有角色信息，根据角色信息可以查询到所有的权限信息，有了权限信息可以实现动态菜单的功能，
不同用户登录系统，根据权限显示不同的菜单选项
 
 
 
## 三、使用shiro实现权限控制
提及权限控制，就要涉及到四个实体：用户、角色、权限、菜单 表关系如下：
![](/images/auth.png)

添加角色信息会关联菜单项，添加用户会关联角色，从而达到不同用户登录系统生成动态菜单项。

### 3.1、基于粗粒的URL级别权限控制： 
在上面提供的spring配置文件中，shiroFilter这个过滤器注入了filterChainDefinitions这个属性，根据上面介绍的就可以达到特定权限和角色可以查看页面的需求。
其原理是由过滤器实现

### 3.2、基于细粒度方法级别权限控制：
1、在spring配置文件中配置开启权限注解设置，并且要指定动态代理的模式为cglib

```
<!-- 激活注解 开启shiro注解模式 -->
<bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator"
	depends-on="lifecycleBeanPostProcessor">
	<!-- 使用cglib动态代理 -->
	<property name="proxyTargetClass" value="true"></property>
</bean>

<!-- spring容器封装securityManager对象 -->
<bean class="org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor">
	<property name="securityManager" ref="securityManager"></property>
</bean> 
```

2、在自定义realm类中的doGetAuthorizationInfo 添加权限信息的关键字： 

```
/**
 * 权限信息
 */
@Override
public AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection pc) { 
	// 查询当前用户对应的权限
	SimpleAuthorizationInfo authorizationInfo = new SimpleAuthorizationInfo();
	Subject subject = SecurityUtils.getSubject();
	User user = (User) subject.getPrincipal();

	// 查询当前用户对应的角色信息
	List<Role> roles = roleService.findByUserId(user.getId());
	for (Role role : roles) {
		// 为用户添加角色权限  在配置文件中有定义能查看的页面权限限制
		authorizationInfo.addRole(role.getKeyword());
		// 为用户添加权限信息
		for (Permission permission : role.getPermissions()) {
			authorizationInfo.addStringPermission(permission.getKeyword());
		}
	}
	return authorizationInfo;
}
```

3、在具体需要权限控制的类上添加注解：
常用的注解有：
- 需要特定的角色：@RequiresRoles(value={})
- 需要特定的权限：@RequiresPermissions(value = { "keyword1","keyword2" }) 
- 验证是否登录：@RequiresAuthentication
- 验证用户是否被记忆，一种是成功登录的，另外一种是被记忆的：@ RequiresUser


 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 

















