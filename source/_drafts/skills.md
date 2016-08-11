Java

基本的框架 
- Spring
- Spring MVC
- MyBatis
- Hibernate
- Spring Data
- Spring Boot
- Spring Session
- FreeMarker
- log4j, log4j2, slf4j commons-logging
- poi

服务交互组件
- RMI
- Hessian
- Webservie
- Restful Api
- Http Api
- Socket
- Thrift (需要学习一下)

EBS 
- dubbo

DB or NoSQL
- MySQL
- Oracle
- MongoDB
- Redis
- Memcached

缓存
- Redis
- Memcached

搜索
- solr
- Elasticsearch
- Lucene

消息队列
- JMS
- ActiveMQ
- ZeroMQ
- RabbitMQ
- Redis 也可以用作消息队列
- kafka

http://www.iigrowing.cn/solr-zong-jie.html
https://www.ibm.com/developerworks/cn/java/j-lo-Spring-Elasticsearch-Logstash/
http://www.cnblogs.com/smartloli/p/4654973.html

服务器
- Tomcat 
- Nginx

分布式
- Zookeeper

构建与部署
- mvn
- Nexus: maven 本地仓库
- Jenkins

版本控制
- svn
- git

其他 
- Swagger 自动生成 rest api 文档的工具， 管理服务接口

微服务之间通信
- 点对点 弊端很多，无法监控，通用逻辑重复等
- API网关，访问所有服务都通过网关，服务之间也通过网关


只读事务

只读事务期间只会读取事务开始时 时间节点的数据， 事务开始后的数据修改不会读取。

一个场景，就是生成报表的时候，需要多次读取数据库的数据，但是第二次读取和第一次读取可能存在脏数据，这时候就需要只读事务来保证只读取一个时间节点的数据。

-http://san-yun.iteye.com/blog/494405
-http://www.cnblogs.com/zhoujinyi/p/3437475.html

聚集索引

聚集索引是一种和记录的存储排序保持一致的索引，比如新华词典的拼音索引，所以一个表里聚集索引只能有一个。
