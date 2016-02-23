---
layout: post
title: Eclipse下用 Gradle构建 Web Application
category: 技术
keywords: IDE
---

1. 安装 Gradle插件<br>
在 Eclipse Marketplace中搜索 Gradle, 选择安装 Gradle Integration for Eclipse

2. 新建一个 Dynamic Web Project<br>
在项目根目录下新建 build.gradle 文件, 并向其中写入如下内容: <br>
```
import org.gradle.plugins.ide.eclipse.model.Facet

apply plugin: 'java'
apply plugin: 'eclipse-wtp'
apply plugin: 'war'

webAppDirName = 'web'
sourceSets.main.java.srcDir 'src'
[compileJava]*.options*.encoding = 'UTF-8'
version = '1.0'
sourceCompatibility = 1.7

repositories {
	mavenLocal()
	mavenCentral()
}

ext {
	springVersion = '4.2.3.RELEASE'
	hibernateVersion = '5.0.5.Final'
	hibernateSearchVersion = '5.5.1.Final'
	freemarkerVersion = '2.3.23'
	httpclientVersion = '4.5'
	jacksonVersion = '2.6.1'
}

dependencies {
	compile (
	"org.freemarker:freemarker:${freemarkerVersion}",

}

eclipse {
	wtp {
		facet {
			facet name: 'jst.web', type: Facet.FacetType.fixed
			facet name: 'wst.jsdt.web', type: Facet.FacetType.fixed
			facet name: 'jst.java', type: Facet.FacetType.fixed
			facet name: 'jst.java', version: '1.7'
			facet name: 'jst.web', version: '3.1'
			facet name: 'wst.jsdt.web', version: '1.0'
		}
	}
}
```

3. 接下来在项目上右击 -> configure -> convert to Gradle project<br>

4. 再次在项目上右击 -> Gradle -> Refresh All<br>

5. 如果没有 web.xml 文件的话, 再右击项目 -> Java EE Tools -> Generate Deployment Descriptor Stub<br>

6. 最后看下效果如何, 在 web下新建 index.html,里面随便写点什么, 然后项目上右击 -> Run As -> Run on Server<br>
