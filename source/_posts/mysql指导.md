---
title: mysql指导
date: 2018-03-07 10:14:16
tags:
---

###1、执行计划分析
system > const > eq_ref > ref system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL

一般来说，好的sql查询至少达到range级别，最好能达到ref

1、system：表只有一行记录（等于系统表），这是const类型的特例，平时不会出现，可以忽略不计

2、const：表示通过索引一次就找到了，const用于比较primary key 或者 unique索引。因为只需匹配一行数据，所有很快。如果将主键置于where列表中，mysql就能将该查询转换为一个const 

3、eq_ref：唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键 或 唯一索引扫描。

4、ref：非唯一性索引扫描，返回匹配某个单独值的所有行。本质是也是一种索引访问，它返回所有匹配某个单独值的行，然而他可能会找到多个符合条件的行，所以它应该属于查找和扫描的混合体  

5、range：只检索给定范围的行，使用一个索引来选择行。key列显示使用了那个索引。一般就是在where语句中出现了bettween、<、>、in等的查询。这种索引列上的范围扫描比全索引扫描要好。只需要开始于某个点，结束于另一个点，不用扫描全部索引 

6、index：Full Index Scan，index与ALL区别为index类型只遍历索引树。这通常为ALL块，应为索引文件通常比数据文件小。（Index与ALL虽然都是读全表，但index是从索引中读取，而ALL是从硬盘读取） 

7、ALL：Full Table Scan，遍历全表以找到匹配的行

EXPLAIN  SELECT * FROM web_user;


### 2、mysql系统配置

1、运行  show full PROCESSLIST; 列出当前所有的连接
2、explain extended select语句  show warnings 查看mysql优化器优化之后的sql
3、show global status  查看mysql状态信息   



### 服务配置

基础设置
你应该经常会查看或调整的 3 个 MySQL 性能配置项。如果没有，你可能很快就会遇到问题。

innodb_buffer_pool_size：这是任何使用 InnoDB 存储引擎的 MySQL 在安装时第一个应该要查看的配置。Buffer pool 是用来缓存数据和索引的：尽可能地设置大一点，以确保在进行大多数读操作时是读内存而不是读磁盘。一般设置值为 5-6GB(8GB RAM)，20-25G(32GB RAM)，100-120GB(128GB RAM)。

innodb_log_file_size：这个选项是设置 redo 日志（重做日志）的大小。redo 日志 是用来确保写入的数据能够快速地写入，并且持久化，还可以用于崩溃恢复(crash recovery)。MySQL 5.1 之前，这个选项很难去进行调整，因为你既想要加大 redo 日志来提高性能，又想要减小 redo 日志来进行快速的崩溃恢复。幸运的是，自 MySQL 5.5 之后，崩溃恢复的性能有了很大的提高，现在你可以拥有快速写入性能的同时，还能满足快速崩溃恢复。一直到 MySQL 5.5，redo 日志的总大小被限制在 4GB (默认有 2 个日志文件)。这个在 MySQL 5.6 中被增加了。

启动的时候设置 innodb_log_file_size = 512M（也就是 1GB 大小的 redo 日志），这样可以提供充足的写空间。如果你知道你的应用是频繁写入的，并且你使用的 MySQL 版本是 MySQL 5.6，你可以设置 innodb_log_file_size = 4G。

max_connections：如果你经常遇到 "Too many connections" 的错误，是因为 max_connections 太小了。这个错误很长见到，因为应用程序没有正确地关闭与数据库的连接，你需要设置连接数为比默认 151 更大的值。max_connections 设置过高（如 1000 或更高）的一个主要缺点是当服务器运行 1000 个或者更多的事务时，会响应缓慢甚至没有响应。在应用程序端使用连接池或者在 MySQL 端使用线程池有助于解决这个问题。

InnoDB 设置
从 MySQL 5.5 开始，InnoDB 成为了默认的存储引擎，并且它的使用频率比其他存储引擎的要多得多。这就是要小心配置它的原因。

innodb_file_per_table：这个配置项会决定 InnoDB 是使用共享表空间(innodb_file_per_table = OFF) 来存储数据和索引，还是为每个表使用一个单独的 .ibd 文件(innodb_file_per_table= ON)。对每个表使用一个文件的方式，在 drop, truncate, 或者重建表的时候，会回收这个表空间。在一些高级特性，如压缩的时候也需要开启使用独立表空间。然而这个选项却不能带来性能的提升。你不想使用独立表空间的一个主要场景是：有很多的表（例如：10000 以上张表）。

在 MySQL 5.6 中，这个配置项是默认开启的，因此多数情况下，你无需操作。对于早期的 MySQL 版本，需要在加载数据之前把它设置成 ON ，因为它只对新创建的表有影响。

innodb_flush_log_at_trx_commit：默认值为 1，表示 InnoDB 完全支持 ACID 特性。例如在在一个主节点上，你主要关注数据安全性，这是最好的设置值。然而它会对速度缓慢的磁盘系统造成很大的开销，因为每次将改变刷新到 redo 日志的时候，都需要额外的 fsync 操作。设置为 2，可靠性会差一点，因为已提交的事务只会 1 秒钟刷新一次到 redo 日志，但在某些情况下，对一个主节点而言，这仍然是可以接受的，而且对于复制关系的从库来说，这是一个很好的值。设置为 0，速度更快，但是在遇到崩溃的时候很可能会丢失一些数据，这只对从库是一个好的设置值。

innodb_flush_method：这个设置项决定了数据和日志刷新到磁盘的方式。当服务器硬件有 RAID 控制器、断电保护、采取 write-back 缓存机制的时候，最常用的值是 O_DIRECT；其他大多数场景使用默认值 fdatasync。sysbench 是一个帮助你在这两个值之间做出选择好工具。

innodb_log_buffer_size：这个设置项用来设置缓存还没有提交的事务的缓冲区的大小。默认值(1MB) 一般是够用的，但一旦事务之中带有大 blob/text 字段，这个缓冲区会被很快填满，并引起额外的 I/O 负载。看看 innodb_log_waits 这个状态变量的值，如果不是 0 的话，需要增加 innodb_log_buffer_size。