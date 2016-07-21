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

但是这个时候，会存在一个问题，在web 项目中（spring mvc），系统会存在两个容器，一个是root application context ,另一个就是我们自己的 projectName-servlet  context（作为root application context的子容器）。<br>
这种情况下，就会造成onApplicationEvent方法被执行两次。为了避免上面提到的问题，我们可以只在root application context初始化完成后调用逻辑代码，其他的容器的初始化完成，则不做任何处理，修改后代码如下：

```java
@Component
public class Init implements ApplicationListener<ContextRefreshedEvent> {

    private static Logger logger = Logger.getLogger(Init.class);

    public void onApplicationEvent(ContextRefreshedEvent arg0) {
	    if(event.getApplicationContext().getParent() == null){
	        logger.error("初始化项目");
        }
    }
}
```