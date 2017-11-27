---
layout: post
title: springboot将MultipartFile转换为File的处理方法
category: 技术
keywords: server
---

采用流处理的方式，直接调用MultipartFile的getInputStream方法即可。
``` java
BufferedReader br = new BufferedReader(new InputStreamReader(file.getInputStream()));
		try {
			String read = null;
			while ((read = br.readLine()) != null) {
				result = result + read + "\r\n";
			}
		} catch (Exception e) {
			e.printStackTrace();
		} 
```