#StarDB
##什么是StarDB

StarDB是一个以数据安全为先，面向7*24企业级系统，兼顾快速开发的开发方案；并基于此方案提供了一个易于开发的ORM框架。其后台不局限于任何数据库，既可使用关系型数据库（RDBMS：Postgres，MySQL等），也可以是NoSQL（MongoDB，Cassandra等）。

注：现阶段仅提供基于JAVA语言实现的类似JPA的框架，后台现仅支持RDBMS，本文基于RDBMS以说明。NoSQL将于下一版本给予支持。

StarDB的优势：

- 保存所有数据为第一原则，防止设计失误或物理损坏而出现的数据丢失
- 数据库仅允许插入（Insert）操作，无变更（Immutable）的数据，有利于最大化缓存的效果。
- 接近于Key-Value设计，分散的数据结构有利于业务变更，业务变更无需更改既存的数据结构。
- 分散的数据结构利于最大化并行，以及数据物理分布。
- 提供近似于JPA的ORM框架，做到开发方便快速

StarDB不适应于：

- 以节省硬件资源（磁盘，CPU，内存）为优先的算法型系统（比如将表结构分散，增加了系统的IO，将其部署到AWS等云平台会比较昂贵）
- 数据无需永久保存（允许“删改”操作）的系统
- 业务变更不频繁的系统。
- 另外，StarDB仅仅设计数据存储方面，不涉及任何用户界面（UI）方面的技术。

##StarDB特点
### （1）数据库仅允许插入（Insert）操作

传统数据库支持增删改查（CRUD）操作，但是其中的“删改（UD）”操作物理性的改变了数据库的状态。
这可能在某些情况下是无法挽回的损失。
比方说，如果以Update操作来保存用户最后一次登录时间，以后将无法满足调查用户登录历史的需求。

而现在数据对企业越来越重要，并且硬件（磁盘，CPU，内存）越来越廉价，
保存一切数据，并充分利用硬件成为StarDB的主要解决问题。
因此，StarDB仅仅允许使用“增查”（CR）操作,设计上彻底屏蔽了数据丢失问题。

类似于SVN或GIT对源代码的管理一样，数据库的每次更改，都可以追溯，还原某个时间的状态。

并且，为防止机器的物理损坏导致的数据丢失，支持N台数据库同步插入机制。

### （2）为7*24小时系统设计，松散数据，便于设计变更及应用程序部署。

传统的关系型数据库设计方案是基于表的设计，但是表设计在业务变更时，将出现对表结构做出变更（ALT TABLE）的需求。
这对正在运行的系统（正在进行读写的表）带来极大不便，可能为此需要停机部署，难以满足7*24小时系统设计需求。

并且遵循原则*（1）数据库仅允许插入（Insert）操作*，
如果允许表结构做出变更（ALT TABLE），还有可能违背了StarDB的初衷：保存所有数据，包括其数据结构。
将对表结构的变更（ALT TABLE）转化为表追加（CREATE TABLE）操作。


将所有的列尽量分散为以列为单位的独立小表，将有助于业务变更和历史记录。

传统订单表设计例子：

| OrderId | ItemName  | ItemValue | Quatiy   |
| :--- | :------- | ----: | :---: |
| 1    | Computer | $1600 |  5    |
| 2    | Phone    | $12   |  12   |
| 3    | Pipe     | $1    |  234  | 

如果订单变化状态，采用（配图1）：

<!--
<div style="width: 480px; height: 360px; margin: 10px; position: relative;"><iframe allowfullscreen frameborder="0" style="width:480px; height:360px" src="https://www.lucidchart.com/documents/embeddedchart/356559f7-eb9b-404d-b4fa-3f86ba23dd74" id="n-S-R0oTNwuP"></iframe></div>
-->


如果是过去的订单，我们可以根据某个时间点（如ORDER_COMPLETED属性表）去获取当时订单的状态。

如果是当前尚未完成的订单，需要根据最新的属性去获取订单信息。



### （3）不推荐事务（Transaction，TX）


不推荐核心点范围内事务回滚，不支持跨核心点的事务回滚（NoSQL概念）



##StarDB命名由来

松散数据设计。以UseCase为核心点扩展数据，ER图上类似闪耀光芒的太阳（中心之星）和围绕其转动的其他行星以及卫星。而宇宙由这无数恒星，星系相互影响和拓展而成
因此Star由此而来。






#高级话题

##N台数据库同步插入

##锁

##Partition

##故障恢复

##比较分散数据和DB提供的View
View依赖于各个数据库的实现，有的数据库以单线程实现View，效率不高。而且没有为数据库历史管理提供最优化数据管理方式。

##提高插入性能，

使用NOLOGGING - RDBMS 
http://www.orafaq.com/wiki/Nologging_and_force_logging
http://perfopy.ashisuto.co.jp/oracle_performance_educ/20nologging.html



##类似解决方案
https://en.wikipedia.org/wiki/Change_data_capture   CDC

https://blogs.saphana.com/2011/09/14/insert-only/

tracking changes database

https://en.wikipedia.org/wiki/Database_audit

database version control




