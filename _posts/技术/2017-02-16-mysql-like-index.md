---
layout: post
title: mysql 关于 like %xx% 的优化方案
category: 技术
keywords: db
---

```
SELECT * from t where id LIKE '%0%'
这种不走索引
```

```
SELECT * from t where locate('%0%',id)>0
SELECT * from t where instr(id,'%0%')>0
效率比上面的高一点，遗憾的是也不走索引
```

<font color="red">注意：这种只能用于查询特定字符在开头和结尾的情况，特定字符在中间的数据查不到。</font>
```
SELECT * from t where id LIKE '0%' or REVERSE(id) LIKE REVERSE('%0')
这种走索引，效率最高（REVERSE:将字符串反过来查）
```