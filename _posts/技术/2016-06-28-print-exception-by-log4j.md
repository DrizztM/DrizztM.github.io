---
layout: post
title: 在log4j文件中打印异常信息
category: 技术
keywords: java
---

## 编写工具类

```java
import java.io.PrintWriter;
import java.io.StringWriter;

public class ExceptionConstans {
	public static String getTrace(Throwable t) {  
		StringWriter stringWriter = new StringWriter();
		PrintWriter writer = new PrintWriter(stringWriter);
		t.printStackTrace(writer);
		StringBuffer buffer = stringWriter.getBuffer();
		return buffer.toString();
   }
}
```

## 调用方法

```java
try {
	Thread.sleep(Const.DISPATCHER_SLEEPTIME);
} catch (InterruptedException e) {
	logger.error(ExceptionConstans.getTrace(e));
}
```

