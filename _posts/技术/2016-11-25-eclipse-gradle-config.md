---
layout: post
title: eclipse集成gradle
category: 技术
keywords: IDE
---

建议全程翻墙

## 下载gradle
需翻墙：<br>
http://services.gradle.org/distributions

## 设置环境变量
```
GRADLE_HOME
path添加：%GRADLE_HOME%\bin
修改下载的jar包保存位置：GRADLE_USER_HOME=%GRADLE_HOME%\.gradle
查看是否成功：cmd—>gradle -v
```

## eclipse下载插件
<img src="/assets/img/0069.jpg">


## 自己生成wrapper文件夹
进入gradle目录，执行gradle wrapper,会生成另一个gradle文件夹，下面有wrapper子文件夹，将子文件夹拷贝到.gradle目录下，并将gradle目录下的lib文件夹和bin文件夹拷贝到wrapper文件夹

## 配置eclipse的gradle插件
<img src="/assets/img/0070.jpg"><br>
<img src="/assets/img/0071.jpg">

## gradle配置文件样例
```
import org.gradle.plugins.ide.eclipse.model.Facet

apply plugin: 'java' 
apply plugin: 'war' 
apply plugin: 'eclipse-wtp' 

sourceCompatibility = 1.8   // 设置 JDK 版本 
webAppDirName = 'web'    // 设置 WebApp 根目录 
sourceSets.main.java.srcDir 'src'   // 设置 Java 源码所在目录 
sourceSets.main.java.srcDir 'test'   // 设置 Java 源码所在目录 
sourceSets.main.output.classesDir 'web/WEB-INF/classes'  //设置class生成路径

repositories {
	maven { url 'http://maven.aliyun.com/nexus/content/groups/public' }
}

ext {
	springVersion = '4.3.4.RELEASE'
	mybatisVersion = '3.4.1'
	mybatisSpringVersion = '1.3.0'
	c3p0Version = '0.9.1.2'
	mysqlConnectorVersion = '5.1.40'
	freemarkerVersion = '2.3.23'
	jacksonVersion = '2.8.5'
}

dependencies {
	compile (
	
	"org.springframework:spring-context-support:${springVersion}",
	"org.springframework:spring-jdbc:${springVersion}",
	"org.springframework:spring-orm:${springVersion}",
	"org.springframework:spring-webmvc:${springVersion}",
	
	"org.mybatis:mybatis:${mybatisVersion}",
	"org.mybatis:mybatis-spring:${mybatisSpringVersion}",
	
	"c3p0:c3p0:${c3p0Version}",
	"mysql:mysql-connector-java:${mysqlConnectorVersion}",
	
	"org.freemarker:freemarker:${freemarkerVersion}",
	
	"com.fasterxml.jackson.core:jackson-databind:${jacksonVersion}",
    "com.fasterxml.jackson.core:jackson-annotations:${jacksonVersion}",
	
	)
	providedCompile('javax.servlet:javax.servlet-api:3.1.0')
}

eclipse {
	wtp {
		facet {
			facet name: 'jst.web', type: Facet.FacetType.fixed
			facet name: 'wst.jsdt.web', type: Facet.FacetType.fixed
			facet name: 'jst.java', type: Facet.FacetType.fixed
			facet name: 'jst.java', version: '1.8'
			facet name: 'jst.web', version: '3.1'
			facet name: 'wst.jsdt.web', version: '1.0'
		}
	}
}
```