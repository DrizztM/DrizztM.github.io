---
layout: post
title: mybatis批量插入
category: 技术
keywords: 优化
---

## mapper类

```java
package cn.ciaapp.write.mapper;

import java.util.List;

import org.springframework.stereotype.Component;

import cn.ciaapp.model.CiaAuth;

@Component("ciaAuthExWriteMapper")
public interface CiaAuthExMapper {
    
    void insertBatch(List<CiaAuth> auths);

}
```

## mapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="cn.ciaapp.write.mapper.CiaAuthExMapper">
	<insert id="insertBatch" parameterType="java.util.List">
		insert into cia_auth
		values
		<foreach collection="list" item="item" separator=",">
			(#{item.transId,jdbcType=VARCHAR},
			#{item.imsi,jdbcType=VARCHAR},
			#{item.appId,jdbcType=VARCHAR},
			#{item.authKey,jdbcType=VARCHAR},
			#{item.pn,jdbcType=VARCHAR},
			#{item.authCode,jdbcType=VARCHAR},
			#{item.duration,jdbcType=BIGINT},
			#{item.status,jdbcType=VARCHAR},
			#{item.reason,jdbcType=VARCHAR},
			#{item.ip,jdbcType=VARCHAR},
			#{item.date,jdbcType=TIMESTAMP},
			#{item.startTime,jdbcType=BIGINT},
			#{item.uuid,jdbcType=VARCHAR},
			#{item.system,jdbcType=VARCHAR},
			#{item.origin,jdbcType=VARCHAR}
			)
		</foreach>
	</insert>
</mapper>
```

