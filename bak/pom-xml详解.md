---
title: pom.xml详解
date: 2018-03-20 15:50:08
tags: [Maven,Spring,SpringMVC,Mybatis]  
categories: 
- 环境搭建
- 配置文件
---

# pom.xml详解  
&emsp;&emsp;pom.xml主要描述了项目的maven坐标，依赖关系，开发者需要遵循的规则，缺陷管理系统，组织和licenses，以及其他所有的项目相关因素，是项目级别的配置文件。  
&emsp;&emsp;下边是一个Maven项目的pom.xml配置文件，以此为例进行解析。   
<!-- more --> 

```
<?xml version="1.0"?>
<project
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"
    xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <modelVersion>4.0.0</modelVersion>
    <!--必填项GAV-->
    <!--企业名称-->
    <groupId>com.tortuousroad</groupId>
    <!--项目名称，项目的唯一标识-->
    <artifactId>groupon</artifactId>
    <!--版本号-->
    <version>1.0</version>
    <!--打包形式：pom,jar,war默认为jar，
    若要使用Maven进行模块管理，则在每个模块中都有pom.xml文件
    父级的pom.xml packaging应配置为pom,子级模块中的packaging配置为
    jar或war若要进行部署则用war，其他情况推荐用jar-->
    <packaging>pom</packaging>
    <!---->
    <name>${project.artifactId}</name>
    
    <!--版权许可，这里通常描述采用的开源协议是什么。-->
    <licenses>
        <license>
            <name>Apache 2</name>
            <url>http://www.apache.org/licenses/LICENSE-2.0.txt</url>
            <distribution>repo</distribution>
            <comments>A business-friendly OSS license</comments>
        </license>
    </licenses>

    <!--组织机构信息-->
    <organization>
        <name></name>
        <url></url>
    </organization>

    <!--模块划分，在每个模块中都有一个pom.xml-->
    <modules>
        <module>Site</module>
        <module>Service</module>
        <module>Admin</module>
        <module>ImageServer</module>
        <module>Alipay</module>
    </modules>

    <!--统一管理版本信息以及字符集等属性
    在后边使用时：用${spring_version}引用即可-->
    <properties>
        <spring_version>4.3.2.RELEASE</spring_version>

        <!--......-->
    </properties>

	<!--<dependencyManagement>主要用于存在父子继承的Maven项目中。
	在父项目中通过<dependencyManagement>设置被依赖的Maven构件，
	在子项目中设置被依赖的Maven构件时，只要给出构件的groupId和artifactId，
	而version则默认引用父项目的设置。例如下边的org.slf4j-->
    <dependencyManagement>
        <dependencies>

            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-aop</artifactId>
                <version>${spring_version}</version>
            </dependency>
           ......
           <dependency>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-api</artifactId>
                <version>1.7.12</version>
            </dependency>
			.......
        </dependencies>
    </dependencyManagement>

	<!-- 通过<dependencies>给出Maven项目所依赖的第三方类库 -->
    <dependencies>
	
        <!-- 只需给出GA，V在dependencyManagement已声明 -->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
        </dependency>
        ......
        <!-- <dependency> -->
            <!-- <groupId>junit</groupId> -->
            <!-- <artifactId>junit</artifactId> -->
            <!-- <version>4.12</version> -->
        <!-- </dependency> -->
       ......
	   <dependency>  
			<groupId>junit</groupId>  
			<artifactId>junit</artifactId>  
			<version>4.0</version> 
			<!-- 打包类型，默认jar -->
			<type>jar</type>
			
			<!-- 被依赖的Maven构件在classpath中的可访问范围  
			compile，默认值，被依赖的Maven构件在compile、runtime和test的时候都可以在classpath中找到
			provided，被依赖的Maven构件在compile和test的时候都可以在classpath中找到，
			在runtime的时候由JDK或容器提供
			system，被依赖的Maven构件在compile和test的时候都可以在classpath中找到，在runtime的时候必须显式
			将JAR加入到classpath中，此时可以设置<systemPath></systemPath>该值必须是一个绝对路径，
			可以通过环境变量给出具体的绝对路径
			runtime，被依赖的Maven构件在runtime和test的时候都可以在classpath中找到，在compile时不是必须的
			test，被依赖的Maven构件在test的时候可以在classpath中找到，
			在compile和runtime时不是必须的-->
			<scope>test</scope>  
			<!-- 当前Maven项目的构件被其他项目依赖，此处被依赖的Maven构件相对于其他项目来说是不必须的 -->
			<optional>true</optional>  
		</dependency> 
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <configuration>
                    <archive>
                        <addMavenDescriptor>true</addMavenDescriptor>
                        <index>true</index>
                        <manifest>
                            <addDefaultSpecificationEntries>true</addDefaultSpecificationEntries>
                            <addDefaultImplementationEntries>true</addDefaultImplementationEntries>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-deploy-plugin</artifactId>
                <configuration>
                    <skip>false</skip>
                </configuration>
            </plugin>
            <plugin>
                <artifactId>maven-source-plugin</artifactId>
                <version>2.1</version>
                <configuration>
                    <attach>true</attach>
                </configuration>
                <executions>
                    <execution>
                        <phase>compile</phase>
                        <goals>
                            <goal>jar</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.18.1</version>
                <configuration>
                    <skipTests>true</skipTests>
                </configuration>
            </plugin>
        </plugins>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>

        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-jar-plugin</artifactId>
                    <version>${maven_jar_plugin_version}</version>
                </plugin>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-war-plugin</artifactId>
                    <version>${maven_war_plugin_version}</version>
                </plugin>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-install-plugin</artifactId>
                    <version>${maven_install_plugin_version}</version>
                </plugin>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-deploy-plugin</artifactId>
                    <version>${maven_deploy_plugin_version}</version>
                </plugin>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>${maven_compiler_plugin_version}</version>
                    <configuration>
                        <source>${java_source_version}</source>
                        <target>${java_target_version}</target>
                        <encoding>${file_encoding}</encoding>
                    </configuration>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>

    <issueManagement>
        <system>GitHub</system>
        <url></url>
    </issueManagement>

    <developers>
        <developer>
            <name>Road</name>
            <id></id>
            <email></email>
            <roles>
                <role>Manager</role>
            </roles>
            <timezone>+8</timezone>
        </developer>
    </developers>

</project>
```



