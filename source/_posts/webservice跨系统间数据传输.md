---
title: webservice跨系统间数据传输
date: 2018-02-14 19:30:23
tags: [Java,webservice]
categories: work
---

WebService是一个平台独立的，低耦合的，自包含的、基于可编程的web的应用程序，可使用开放的XML标准来描述、发布、发现、协调和配置这些应用程序，用于开发分布式的互操作的应用程序。
简单来说，Webservice主要应用于多个系统进行分布式开发时，跨系统间的数据通信。在前台可以通过ajax jsonp实现跨域，但是使用ajax跨域会存在代码侵入和不安全性，所以往往就需要服务器端来处理了。


<!-- more-->

## 两种方式
Webservice CXF主要有两种数据传输方式：一种是基于SOAP协议，使用XML传输的JAX-WS，另一种是基于HTTP协议，使用Restful风格的JAX-RS，
RS既可以使用XML数据格式进行传输，也可以使用JSON格式传输。通常我们会使用RS方式，因为restful风格会简化我们的开发。

区别：
SOAP偏向于面向活动，有严格的规范和标准，包括安全，事务等各个方面的内容，同时SOAP强调操作方法和操作对象的分离，有WSDL文件规范和XSD文件分别对其定义。
而REST强调面向资源，只要我们要操作的对象可以抽象为资源即可以使用REST架构风格。

Restful风格：
一种软件架构风格，提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制，同时基于HTTP协议，支持多种消息格式，比如XML、JSON。
在服务器端，应用程序状态和功能可以分为各种资源。资源是一个有趣的概念实体，它向客户端公开。资源的例子有：应用程序对象、数据库记录、算法等等。每个资源都使用 URI 得到一个唯一的地址。所有资源都共享统一的接口，
以便在客户端和服务器之间传输状态。使用的是标准的 HTTP 方法，比如 GET、PUT、POST 和 DELETE。
 
## Spring整合Webservice开发

### 基于JAX-WS方式

1、导入jar包
```
<!-- CXF WS开发 -->
<dependency>
    <groupId>org.apache.cxf</groupId>
    <artifactId>cxf-rt-frontend-jaxws</artifactId>
    <version>3.0.1</version>
</dependency>

<!-- spring框架需要jar包-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>4.1.7.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>4.1.7.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>4.1.7.RELEASE</version>
</dependency>

<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
```

2、接口和实现类
```
@WebService
public interface IUserService {
	@WebMethod
	public String sayHello(String name);

	@WebMethod
	public List<Car> findCarsByUser(User user);
}
```

注意：接口上需要加上@WebService注解，方法上需要加入@WebMethod注解
实现类上需要加上@WebService(endpointInterface = "${接口的全限定名}", serviceName = "${对外发布的服务名称}")
 



3、配置文件
A、Web.xml:
```
<!-- spring配置文件位置 -->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
</context-param>
<!-- spring核心监听器 -->
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

<servlet>
    <servlet-name>CXFService</servlet-name>
    <servlet-class>org.apache.cxf.transport.servlet.CXFServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>CXFService</servlet-name>
    <url-pattern>/services/*</url-pattern>
</servlet-mapping>
```

B、spring配置文件
服务器端： 
``` 
<!-- Spring配置文件中需要加上名称空间 -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:jaxws="http://cxf.apache.org/jaxws"
	xsi:schemaLocation="
	http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
	http://cxf.apache.org/jaxws http://cxf.apache.org/schemas/jaxws.xsd">

	<jaxws:server id="userService" address="/userService" serviceClass="${接口全限定名}">
		<jaxws:serviceBean>
			<bean class="${接口实现类全限定名称}" />
		</jaxws:serviceBean>
	</jaxws:server>
	
</beans>
```

客户端：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:jaxws="http://cxf.apache.org/jaxws"
	xsi:schemaLocation="
	http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
	http://cxf.apache.org/jaxws http://cxf.apache.org/schemas/jaxws.xsd">

	<jaxws:client id="userServiceClient" serviceClass="${接口全限定名}" 
		address="${发布地址}/services/userService" >
		<!-- 日志信息，会打印一些连接时的信息-->
		<jaxws:inInterceptors>
			<bean class="org.apache.cxf.interceptor.LoggingInInterceptor"/>
		</jaxws:inInterceptors>
		<jaxws:outInterceptors>
			<bean class="org.apache.cxf.interceptor.LoggingOutInterceptor" />
		</jaxws:outInterceptors>
	</jaxws:client>
</beans>
```

4、客户端测试类
``` 
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="classpath:applicationContext-test.xml")
public class JAXWS_Spring_Test {
	@Autowired
	private IUserService userService;
	
	@Test
	public void fun(){
		System.out.println(userService.sayHello("世界"));
	}
}

```

5、测试与总结  
启动服务器，访问地址+web.xml下配置的映射路径+服务端spring配置文件中的address值+"?wsdl" ,就可已在浏览器中查看到一个xml形式的WSDL文件
再运行客户端测试方法，即可在控制台中打印出信息

以上代码可以看到，使用JAX-WS方式进行数据传输，服务器端和客户端都必须要有相同的接口，使用起来比较不方便，所以更倾向于JAX-RS方式



### 基于JAX-RS方式

1、导入jar包

```
<!-- cxf 进行rs开发 必须导入 -->
<dependency>
    <groupId>org.apache.cxf</groupId>
    <artifactId>cxf-rt-frontend-jaxrs</artifactId>
    <version>3.0.1</version>
</dependency>

<!-- 日志引入 -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.7.12</version>
</dependency>

<!-- 客户端 -->
<dependency>
    <groupId>org.apache.cxf</groupId>
    <artifactId>cxf-rt-rs-client</artifactId>
    <version>3.0.1</version>
</dependency>

<!-- 扩展json提供者 -->
<dependency>
    <groupId>org.apache.cxf</groupId>
    <artifactId>cxf-rt-rs-extension-providers</artifactId>
    <version>3.0.1</version>
</dependency>

<!-- 转换json工具包，被extension providers 依赖 -->
<dependency>
    <groupId>org.codehaus.jettison</groupId>
    <artifactId>jettison</artifactId>
    <version>1.3.7</version>
</dependency>

<!-- spring 核心 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>4.1.7.RELEASE</version>
</dependency>

<!-- spring web集成 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>4.1.7.RELEASE</version>
</dependency>

<!-- spring 整合junit -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>4.1.7.RELEASE</version>
</dependency>

<!-- junit 开发包 -->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
```

2、接口和实现类
``` 
//@Path("/userService")
@Produces("*/*")
public interface IUserService {

	@POST
	@Path("/saveUser")
	@Consumes({ "application/xml", "application/json" })
	public void saveUser(User user);

	@PUT
	@Path("/updateUser")
	@Consumes({ "application/xml", "application/json" })
	public void updateUser(User user);

	@GET
	@Path("/user")
	@Produces({ "application/xml", "application/json" })
	public List<User> findAllUsers();

	@GET
	@Path("/findUser/{id}")
	@Consumes("application/xml")
	@Produces({ "application/xml", "application/json" })
	public User finUserById(@PathParam("id") Integer id);

	@DELETE
	@Path("/deleteUser/{id}")
	@Consumes("application/xml")
	public void deleteUser(@PathParam("id") Integer id);
}

```

**接口中使用到注解说明**：
- @Path：连接路径依次为：${发布的路径}/${web.xml中的配置}/${spring配置文件中的配置}/${接口上面的@Path中的值}/${方法上面的@Path中的值}
- @POST/@GET/@DELETE/@PUT/ :restful风格中将请求方式的不同，对应着增（@POST）删（@DELETE）改（@PUT）查（@GET）操作，通常@POST和@GET使用的较多，因为只是一种风格而不是规范。
- @Consumes/@Produces ： 前者指定客户端请求数据的解析方式，后者指定响应到客户端的数据解析方式，通常会与复杂类型解析，简单类型无需指定
- @PathParam/@QueryParam ： 前者接受的参数直接使用 /user/{参数} 显示在URL上，后者是以 /user?${@QueryParam注解中的值}=传入参数 键值对方式显示在URL上

实现类略，直接实现接口，写业务逻辑即可

注意：**传输过程中，参数如果使用到了实体，需要在对应的实体类上加上@XmlRootElement(name = "xxx") 注解**


3、配置文件

```   
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:jaxrs="http://cxf.apache.org/jaxrs"
	xsi:schemaLocation="
	http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
	http://cxf.apache.org/jaxrs http://cxf.apache.org/schemas/jaxrs.xsd">

	<!-- 
	    需要引入jaxrs名称空间
		address 发布服务地址 
		servicesBeans 服务实现类 
	 -->
	<jaxrs:server id="userService" address="/userService" >
		<jaxrs:serviceBeans>
			<bean class="${实现类的全限定名称}" />
		</jaxrs:serviceBeans>
		<jaxrs:inInterceptors>
			<bean class="org.apache.cxf.interceptor.LoggingInInterceptor" />
		</jaxrs:inInterceptors>
		<jaxrs:outInterceptors>
			<bean class="org.apache.cxf.interceptor.LoggingOutInterceptor" />
		</jaxrs:outInterceptors>
	</jaxrs:server>
</beans>
```

4、客户端测试
客户端直接使用WebCLient/HttpClient工具调用服务即可，此处使用的Webclient：
```
// @GET请求
@Test
public void fun() {
    // 如果返回的是集合，调用getCollection()
    User user = WebClient.create("${发布的地址}").accept(MediaType.APPLICATION_JSON).get(User.class);
    System.out.println(user);
}

// @POST请求
@Test
public void fun2() {
   WebClient.create("${发布的地址}").type(MediaType.APPLICATION_JSON).post(user);
}
```

5、测试与总结
启动服务器，运行客户端测试方法，即可。

在不同系统间数据传输过程中，会有很多问题，通常需要双方联调，此处安利一个软件soap ui 来检查、调用、实现Web Service的功能/负载/符合性测试
输入服务端发布的地址和所需要的参数，如果服务端没有问题的情况下，就可以测通

