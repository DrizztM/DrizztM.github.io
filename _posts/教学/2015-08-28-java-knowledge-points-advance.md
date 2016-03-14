---
layout: post
title: java知识点——进阶篇
category: 教学
keywords: java
---

## JVM的相关概念

- 生命周期：加载、连接、初始化，使用，卸载。<br>
<img src="/assets/img/0001.png">

- 对象基本上都是在jvm的堆区中创建，在创建对象之前，会触发类加载（加载、连接、初始化），其中链接又可分为校验（Verify），备（Prepare），解析（Resolve）三步。
- 当类初始化完成后，利用ClassLoader来装载，通过指定的className，找到二进制码，生成Class实例，放到JVM中。
- 当对象使用完之后会在合适的时候被jvm垃圾收集器回收。

## 线程安全

- Spring
 - Spring默认的注入是单例的，所以Spring存在线程安全问题。
 - 解决方案就是在spring配置文件创建注入Bean的时候，将原本的单例改成原型。
 - 旧版本Spring配置方式： Singleton=”true”【单例】→Singleton=”false”【原型】。
 - 新版本Spring配置方式：Scope=”singleton”【单例】→Scope=”prototype”【原型】。
 
- Struts
 - Struts1是单例模式，所以Struts1是线程不安全的。
 - Struts2是多例【即原型】，所以Struts2是线程安全的。
 - Struts2是多例，线程安全的，但如果我们将Struts2交给Spring管理的时候，Struts2就会默认使用Spring的线程即单例。这样就会产生线程安全为例，解决方案：在Spring配置Struts2的Bean时，将单例改成原型。

- Stringbuffer、stringbuilder
 - StringBuffer是线程安全的，它是单线程的，但因为StringBuffer类内部使用了synchronized，所以StringBuffer是线程安全的。
 - StringBuilder是线程不安全的。【它在多线程环境下是不安全的，虽然它和StringBuffer很多相似地方，但它没有synchronized】,我们应该尽量避免在多线程环境下使用StringBuilder，但不得不说它的效率的确是最快的。

- HashMap、HashTable
 - Map是一个以键值对存储的接口。Map下有两个具体的实现，分别是HashMap和HashTable。
 - HashMap是线程非安全的，HashTable是线程安全的，所以HashMap的效率高于HashTable。
 - HashMap允许键或值为空，而HashTable不允许键或值为空。
 - HashMap性能优于Hashtable。

- ArrayList、Vector
 - List接口实现类有ArrayList,LinkedList,Vector。ArrayList和Vector是基于数组实现的,所以查询的时候速度快，而在进行增加和删除的时候速度较慢LinkedList是基于链式存储结构，所以在进行查询的时候速度较慢但在进行增加和删除的时候速度较快。又因为Vector是线程安全的，所以他和ArrayList相比而言，查询效率要低。
 - ArrayList是线程非安全的，因为没有同步方法。
 - LinkedList,也是线程不安全的，解决方法参照ArrayList。
 - 如何将ArrayList变成线程安全的：<br>
   1.使用synchronized关键字，这个大家应该都很熟悉了；<br>
   2.List list = Collections.synchronizedList(new ArrayList());

## 必会sql语句

- oracle分页<br>
select * from (select t.*, rownum rn from (select * from menu order by id desc) t where rownum < 10) where rn >=5

- mysql分页<br>
select * from music where id limit 5,5

- 去重<br>
select DISTINCT name from store

- 排序<br>
select * from store ORDER BY num

- 模糊查询（\_匹配一个  %匹配多个）<br>
select name from store where name like '亚\_'
select name from store where name like '淘%'

- 内联、左联、右连<br>
select * from store as s INNER JOIN country as c on s.country\_id = c.id<br>
select * from store as s LEFT JOIN country as c on s.country\_id = c.id<br>
select * from store as s RIGHT JOIN country as c on s.country\_id = c.id

- 分组查询<br>
SELECT Customer,SUM(OrderPrice) FROM Orders GROUP BY Customer HAVING SUM(OrderPrice)<2000

## 常用的sql优化

总体来说3个方面 ：缓存；分段批量；存储过程。

1.(重点)SELECT语句中避免使用 \*，尽量应该根据业务需求按字段进行查询。<br>
举例：如果表中有个字段用的是clob或者是blob这种大数据字段的话，他们的查询应该根据业务需要来进行指定字段的查询，切记勿直接用\*。<br>

2.(重点) 删除重复记录(oracle)<br>
DELETE  FROM  EMP E  WHERE  E.ROWID > (SELECT MIN(X.ROWID) FROM  EMP X  WHERE  X.EMP_NO = E.EMP_NO)
3.用>=替换><br>
如一个表有100万记录，一个数值型字段A，A=0时，有30万条；A=1时，有30万条；A=2时，有39万条;A=3时，有1万记录。那么执行 A>2 与 A>=3 的效果就有很大的区别了，因为 A>2 时，ORACLE会先找出为2的记录索引再进行比较，而A>=3时ORACLE则直接找到=3的记录索引。<br>

4.(重点)尽量多使用COMMIT<br>
如对大数据量的分段批量提交。<br>

5.(重点)用NOT EXISTS 或（外连接+判断为空）方案 替换 NOT IN操作符<br>
NOT IN 操作是强列推荐不使用的，因为它不能应用表的索引。<br>

6.(重点)LIKE操作符(大数据的全文检索使用luncene)(solr)<br>
因为使用like不当，会导致性能问题，原因是like在左右两边都有%的时候，不会使用索引。<br>
如LIKE '%5400%' 这种查询不会引用索引，而LIKE 'X5400%' 则会引用范围索引。<br>
 一个实际例子：<br>
查询营业编号 YY\_BH LIKE '%5400%' 这个条件会产生全表扫描，如果改成  YY\_BH LIKE 'X5400%' OR YY\_BH LIKE 'B5400%' ，则会利用  YY\_BH 的索引进行两个范围的查询，性能肯定大大提高。<br>

7.(重点)避免在索引列上使用计算和函数,这样索引就不能使用<br>
举例: <br>
低效：SELECT … FROM  DEPT  WHERE SAL * 12 > 25000; <br>
高效: SELECT … FROM DEPT WHERE SAL > 25000/12;<br>

8.(重点)用UNION ALL 替换UNION<br>
因为UNION ALL不会过滤重复数据而且不会自动排序，所执行效率要快于UNION。<br>

9.减少访问数据库的次数<br>
举例:如果批量删除多条数据，可以用  delete  from tableName where id in (1,2,3)，而不要用多条delete语句进行删除。<br>

10.（重点）用TRUNCATE替代DELETE<br>
TRUNCATE不记录日志，DELETE记录日志，所以TRUNCATE要快于DELETE;但是一旦用TRUNCATE进行删除就不能进行恢复,TRUNCATE是删除整张表的数据;TRUNCATE不能加where条件。<br>
ps:mysql,sqlserver中如果id为自增类型，那么如果用TRUNCATE删除，则id字段再插入数据时从1开始，如果delete删除的话，则从删除之的id的值继续增长。<br>

## 常见的代码优化

1.尽量重用对象，特别是String对象的使用，出现需要字符串连接的情况时，要用StringBuffer或是StringBuilding。<br>

2.尽量使用局部变量，速度会比较快。<br>

3.当项目使用的是oracle数据库时，java中写的sql语句尽量大写，减轻oracle解析的负担。<br>

4.操作流的时候用完一定要关闭。<br>

5.减少对变量的重复计算。<br>

6.如果可以，尽量采用懒加载，在需要的时候再去创建。<br>

7.异常的使用要放在最外层。<br>

8.StringBuffer有三种构造方法，StringBuffer(),StringBuffer(int size)，StringBuffer(String str),我们在new的时候可以用StringBuffer(int size)，在初始化时就设置它的容量，可以很好的提升性能。<br>

9.在jsp页面关闭无用的会话。<br>

10.在引用页面时。Include指令，比include动作更高效。<br>
Include指令在编译时引入指定的资源，在编译之前将两个页面的资源并为一个。只生成一个.Class文件。被引用的资源在编译的时候就确定了，比在运行时才确定更高效。<br>
Include动作，该动作引入指定页面执行后生成的结果，由于他在运行时完成，生成多个.class文件。因此对输出结果的控制更加灵活，但是，只有当被引用的内容频繁改变时，用include动作才划算。<br>

11.及时清除不再需要的会话。<br>

12.hashMap和ArrayList比hashTable和Vector的效率高。但是在知道数组大小时，尽量使用array[]。
