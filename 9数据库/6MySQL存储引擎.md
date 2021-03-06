#  MySQL--数据库存储引擎（主要分析对比InnoDB和MyISAM以及讲述Mrg_Myisam分表）



一、存储引擎的概述：

于此博客[MySQL数据库引擎](http://blog.csdn.net/wangyang1354/article/details/50740041)去扩展，不过每个我都会用例子去验证，并写出的见解。

*（1）为什么要合理选择数据库存储引擎：**

ySQL中的数据用各种不同的技术存储在文件（或者内存）中。这些技术中的每一种技术都使用不同的存储机制、索引技巧、锁定水平并且最终提供广泛的不同的功能和能力。通过选择不同的技术，你能够获得额外的速度或者功能，从而改善你的应用的整体功能。

不同的技术以及配套的相关功能在MySQL中被称作存储引擎(也称作表类型)。MySQL默认配置了许多不同的存储引擎，可以预先设置或者在MySQL服务器中启用。你可以选择适用于服务器、数据库和表格的存储引擎，以便在选择如何存储你的信息、如何检索这些信息以及你需要你的数据结合什么性能和功能的时候为你提供最大的灵活性。

（2）定义：**

#引擎是用于存储、处理和保护数据的核心服务。利用数据库引擎可控制访问权限并快速处理事务，从而满足企业内大多数需要处理大量数据的应用程序的要求。 使用数据库引擎创建用于联机事务处理或联机分析处理数据的关系数据库。这包括创建用于存储数据的表和用于查看、管理和保护数据安全的数据库对象（如索引、视图和存储过程）。

#3）存储引擎作用：**

#计并创建数据库以保存系统所需的关系或XML文档。

#现系统以访问和更改数据库中存储的数据。包括实现网站或使用数据的应用程序，还包括生成使用SQL Server工具和实用工具以使用数据的过程。

#单位或客户部署实现的系统。

#供日常管理支持以优化数据库的性能。

#4）如何修改数据库引擎：**

#：

#置文件my.ini

#sql.ini另存为my.ini，在[mysqld]后面添加default-storage-engine=InnoDB，重启服务，数据库默认的引擎修改为InnoDB

#：

#的时候指定

```
create table mytbl(       id int primary key,       name varchar(50)   )type=MyISAM;
```

#：

#更改

```
alter table table_name type = InnoDB;
```

#5）怎么查看修改成功？**

#：

```
show table status from table_name; 
```

#：

```
show create table table_name
```

#：

#据库管理工具啊。

------

#ySQL各大存储引擎：

#看下你下的MySQL支持什么数据库引擎

![这里写图片描述](https://img-blog.csdn.net/20170610222359576?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja19fRnJvc3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#擎主要有： 1. MyIsam , 2. InnoDB, 3. Memory, 4. Blackhole, 5. CSV, 6. Performance_Schema, 7. Archive, 8. Federated , 9 Mrg_Myisam

#们主要分析使用MyIsam 和InnoDB。其余略微带过，详情请分别百度。

#InnoDB：

#义：**（默认的存储引擎）

#oDB是一个事务型的存储引擎，有行级锁定和外键约束。

#odb引擎提供了对数据库ACID事务的支持，并且实现了SQL标准的四种隔离级别，关于数据库事务与其隔离级别的内容请见数据库事务与其隔离级别这类型的文章。该引擎还提供了行级锁和外键约束，它的设计目标是处理大容量数据库系统，它本身其实就是基于MySQL后台的完整数据库系统，MySQL运行时Innodb会在内存中建立缓冲池，用于缓冲数据和索引。但是该引擎不支持FULLTEXT类型的索引，而且它没有保存表的行数，当SELECT COUNT(*) FROM TABLE时需要扫描全表。当需要使用数据库事务时，该引擎当然是首选。由于锁的粒度更小，写操作不会锁定全表，所以在并发较高时，使用Innodb引擎会提升效率。但是使用行级锁也不是绝对的，如果在执行一个SQL语句时MySQL不能确定要扫描的范围，InnoDB表同样会锁全表。

```
//这个就是select锁表的一种，不明确主键。增删改查都可能会导致锁全表，在以后我们会详细列出。SELECT * FROM products WHERE name='Mouse' FOR UPDATE;
```

#用场景：**

#常更新的表，适合处理多重并发的更新请求。

#持事务。

#以从灾难中恢复（通过bin-log日志等）。

#键约束。只有他支持外键。

#持自动增加列属性auto_increment。

#QL官方对InnoDB的讲解：

#nnoDB给MySQL提供了具有提交、回滚和崩溃恢复能力的事务安全（ACID兼容）存储引擎。

#nnoDB锁定在行级并且也在SELECT语句提供一个Oracle风格一致的非锁定读，这些特色增加了多用户部署和性能。没有在InnoDB中扩大锁定的需要，因为在InnoDB中行级锁定适合非常小的空间。

#nnoDB也支持FOREIGN KEY强制。在SQL查询中，你可以自由地将InnoDB类型的表与其它MySQL的表的类型混合起来，甚至在同一个查询中也可以混合。

#nnoDB是为处理巨大数据量时的最大性能设计，它的CPU效率可能是任何其它基于磁盘的关系数据库引擎所不能匹敌的。

#InnoDB被用来在众多需要高性能的大型数据库站点上产生。

#充：**什么叫事务？简称ACID

#务的原子性(Atomicity)：指一个事务要么全部执行,要么不执行.也就是说一个事务不可能只执行了一半就停止了.比如你从取款机取钱,这个事务可以分成两个步骤:1划卡,2出钱.不可能划了卡,而钱却没出来.这两步必须同时完成.要么就不完成.

#务的一致性(Consistency)：指事务的运行并不改变数据库中数据的一致性.例如,完整性约束了a+b=10,一个事务改变了a,那么b也应该随之改变.

#立性(Isolation）:事务的独立性也有称作隔离性,是指两个以上的事务不会出现交错执行的状态.因为这样可能会导致数据不一致.

#久性(Durability）:事务的持久性是指事务执行成功以后,该事务所对数据库所作的更改便是持久的保存在数据库之中，不会无缘无故的回滚.

#MyIsam：

#义**：

#ASM是MySQL默认的引擎，但是它没有提供对数据库事务的支持，也不支持行级锁和外键，因此当INSERT(插入)或UPDATE(更新)数据时即写操作需要锁定整个表，效率便会低一些。

#sam 存储引擎独立于操作系统，也就是可以在windows上使用，也可以比较简单的将数据转移到linux操作系统上去。

#：引擎在创建表的时候，会创建三个文件，一个是.frm文件用于存储表的定义，一个是.MYD文件用于存储表的数据，另一个是.MYI文件，存储的是索引。操作系统对大文件的操作是比较慢的，这样将表分为三个文件，那么.MYD这个文件单独来存放数据自然可以优化数据库的查询等操作。**有索引管理和字段管理**。MyISAM还使用一种表格锁定的机制，来优化多个并发的读写操作，其代价是你需要经常运行OPTIMIZE TABLE命令，来恢复被更新机制所浪费的空间。

#用场景**：

#支持事务的设计，但是并不代表着有事务操作的项目不能用MyIsam存储引擎，可以在service层进行根据自己的业务需求进行相应的控制。

#支持外键的表设计。

#询速度很快，如果数据库insert和update的操作比较多的话比较适用。

#天 对表进行加锁的场景。

#yISAM极度强调快速读取操作。

#yIASM中存储了表的行数，于是SELECT COUNT(*) FROM TABLE时只需要直接读取已经保存好的值而不需要进行全表扫描。如果表的读操作远远多于写操作且不需要数据库事务的支持，那么MyIASM也是很好的选择。

#点：**

#能在表损坏后恢复数据。（是不能主动恢复）

#充**：ISAM索引方法–索引顺序存取方法

#义：**

#定义明确且历经时间考验的数据表格管理方法，它在设计之时就考虑到 数据库被查询的次数要远大于更新的次数。

#性：**

#M执行读取操作的速度很快，而且不占用大量的内存和存储资源。

#之初就预想数据组织成有固定长度的记录，按顺序存储的。—ISAM是一种静态索引结构。

#点：**

#不 支持事务处理

#不能够容错。如果你的硬盘崩溃了，那么数据文件就无法恢复了。如果你正在把ISAM用在关键任务应用程序里，那就必须经常备份你所有的实 时数据，通过其复制特性，MYSQL能够支持这样的备份应用程序。

#Memory（也叫HEAP）堆内存嘛：

#义**：

#在内存中的内容来创建表。每个MEMORY表只实际对应一个磁盘文件。MEMORY类型的表访问非常得快，因为它的数据是放在内存中的，并且默认使用HASH索引。

#旦服务关闭，表中的数据就会丢失掉。 HEAP允许只驻留在内存里的临时表格。驻留在内存里让HEAP要比ISAM和MYISAM都快，但是它所管理的数据是不稳定的，而且如果在关机之前没有进行保存，那么所有的数据都会丢失。在数据行被删除的时候，HEAP也不会浪费大量的空间。HEAP表格在你需要使用SELECT表达式来选择和操控数据的时候非常有用。

#用场景：**

#些内容变化不频繁的代码表，或者作为统计操作的中间结果表，便于高效地堆中间结果进行分析并得到最终的统计结果。

#标数据比较小，而且非常频繁的进行访问，在内存中存放数据，如果太大的数据会造成内存溢出。可以通过参数max_heap_table_size控制Memory表的大小，限制Memory表的最大的大小。

#据是临时的，而且必须立即可用得到，那么就可以放在内存中。

#储在Memory表中的数据如果突然间丢失的话也没有太大的关系。

Memory同时支持散列索引和B树索引，B树索引可以使用部分查询和通配查询，也可以使用<,>和>=等操作符方便数据挖掘，散列索引相等的比较快但是对于范围的比较慢很多。

#性要求：**

#求存储的数据是数据长度不变的格式，比如，Blob和Text类型的数据不可用（长度不固定的）。

#记住，在用完表格之后就删除表格。

#Mrg_Myisam：（分表的一种方式–水平分表）

#义：**

#相同的可以被当作一个来用的MyISAM表的集合。“相同”意味着所有表同样的列和索引信息。

#说，他将MyIsam引擎的多个表聚合起来，但是他的内部没有数据，真正的数据依然是MyIsam引擎的表中，但是可以直接进行查询、删除更新等操作。

#我们可能会遇到这样的问题，同一种类的数据会根据数据的时间分为多个表，如果这时候进行查询的话，就会比较麻烦，Merge可以直接将多个表聚合成一个表统一查询，然后再删除Merge表（删除的是定义），原来的数据不会影响。

#Blackhole（黑洞引擎）

#义**

#入到此引擎的数据均会被丢弃掉， 不做实际存储；Select语句的内容永远是空。

#弃所有的插入的数据，服务器会记录下Blackhole表的日志，所以可以用于复制数据到备份数据库。

#用场景**：

#证dump file语法的正确性

#使用blackhole引擎来检测binlog功能所需要的额外负载

#当日志服务器

#擎，大家感兴趣就各自先百度吧。本文主要是对比引擎使用以及其原理。

------

#nnoDB和MyIsam使用及其原理对比：

#使用的效果与区别展示：

#SQL数据库实战例子（存储引擎、视图、锁机制、分表）](http://download.csdn.net/detail/jack__frost/9885623)

#一）在一个普通数据库中创建两张分别以MyIsam和InnoDB作为存储引擎的表。**

```
create table testMyIsam(  id int unsigned primary key auto_increment,  name varchar(20) not null  )engine=myisam;  
 create table testInnoDB(  id int unsigned primary key auto_increment,  name varchar(20) not null  )engine=innodb;   
```

#，，效果如图，一会总结。

![这里写图片描述](https://img-blog.csdn.net/20170609162135050?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja19fRnJvc3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#二）对比插入效率（百万级插入）：**（虽然速度上MyISAM快，但是增删改是涉及事务安全的，所以用InnoDB相对好很多）

#好地对比，我们可以使用函数的方式或者存储过程的方式。博主采用存储过程。（存储过程在往后的章节会讲到）

```
//创建存储过程delimiter $$drop procedure if exists ptestmyisam;create procedure ptestmyisam()begindeclare pid int ;set pid = 1000000;while pid>0 doinsert into testmyisam(name) values(concat("fuzhu", pid));set pid = pid-1;end while;end $$ //使用存储过程：call ptestmyisam();
```

![这里写图片描述](https://img-blog.csdn.net/20170609165157759?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja19fRnJvc3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

```
//创建存储过程（尽量把Innodb的数量级压低，不然，，卡在那里半天也不奇怪）delimiter $$drop procedure if exists ptestInndb;create procedure ptestInndb()begindeclare pid int ;set pid = 1000000;while pid>0 doinsert into testinnodb(name) values(concat("fuzhu", pid));set pid = pid-1;end while;end $$ //使用存储过程：call ptestInndb();
```

#在几次innodb测试百万插入的时候，数据库炸了（笑哭）。最终只成功插入1W条。可见效率对比。

#nnodb默认是开启事务的，如果我们把事务给停了，会快很多。

```
//停掉事务set autocommit = 0;  //调用存储过程call ptestInndb; //重启事务set autocommit = 1;  
```

![这里写图片描述](https://img-blog.csdn.net/20170609170222601?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja19fRnJvc3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#三）对比更新：**（虽然速度上MyISAM快，但是增删改是涉及事务安全的，所以InnoDB相对好很多）

```
//耗时3秒多update testinnodb set name = 'fuzhu' where id>0 and id<10000;//耗时0.171秒update testmyisam set name = 'fuzhu' where id>0 and id<13525;
```

#四）查询对比**：

#询总数目

```
select count(*) from testInnoDB;  select count(*) from testMyIsam;
```

#innodb查一万跟myisam查一百万的区别？？效果对比立刻出现。

![这里写图片描述](https://img-blog.csdn.net/20170609202613940?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja19fRnJvc3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#询无索引的列：（这些都自己拿我给出或者自己写的数据库去体验下吧，，差距很明显）

```
select * from testMyIsam where name > "fuzhu100" ; select * from testInnoDB where name > "fuzhu100" ;
```

#询有索引的列：

```
select * from testMyIsam where  id > 10 ;select * from testinnodb where  id > 10 ;
```

#储大小：

#tMyIsam 存了一百万。testinnodb 存了两万。

![这里写图片描述](https://img-blog.csdn.net/20170609211502417?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja19fRnJvc3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#效果对比总述：

#）事务**。MyISAM类型不支持事务处理等高级处理，而InnoDB类型支持，提供事务支持已经外部键等高级数据库功能。

#oDB表的行锁也不是绝对的，假如在执行一个SQL语句时MySQL不能确定要扫描的范围，InnoDB表同样会锁全表，例如updatetable set num=1 where name like “a%”

#在不确定的范围时，InnoDB还是会锁表的。

#）性能主题**。MyISAM类型的表强调的是性能，其执行数度比InnoDB类型更快。

#）行数保存**。InnoDB 中不保存表的具体行数，也就是说，执行select count(*) fromtable时，InnoDB要扫描一遍整个表来计算有多少行，但是MyISAM只要简单的读出保存好的行数即可。注意的是，当count(*)语句包含where条件时，两种表的操作是一样的。

#）索引存储**。对于AUTO_INCREMENT类型的字段，InnoDB中必须包含只有该字段的索引，但是在MyISAM表中，可以和其他字段一起建立联合索引。

#SAM支持全文索引（FULLTEXT）、压缩索引，InnoDB不支持

#SAM的索引和数据是分开的，并且索引是有压缩的，内存使用率就对应提高了不少。能加载更多索引，而Innodb是索引和数据是紧密捆绑的，没有使用压缩从而会造成Innodb比MyISAM体积庞大不小。

#oDB存储引擎被完全与MySQL服务器整合，InnoDB存储引擎为在主内存中缓存数据和索引而维持它自己的缓冲池。InnoDB存储它的表＆索引在一个表空间中，表空间可以包含数个文件（或原始磁盘分区）。这与MyISAM表不同，比如在MyISAM表中每个表被存在分离的文件中。InnoDB 表可以是任何尺寸，即使在文件尺寸被限制为2GB的操作系统上。

#）服务器数据备份**。InnoDB必须导出SQL来备份，LOAD TABLE FROM MASTER操作对InnoDB是不起作用的，解决方法是首先把InnoDB表改成MyISAM表，导入数据后再改成InnoDB表，但是对于使用的额外的InnoDB特性(例如外键)的表不适用。

#yISAM应对错误编码导致的数据恢复速度快。MyISAM的数据是以文件的形式存储，所以在跨平台的数据转移中会很方便。在备份和恢复时可单独针对某个表进行操作。

#oDB是拷贝数据文件、备份 binlog，或者用 mysqldump，在数据量达到几十G的时候就相对痛苦了。

#）锁的支持。**MyISAM只支持表锁。InnoDB支持表锁、行锁 行锁大幅度提高了多用户并发操作的新能。但是InnoDB的行锁，只是在WHERE的主键是有效的，非主键的WHERE都会锁全表的

```
//用于把表的拷贝从主服务器转移到从属服务器。LOAD TABLE tbl_name FROM MASTER 
```

#使用建议：

#点**必须使用** InnoDB：

#靠性高或者要求事务处理，则使用InnoDB。这个是必须的。

#更新和查询都相当的频繁，并且**表锁定的机会比较大**的情况指定InnoDB数据引擎的创建。

#下，MyISAM的使用场景：

#很多count的计算的。如一些日志，调查的业务表。

#入修改不频繁，查询非常频繁的。

#QL能够允许你在表这一层应用数据库引擎，所以你可以只对需要事务处理的表格来进行性能优化，而把不需要事务处理的表格交给更加轻便的MyISAM引擎。对于 MySQL而言，灵活性才是关键。

------

#nnoDB和MyIsam引擎原理：

#前，先去理解什么是聚簇和非聚簇索引。

#MyIASM引擎的索引结构：

#SAM索引结构: MyISAM索引用的B+ tree来储存数据，MyISAM索引的指针指向的是键值的地址，地址存储的是数据。

#ree的数据域存储的内容为实际数据的地址，也就是说它的索引和实际的数据是分开的，只不过是用索引指向了实际的数据，这种索引就是所谓的非聚集索引。

#自博客](http://blog.csdn.net/nuli888/article/details/52443011)

![这里写图片描述](https://img-blog.csdn.net/20170610130558046?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja19fRnJvc3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#此，过程为：** MyISAM中索引检索的算法为首先按照B+Tree搜索算法搜索索引，如果指定的Key存在，则取出其data域的值，然后以data域的值为地址，根据data域的值去读取相应数据记录。

#InnoDB引擎的索引结构：

#+Treee索引结构。Innodb的索引文件本身就是数据文件，即B+Tree的数据域存储的就是实际的数据，这种索引就是聚集索引。这个索引的key就是数据表的主键，因此InnoDB表数据文件本身就是主索引。

#oDB的辅助索引数据域存储的也是相应记录主键的值而不是地址，所以当以辅助索引查找时，会先根据辅助索引找到主键，再根据主键索引找到实际的数据。所以Innodb不建议使用过长的主键，否则会使辅助索引变得过大。

#用自增的字段作为主键，这样B+Tree的每一个结点都会被顺序的填满，而不会频繁的分裂调整，会有效的提升插入数据的效率。

![这里写图片描述](https://img-blog.csdn.net/20170610152711519?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja19fRnJvc3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#可以看到叶节点包含了完整的数据记录。这种索引叫做聚集索引。因为InnoDB的数据文件本身要按主键聚集，所以InnoDB要求表必须有主键（MyISAM可以没有），如果没有显式指定，则MySQL系统会自动选择一个可以唯一标识数据记录的列作为主键，如果不存在这种列，则MySQL自动为InnoDB表生成一个隐含字段作为主键，这个字段长度为6个字节，类型为长整形。

#与MyISAM索引的不同是InnoDB的辅助索引data域存储相应记录主键的值而不是地址。换句话说，InnoDB的所有辅助索引都引用主键作为data域。

#此，过程为：**将主键组织到一棵B+树中，而行数据就储存在叶子节点上，若使用”where id = 13”这样的条件查找主键，则按照B+树的检索算法即可查找到对应的叶节点，之后获得行数据。若对Name列进行条件搜索，则需要两个步骤：第一步在辅助索引B+树中检索Name，到达其叶子节点获取对应的主键。第二步使用主键在主索引B+树种再执行一次B+树检索操作，最终到达叶子节点即可获取整行数据。

------

#余引擎的使用DEMO（主要是Mrg_Myisam分表）

#ory（Heap）：

```
CREATE TABLE tbHeap (id int unsigned primary key auto_increment,  name varchar(20) not null  ) TYPE=Heap
```

#1）Mrg_Myisam引擎分表：**

#先创建两张user表，也就是说我要把用户表进行水平分表（因为用户太多啦，10亿）

```
//用户表一CREATE TABLE IF NOT EXISTS `user1` (   `id` int(11) NOT NULL , `name` varchar(50) DEFAULT NULL,   PRIMARY KEY (`id`)  ) ENGINE=MyISAM  DEFAULT CHARSET=utf8 ;   //用户表二CREATE TABLE IF NOT EXISTS `user2` (   `id` int(11) NOT NULL , `name` varchar(50) DEFAULT NULL,   PRIMARY KEY (`id`)  ) ENGINE=MyISAM  DEFAULT CHARSET=utf8 ;   //分别插入两条测试数据先INSERT INTO `user1` (`name`) VALUES('辅助');  INSERT INTO `user2` (`name`) VALUES('JackFrost');  
```

#要求：

#表必须使用MyISAM存储引擎；

#个分表的表结构必须相同；

#ySQL必须具有存储分表数据文件和索引文件的目录的读写权限；

#须启用MySQL的符号链接支持功能。

#储：

#YD文件是MyISAM表的数据文件；

#YI文件是MyISAM表的索引文件；

#rm文件用于存储MyISAM表的表结构。

#最后创建一个MERGE表，作为一个分发作用的总表。

```
CREATE TABLE IF NOT EXISTS `alluser` (    `id` int(11) NOT NULL ,    `name` varchar(50) DEFAULT NULL,     PRIMARY KEY (`id`)) ENGINE=MRG_MYISAM  DEFAULT CHARSET=utf8 UNION=(user1,user2)  ;  
```

#测试：

#查询：

```
select id,name from alluser;  
```

#据总表把所有数据都查出来了

![这里写图片描述](https://img-blog.csdn.net/20170610231155518?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja19fRnJvc3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#然而，当你测试插入的时候：

#现，总表只有只读权限

![这里写图片描述](https://img-blog.csdn.net/20170610234526266?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja19fRnJvc3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#但是我们又想在总表去插入，怎么办呢？？

#过总表插入数据，数据存储在MRG文件列出的第一个分表之中。例如，执行以下SQL语句，将总表的INSERT_METHOD修改为FIRST，然后通过总表插入一条数据：

```
//就是插入总表的时候，其实也是插入到第一个分表。ALTER TABLE `test_engine`.`alluser` INSERT_METHOD = FIRST;  INSERT INTO `alluser` (id,`name`) VALUES(2,'插入到第一个分表');  
```

![这里写图片描述](https://img-blog.csdn.net/20170611131737068?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja19fRnJvc3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](https://img-blog.csdn.net/20170611131745780?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja19fRnJvc3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#许通过总表插入数据，数据存储在MRG文件列出的最后一个分表之中。例如，执行以下SQL语句，将总表的INSERT_METHOD修改为LAST，然后通过总表插入一条数据：

```
//就是插入总表的时候，其实也是插入到最后一个分表。ALTER TABLE `test_engine`.`alluser` INSERT_METHOD = LAST;
```

![这里写图片描述](https://img-blog.csdn.net/20170611132015641?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja19fRnJvc3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](https://img-blog.csdn.net/20170611132023290?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSmFja19fRnJvc3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#NSERT_METHOD ：

#ERT_METHOD选项只会影响通过总表插入（INSERT）数据的行为，通过总表对数据进行删除（DELETE）、查询（SELECT）、修改（UPDATE）、清空（TRUNCATE）都不会受影响。

#2）项目中如何使用MRG_MYISAM总表：**

#插入（INSERT）数据时，需要根据给定的路由策略将新数据分别插入不同的子表，此处采用对id进行模3计算（可能结果为0、1、2）来决定插入哪个子表。

#们需要创建一张表专门去创建id。

```
CREATE TABLE `create_id` (    `id` INT(11) NOT NULL AUTO_INCREMENT,    PRIMARY KEY (`id`))COLLATE='utf8_general_ci'ENGINE=MyISAM;
```

#ORM框架的时候使用上面这个表生成id

```
insert into create_id () values();
```

#真正插入的时候：

#ID，然后根据拿到的创建ID去插入到分表。

```
INSERT INTO {$table_name} (id, name) VALUES ('{$id}');
```

#获取表名和ID：

#需要我们自定义自己的规则了。比如取余、比如范围预判断。

#删除（DELETE）、查询（SELECT）、修改（UPDATE）、清空（TRUNCATE）等操作都可以通过总表alluser完成。

#RG_MYISAM存储引擎实现的分表机制，比较适用于插入和查询频率较高的场景。由于MyISAM具有表级别的锁机制，所以不适用于更新频率较高的场景。

#3）MRG_MYISAM分表的优点：**

#适用于存储日志数据。例如，可以将不同月份的数据存入不同的表，然后使用一些工具压缩数据，最后通过一张MRG_MYISAM表来查询这些数据。

#可以获得更快的速度。可以根据某种指标，将一张只读的大表分割成若干张小表，然后将这些小表分别放在不同的磁盘上存储。当需要读取数据时，MERGE表可以将这些小表的数据组织起来，就好像使用先前的大表一样，但是速度会快很多。

#可以提高搜索效率。可以根据某种指标将一张只读的大数据表分割为若干个小表，然后根据不同的查询维度，可以得到若干种小表的组合，然后再为这些组合分别创建不同的MERGE表。例如，有一张只读的大数据表T，分割为T1、T2、T3、T4，共4张小表，有两种查询维度A和B，A可以得到小表组合T1、T2和T3，B可以得到小表组合T2、T3和T4，分别为A和B创建两个MERGE表，也就是M1和M2，这两个MERGE表分别关联的小表是存在交叠的。

#可以更加有效的修复表。修复单个的小表要比修复大数据表更加容易。

#多个子表映射至一个总表的速度极快。因为MERGE表本身不会存储和维护任何索引，索引都是由各个关联的子表存储和维护的，所以创建和重新映射MERGE表的速度非常快。

#不受操作系统的文件大小限制。单个表会受到文件大小的限制，但是拆分成多个表，则可以无限扩容。

#MERGE表还可以用来给单个表创建别名，并且几乎不会影响性能。

#4）使用MRG_MyISAM分表的必须思考问题：**（针对总表）

#总表（MERGE表）必须使用MRG_MyISAM存储引擎，子表必须使用MyISAM存储引擎，不可避免会受到MyISAM存储引擎的限制。（比如不支持事务）

#MERGE表不能使用某些MyISAM特性。例如，虽然可以为子表创建全文索引，但是却不能使用全文索引，只能通过MERGE表查询数据。

#若使用ALTER TABLE语句修改总表的存储引擎，那么会立即丢失总表和子表的映射关系，并且会将所有子表的数据拷贝至修改后的新表。

#总表和子表的主键都不能使用自动增长（auto increment）。

#子表之间不能保证唯一键约束，只能保证单个子表内部的唯一性约束。也就是说，直接查总表全部，id可能会重复。

#由于不能保证唯一键约束，导致REPLACE语句的行为会不可预期，INSERT … ON DUPLICATE KEY UPDATE语句也有类似问题。因此，只能使用路由策略，对子表使用这些语句，而不能对总表使用。

#当正在使用总表时，不能对任何子表执行ANALYZE TABLE、REPAIR TABLE、OPTIMIZE TABLE、ALTER TABLE、DROP TABLE、DELETE或TRUNCATE TABLE语句，否则会导致不可预期的结果。

#总表和子表的表结构必须完全一致。

#总表可以映射的所有子表的总行数上限为 2的64次方 行。

#不支持INSERT DELAYED语句