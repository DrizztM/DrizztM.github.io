---
layout: post
title: Spring/SpringMVC在启动完成后执行方法 
category: 技术
keywords: 优化
---

在某些情况下，有可能你会有这种需求：在Spring/SpringMVC项目中，当Spring/SpringMVC启动完成后，你需要执行一个方法来完成某些事件（比如创建网站地图，比如从订阅Redis服务器等），这个时候，可以使用Tomcat/Servlet容器提供的事件回调机制来完成，但是这样有个问题是：无法使用Spring提供的Annotation，解决方法是：

```java
@Component
public class Init implements ApplicationListener<ContextRefreshedEvent> {

    private static Logger logger = Logger.getLogger(Init.class);

    public void onApplicationEvent(ContextRefreshedEvent arg0) {
        logger.error("初始化项目");
    }

}
```
