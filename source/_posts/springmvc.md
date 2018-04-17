---
title: springmvc
date: 2018-03-22 00:00:14
tags: [spring,springMVC]  
categories: 
- 框架 
---
最近做毕业设计用到springMVC，在做项目的过程中顺便总结一下springMVC的知识点  
 <!-- more -->  
 > 注意:spring3.x与jdk 8不兼容会报错（以前踩过的坑） 
本次做毕设用到的版本为：spring4.x,jdk 8,mybatis 3.x 
#### 1.springMVC配置  
由于毕设使用的是SSM框架，索性在这里把spring和mybatis整合的配置文件也一块总结了 ，这些配置文件都可以在以后的项目中复用。这一节只是最基础的配置，以后再开发过程中如若要修改或添加配置，会在后续说明 
##### spring配置文件头  

```
	<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
		http://www.springframework.org/schema/beans/spring-beans-3.2.xsd 
		http://www.springframework.org/schema/mvc 
		http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd 
		http://www.springframework.org/schema/context 
		http://www.springframework.org/schema/context/spring-context-3.2.xsd 
		http://www.springframework.org/schema/aop 
		http://www.springframework.org/schema/aop/spring-aop-3.2.xsd 
		http://www.springframework.org/schema/tx 
		http://www.springframework.org/schema/tx/spring-tx-3.2.xsd ">
  ``` 
  
##### springMVC配置  

 在springmvc.xml中可以配置的模块：1. 最基本的视图解析器和Handler扫描等   2. json解析  3. 参数绑定类型转换器……  

```
<!-- 配置Handler  自动扫描包，多个包中间用逗号隔开-->
	 <context:component-scan base-package="com.adoptPet.userInterface.controller"></context:component-scan>
	 
	 <mvc:annotation-driven/>
	 <!-- conversion-service="conversionService" -->
	 <!-- 视图解析器 -->
	 <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	 	<!-- 配置jsp路径的前缀 -->
		<property name="prefix" value="/WEB-INF/jsp/"/>
		<!-- 配置jsp路径的后缀 -->
		<property name="suffix" value=".jsp"/>
	 </bean>
```     

##### applicationContext配置  
  
我习惯把applicationContext配置分为三部分，applicationContext-dao,applicationContext-service,applicationContext-transaction   

###### applicationContext-dao.xml(已整合mybatis)   

```
<!-- 加载db.properties文件中的内容，db.properties文件中key命名要有一定的特殊规则 -->
	<context:property-placeholder location="classpath:db.properties" />
	<!-- 配置数据源 -->
	<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
		destroy-method="close">
		<property name="driverClassName" value="${jdbc.driver}" />
		<property name="url" value="${jdbc.url}" />
		<property name="username" value="${jdbc.username}" />
		<property name="password" value="${jdbc.password}" />
		<property name="maxActive" value="30" />
		<property name="maxIdle" value="5" />
	</bean>
	<!-- 配置sqlsessionFactory -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<!-- 数据库连接池 -->
		<property name="dataSource" ref="dataSource" />
		<!-- 加载mybatis的全局配置文件 -->
		<property name="configLocation" value="classpath:mybatis/sqlMapConfig.xml" />
	</bean>
	<!-- 配置mapper扫描 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<!-- 扫描包路径，如果需要扫描多个包，中间使用半角逗号隔开 -->
		<property name="basePackage" value="com.adoptPet.userInterface.mapper"></property>
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
	</bean>
```  

######  applicationContext-service.xml(业务层)    

```
<bean id="UserService" class="com.adoptPet.userInterface.service.impl.UserServiceImpl"/>
```  

###### applicationContext-transaction.xml(事务控制)    

```
<!-- 事务管理器 
			对mybatis操作数据库事务控制，spring使用jdbc的事务控制类
		-->
		<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
			<!-- 数据源
			dataSource在applicationContext-dao.xml中配置了
			 -->
			<property name="dataSource" ref="dataSource"/>
		</bean>
		
		<!-- 通知 -->
		<tx:advice id="txAdvice" transaction-manager="transactionManager">
			<tx:attributes>
				<!-- 传播行为 -->
				<tx:method name="save*" propagation="REQUIRED"/>
				<tx:method name="delete*" propagation="REQUIRED"/>
				<tx:method name="insert*" propagation="REQUIRED"/>
				<tx:method name="update*" propagation="REQUIRED"/>
				<tx:method name="find*" propagation="SUPPORTS" read-only="true"/>
				<tx:method name="get*" propagation="SUPPORTS" read-only="true"/>
				<tx:method name="select*" propagation="SUPPORTS" read-only="true"/>
			</tx:attributes>
		</tx:advice>
		<!-- aop -->
		<aop:config>
			<aop:advisor advice-ref="txAdvice" pointcut="execution(* com.adoptPet.userInterface.service.impl.*.*(..))"/>
		</aop:config>
```    

##### mybatis的sqlMapConfig.xml配置   

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
	<!-- 全局setting配置根据需要添加 -->
	<!-- 配置别名 -->
	<typeAliases>
		<!-- 批量扫描别名 -->
		<package name="com.adoptPet.userInterface.entity"/>
	</typeAliases>
	<!-- 配置mapper
	由于使用spring和mybatis的整合包进行mapper扫描，这里不需要配置了。
	但必须遵循：mapper.xml和mapper.java文件同名且在一个目录 
	 -->

	<!-- <mappers>
	
	</mappers> -->

</configuration>
```