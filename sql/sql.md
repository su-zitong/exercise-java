# MySQL

共计32题。

### ❓Q1: MySQL的逻辑架构了解吗?

第一层是服务器层，主要提供连接处理、授权认证、安全等功能。
第二层实现了MySQL核心服务功能，包括查询解析、分析、优化、缓存以及日期和时间等所有内置函
数，所有跨存储弓|擎的功能都在这一层实现， 例如存储过程、触发器、视图等。
第三层是存储引擎层，存储引擎负责MySQL中数据的存储和提取。服务器通过API与存储引擎通信,
这些接口屏蔽了不同存储引擎的差异，使得差异对上层查询过程透明。除了会解析外键定义的InnoDB
外，存储弓|擎不会解析SQL,不同存储引擎之间也不会相互通信，只是简单响应上层服务器请求。



### ❓Q2:谈一谈MySQL的读写锁

在处理并发读或写时，可以通过实现一个由两种类型组成的锁系统来解决问题。这两种类型的锁通常被
称为共享锁和排它锁，也叫读锁和写锁。读锁是共享的，相互不阻塞，多个客户在同一时刻可以同时读
取同一个资源而不相互干扰。写锁则是排他的，也就是说- -个写锁会阻塞其他的写锁和读锁，确保在给
定时间内只有一个用户能执行写入并防止其他用户读取正在写入的同一资源。
在实际的数据库系统中，每时每刻都在发生锁定，当某个用户在修改某一部分数据时，MySQL会通过
锁定防止其他用户读取同一数据。写锁比读锁有更高的优先级，一个写锁请求可能会被插入到读锁队列
的前面，但是读锁不能插入到写锁前面。



### ❓Q3: MySQL的锁策略有什么?

表锁是MySQL中最基本的锁策略，并且是开销最小的策略。表锁会锁定整张表，一个用户在对表进行写
操作前需要先获得写锁，这会阻塞其他用户对该表的所有读写操作。只有没有写锁时，其他读取的用户
才能获取读锁，读锁之间不相互阻塞。
行锁可以最大程度地支持并发，同时也带来了最大开销。InnoDB 和XtraDB以及一些其他存储引擎实
现了行锁。行锁只在存储引擎层实现，而服务器层没有实现。



### ❓Q4:数据库死锁如何解决?

死锁是指多个事务在同一资源上相互占用并请求锁定对方占用的资源而导致恶性循环的现象。当多个事
务试图以不同顺序锁定资源时就可能会产生死锁，多个事务同时锁定同一个资源时也会产生死锁。
为了解决死锁问题，数据库系统实现了各种死锁检测和死锁超时机制。越复杂的系统，例如InnoDB 存
储引擎，越能检测到死锁的循环依赖，并立即返回-一个错误。这种解决方式很有效，否则死锁会导致出
现非常慢的查询。还有-种解决方法，就是当查询的时间达到锁等待超时的设定后放弃锁请求，这种方
式通常来说不太好。InnoDB 目前处理死锁的方法是将持有最少行级排它锁的事务进行回滚。
死锁发生之后，只有部分或者完全回滚其中一个事务，才能打破死锁。对于事务型系统这是无法避免
的，所以应用程序在设计时必须考虑如何处理死锁。大多数情况下只需要重新执行因死锁回滚的事务即
可。



### ❓Q5:事务是什么?

事务是一组原子性的SQL查询，或者说- -个独立的工作单元。如果数据库引擎能够成功地对数据库应用
该组查询的全部语句，那么就执行该组查询。如果其中有任何一条语句因为崩溃或其他原因无法执行,
那么所有的语句都不会执行。也就是说事务内的语句要么全部执行成功，要么全部执行失败。



### ❓Q6:事务有什么特性?

原子性atomicity
-个事务在逻辑上是必须不可分割的最小工作单元，整个事务中的所有操作要么全部提交成功，要么全
部失败回滚，对于-一个事务来说不可能只执行其中的一部分。
-致性consistency
数据库总是从一个-致性的状态转换到另-一个- 致性的状态。
隔离性isolation
针对并发事务而言，隔离性就是要隔离并发运行的多个事务之间的相互影响，一般来说一 个事务所做的
修改在最终提交以前，对其他事务是不可见的。
持久性durability
-旦事务提交成功，其修改就会永久保存到数据库中，此时即使系统崩溃，修改的数据也不会丢失。



### ❓Q7: MySQL的隔离级别有哪些?

未提交读READ UNCOMMITTED
在该级别事务中的修改即使没有被提交，对其他事务也是可见的。事务可以读取其他事务修改完但未提
交的数据，这种问题称为脏读。这个级别还会导致不可重复读和幻读，性能没有比其他级别好很多，很
少使用。
提交读READ COMMITTED
多数数据库系统默认的隔离级别。提交读满足了隔离性的简单定义: -个事务开始时只能”看见"已经提
交的事务所做的修改。换句话说，一个事务从开始直到提交之前的任何修改对其他事务都是不可见的。
也叫不可重复读，因为两次执行同样的查询可能会得到不同结果。
可重复读REPEATABLE READ (MySQL 默认的隔离级别)
可重复读解决了不可重复读的问题，保证了在同一个事务中多次读取同样的记录结果-致。但还是无法
解决幻读，所谓幻读指的是当某个事务在读取某个范围内的记录时，会产生幻行。InnoDB 存储引擎通
过多版本并发控制MVCC解决幻读的问题。
可串行化SERIALIZABLE
最高的隔离级别，通过强制事务串行执行，避免幻读。可串行化会在读取的每- -行数据上都加锁，可能
导致大量的超时和锁争用的问题。实际应用中很少用到这个隔离级别，只有非常需要确保数据一致性且
可以接受没有并发的情况下才考虑该级别。

### ❓Q8: MVCC是什么?

MVCC是多版本并发控制，在很多情况下避免加锁，大都实现了非阻塞的读操作，写操作也只锁定必要
的行。
InnoDB的MVCC通过在每行记录后面保存两个隐藏的列来实现，这两个列一个保存了行的创建时间,
-个保存行的过期时间间。不过存储的不是实际的时间值而是系统版本号，每开始一个新的事务系统版
本号都会自动递增，事务开始时刻的系统版本号会作为事务的版本号，用来和查询到的每行记录的版本
号进行比较。
MVCC只能在READ COMMITTED 和REPEATABLE READ 两个隔离级别下1工作，因为READ
UNCOMMITTED总是读取最新的数据行，而不是符合当前事务版本的数据行，而SERIALIZABLE则会对
所有读取的行都加锁。





### ❓Q9:谈一谈InnoDB

InnoDB是MySQL的默认事务型引擎，用来处理大量短期事务。InnoDB 的性能和自动崩溃恢复特性使
得它在非事务型存储需求中也很流行，除非有特别原因否则应该优先考虑InnoDB。
InnoDB的数据存储在表空间中，表空间由一系列数据文件组成。MySQL4.1 后InnoDB可以将每个表:
的数据和索引放在单独的文件中。
InnoDB采用MVCC来支持高并发，并且实现了四个标准的隔离级别。其默认级别是REPEATABLE
READ，并通过间隙锁策略防止幻读，间隙锁使InnoDB不仅仅锁定查询涉及的行，还会对索引中的间
隙进行锁定防止幻行的插入。
InnoDB表是基于聚簇索引建立的，InnoDB 的索引结构和其他存储引擎有很大不同，聚簇索引对主键
查询有很高的性能，不过它的二级索引中必须包含主键列，所以如果主键很大的话其他所有索引都会很
大，因此如果表上索引较多的话主键应当尽可能小。
InnoDB的存储格式是平***立的，可以将数据和索引文件从一个平台复制到另一个平台。
InnoDB内部做了很多优化，包括从磁盘读取数据时采用的可预测性预读，能够自动在内存中创建加速
读操作的自适应哈希索引，以及能够加速插入操作的插入缓冲区等。





### ❓Q10:谈-谈MyISAM

MySQL5.1及之前，MyISAM是默认存储引擎，MyISAM 提供了大量的特性，包括全文索引、压缩、空
间函数等，但不支持事务和行锁，最大的缺陷就是崩溃后无法安全恢复。对于只读的数据或者表比较
小、可以忍受修复操作的情况仍然可以使用MylSAM。
MyISAM将表存储在数据文件和索引文件中，分别以.MYD和| .MYI作为扩展名。MyISAM表可以包
含动态或者静态行，MySQL会根据表的定义决定行格式。MyISAM表可以存储的行记录数一般受限于
可用磁盘空间或者操作系统中单个文件的最大尺寸。
MyISAM对整张表进行加锁，读取时会对需要读到的所有表加共享锁，写入时则对表加排它锁。但是在
表有读取查询的同时，也支持并发往表中插入新的记录。
对于MyISAM表，MySQL可以手动或自动执行检查和修复操作，这里的修复和事务恢复以及崩溃恢复
的概念不同。执行表的修复可能导致一些数据丢失，而且修复操作很慢。
对于MyISAM表，即使是BLOB和TEXT等长字段，也可以基于其前500个字符创建索引。MyISAM
也支持全文索引，这是-种基于分词创建的索引，可以支持复杂的查询。
MyISAM设计简单，数据以紧密格式存储，所以在某些场景下性能很好。MyISAM最典型的性能问题还
是表锁问题，如果所有的查询长期处于Locked状态，那么原因毫无疑问就是表锁。





### ❓Q11:谈- -谈Memory

如果需要快速访问数据且这些数据不会被修改，重启以后丢失也没有关系，那么使用Memory表是非
常有用的。Memory表至少要比MyISAM表快一个数量级，因为所有数据都保存在内存，不需要磁盘
I0，Memory 表的结构在重启后会保留，但数据会丢失。
Memory表适合的场景:查找或者映射表、缓存周期性聚合数据的结果、保存数据分析中产生的中间数
据。
Memory表支持哈希索引，因此查找速度极快。虽然速度很快但还是无法取代传统的基于磁盘的表,
Memory表使用表级锁，因此并发写入的性能较低。它不支持BLOB和TEXT类型的列，并且每行的长
度是固定的，所以即使指定了VARCHAR列，实际存储时也会转换成CHAR，这可能导致部分内存的浪
费。
如果MySQL在执行查询的过程中需要使用临时表来保持中间结果，内部使用的临时表就是Memory
表。如果中间结果太大超出了Memory表的限制，或者含有BLOB或TEXT字段，临时表会转换成
MyISAM表。





### ❓Q12:查询执行流程是什么?

简单来说分为五步:①客户端发送-条查询给服务器。②服务器先检查查询缓存，如果命中了缓存则
立刻返回存储在缓存中的结果，否则进入下一阶段。③服务器端进行SQL解析、预处理，再由优化器
生成对应的执行计划。④MySQL根据优化器生成的执行计划，调用存储引擎的API来执行查询。⑤将
结果返回给客户端。



### ❓Q13: VARCHAR和CHAR的区别?

VARCHAR用于存储可变字符串，是最常见的字符串数据类型。它比CHAR更节省空间，因为它仅使用
必要的空间。VARCHAR需要1或2个额外字节记录字符串长度，如果列的最大长度不大于255字节则
只需要1字节。VARCHAR不会删除末尾空格。
VARCHAR适用场景:字符串列的最大长度比平均长度大很多、列的更新很少、使用了UTF8这种复杂
字符集，每个字符都使用不同的字节数存储。
CHAR是定长的，根据定义的字符串长度分配足够的空间。CHAR会删除末尾空格。
CHAR适合存储很短的字符串，或所有值都接近同一个长度，例如存储密码的MD5值。对于经常变更
的数据，CHAR 也比VARCHAR更好，因为定长的CHAR不容易产生碎片。对于非常短的列，CHAR在
存储空间上也更有效率,例如用CHAR来存储只有Y和N的值只需要一个字节，但是VARCHAR需要
两个字节，因为还有一个记录长度的额外字节。





### ❓Q14: DATETIME 和TIMESTAMP的区别?

DATETIME能保存大范围的值，从1001~9999年，精度为秒。把日期和时间封装到了一一个整数中，与
时区无关，使用8字节存储空间。
TIMESTAMP和UNIX时间戳相同，只使用4字节的存储空间，范围比DATETIME小得多，只能表示
1970- -2038 年，并且依赖于时区。





### ❓Q15:数据类型有哪些优化策略?

更小的通常更好
一般情况下尽量使用可以正确存储数据的最小数据类型，更小的数据类型通常也更快，因为它们占用更
少的磁盘、内存和CPU缓存。
尽可能简单
简单数据类型的操作通常需要更少的CPU周期，例如整数比字符操作代价更低，因为字符集和校对规
则使字符相比整形更复杂。应该使用MySQL的内建类型date、time 和datetime而不是字符串来存储
日期和时间，另一点是应该使用整形存储IP地址。
尽量避免NULL
通常情况下最好指定列为NOT NULL，除非需要存储NULL值。因为如果查询中包含可为NULL的列对
MySQL来说更难优化，可为NULL的列使索引、索引统计和值比较都更复杂，并且会使用更多存储空
间。当可为NULL的列被索引时，每个索引记录需要一个额外字节， 在MyISAM中还可能导致固定大小
的索引变成可变大小的索引。
如果计划在列上建索引，就应该尽量避免设计成可为NULL的列。



### ❓Q16:索引有什么作用?

索引也叫键，是存储引擎用于快速找到记录的一种数据结构。索引对于良好的性能很关键，尤其是当表，
中数据量越来越大时，索弓|对性能的影响愈发重要。在数据量较小且负载较低时，不恰当的索引对性能
的影响可能还不明显，但数据量逐渐增大时，性能会急剧下降。
索引大大减少了服务器需要扫描的数据量、可以帮助服务器避免排序和临时表、可以将随机10变成顺
序I0。但索引并不总是最好的工具，对于非常小的表，大部分情况下会采用全表扫描。对于中到大型的
表，索引就非常有效。但对于特大型的表，建立和使用索引的代价也随之增长，这种情况下应该使用分
区技术。
在MySQL中，首先在索引中找到对应的值，然后根据匹配的索引记录找到对应的数据行。索引可以包括
-个或多个列的值，如果索引包含多个列，那么列的顺序也十分重要，因为MySQL只能使用索引的最
左前缀。



### ❓Q17:谈-谈MySQL的B-Tree索引

大多数MySQL引擎都支持这种索引，但底层的存储引擎可能使用不同的存储结构，例如NDB使用T-
Tree，而InnoDB使用B+ Tree。
B-Tree通常意味着所有的值都是按顺序存储的，并且每个叶子页到根的距离相同。B-Tree 索引能够加
快访问数据的速度，因为存储引擎不再需要进行全表扫描来获取需要的数据，取而代之的是从索引的根
节点开始进行搜索。根节点的槽中存放了指向子节点的指针，存储引擎根据这些指针向下层查找。通过
比较节点页的值和要查找的值可以找到合适的指针进入下层子节点，这些指针实际上定义了子节点页中
值的.上限和下限。最终存储引擎要么找到对应的值，要么该记录不存在。叶子节点的指针指向的是被索
引的数据，而不是其他的节点页。
B-Tree索引的限制:
●如果不是按照索引的最左列开始查找，则无法使用索引。
●不能跳过索引中的列，例如索引为(id,name,sex)，不能只使用id和sex而跳过name。
●如果查询中有某个列的范围查询，则其右边的所有列都无法使用索引。



### ❓Q18:了 解Hash索引吗?

哈希索引基于哈希表实现，只有精确匹配索引所有列的查询才有效。对于每一行数据，存储引擎都会对
所有的索引列计算一个哈希码， 哈希码是一个较小的值， 并且不同键值的行计算出的哈希码也不一样。
哈希索引将所有的哈希码存储在索引中，同时在哈希表中保存指向每个数据行的指针。
只有Memory引擎显式支持哈希索引，这也是Memory引擎的默认索引类型。
因为索引自身只需存储对应的哈希值，所以索引的结构十分紧凑，这让哈希索引的速度非常快，但它也
有一些限制:
●哈希索引数据不是按照索引值顺序存储的，无法用于排序。
●哈希索引不支持部分索引列匹配查找，因为哈希索引始终是使用索引列的全部内容来计算哈希值
的。例如在数据列(a,b)上建立哈希索引，如果查询的列只有a就无法使用该索引。
哈希索引只支持等值比较查询，不支持任何范围查询。



### ❓Q19:什么是自适应哈希索引?

自适应哈希索引是InnoDB引擎的一个特殊功能，当它注意到某些索引值被使用的非常频繁时，会在内
存中基于B-Tree索引之上再创键一个哈希索引，这样就让B-Tree索引也具有哈希索引的一些优点，比
如快速哈希查找。这是一个完全自动的内部行为，用户无法控制或配置，但如果有必要可以关闭该功
能。



### ❓Q20 :什么是空间索引?

MyISAM表支持空间索引，可以用作地理数据存储。和B-Tree索引不同，这类索引无需前缀查询。空
间索引会从所有维度来索引数据，查询时可以有效地使用任意维度来组合查询。必须使用MySQL的
GIS即地理信息系统的相关函数来维护数据，但MySQL对GIS的支持并不完善，因此大部分人都不会
使用这个特性。



### ❓Q21:什么是全文索引?

通过数值比较、范围过滤等就可以完成绝大多数需要的查询，但如果希望通过关键字匹配进行查询，就
需要基于相似度的查询，而不是精确的数值比较，全文索引就是为这种场景设计的。
MyISAM的全文索引是一种特殊的B-Tree索引，共有两层。 第一层是所有关键字,然后对于每- -个
关键字的第二层，包含的是- -组相关的"文档指针"。全文索引不会索引文档对象中的所有词语，它会根
据规则过滤掉一些词语， 例如停用词列表中的词都不会被索引。



### ❓Q22:什么是聚簇索引?

聚簇索引不是一种索引类型，而是一种数据存储方式。InnoDB的聚簇索引实际上在同一个结构中保存
了B-Tree索引和数据行。当表有聚餐索引时，它的行数据实际上存放在索引的叶子页中，因为无法同
时把数据行存放在两个不同的地方，所以一个表只能有一个聚簇索引。
优点:①可以把相关数据保存在一起。②数据访问更快，聚簇索引将索引和数据保存在同-个B-Tree
中，因此获取数据比非聚簇索引要更快。③使用覆盖索引扫描的查询可以直接使用页节点中的主键值。
缺点:①聚簇索引最大限度提高了10密集型应用的性能，如果数据全部在内存中将会失去优势。②更
新聚簇索引列的代价很高，因为会强制每个被更新的行移动到新位置。③基于聚簇索引的表插入新行或
主键被更新导致行移动时，可能导致页分裂，表会占用更多磁盘空间。④当行稀疏或由于页分裂导致数
据存储不连续时，全表扫描可能很慢。



### ❓Q23:什么是覆盖索引?

覆盖索引指一个索引包含或覆盖了所有需要查询的字段的值，不再需要根据索引回表查询数据。覆盖索
引必须要存储索引列的值，因此MySQL只能使用B-Tree索引做覆盖索引。
优点:①索引条目通常远小于数据行大小，可以极大减少数据访问量。②因为索引按照列值顺序存
储，所以对于10密集型防伪查询回避随机从磁盘读取每一-行数据的I0少得多。③由于InnoDB使用
聚簇索引，覆盖索引对InnoDB很有帮助。InnoDB 的二级索引在叶子节点保存了行的主键值，如果二
级主键能覆盖查询那么可以避免对主键索引的二次查询。



### ❓Q24:你知道哪些索引使用原则?

建立索引
对查询频次较高且数据量比较大的表建立索引。索引字段的选择，最佳候选列应当从WHERE子句的条
件中提取，如果WHERE子句中的组合比较多，应当挑选最常用、过滤效果最好的列的组合。业务上具
有唯一特性的字段，即使是多个字段的组合，也必须建成唯一索引。
使用前缀索引
索引列开始的部分字符，索引创建后也是使用硬盘来存储的，因此短索引可以提升索引访问的10效
率。对于BLOB、TEXT或很长的VARCHAR列必须使用前缀索引，MySQL 不允许索引这些列的完整长
度。前缀索引是一种能使索引更小更快的有效方法，但缺点是MySQL无法使用前缀索引做ORDER BY
和GROUP BY,也无法使用前缀索引做覆盖扫描。
选择合适的索引顺序
当不需要考虑排序和分组时，将选择性最高的列放在前面。索引的选择性是指不重复的索引值和数据表
的记录总数之比，索引的选择性越高则查询效率越高，唯一索引的选择性是1,因此也可以使用唯一-索
引提升查询效率。
删除无用索引
MySQL允许在相同列上创建多个索引，重复的索引需要单独维护，并且优化器在优化查询时也需要逐
个考虑，这会影响性能。重复索引是指在相同的列上按照相同的顺序创建的相同类型的索引，应该避免
创建重复索引。如果创建了索引(A,B)再创建索引(A)就是冗余索引，因为这只是前一一个索引的前缀索
引，对于B-Tree索引来说是冗余的。解决重复索引和冗余索引的方法就是删除这些索引。除了重复索
引和冗余索引，可能还会有- -些服务器永远不用的索引，也应该考虑删除。

### ❓Q25:索引失效的情况有哪些?

如果索引列出现了隐式类型转换，则MySQL不会使用索引。常见的情况是在SQL的WHERE条件中字
段类型为字符串，其值为数值，如果没有加引号那么MySQL不会使用索引。
如果WHERE条件中含有OR，除非OR前使用了索引列而OR之后是非索引列，索引会失效。
MySQL不能在索引中执行LIKE操作，这是底层存储引擎API的限制，最左匹配的LIKE比较会被转换
为简单的比较操作，但如果是以通配符开头的LIKE查询，存储引擎就无法做比较。这种情况下MySQL
只能提取数据行的值而不是索引值来做比较。
如果查询中的列不是独立的，则MySQL不会使用索引。独立的列是指索引列不能是表达式的一部分，
也不能是函数的参数。
对于多个范围条件查询，MySQL 无法使用第一个范围列后面的其他索引列，对于多个等值查询则没有
这种限制。
如果MySQL判断全表扫描比使用索引查询更快，则不会使用索引。
索引文件具有B-Tree的最左前缀匹配特性，如果左边的值未确定，那么无法使用此索引。

### ❓Q26:如何定位低效SQL?

可以通过两种方式来定位执行效率较低的SQL语句。- -种是通过慢查询日志定位，可以通过慢查询日志
定位那些已经执行完毕的SQL语句。另-种是使用SHOW PROCESSLIST查询，慢查询日志在查询结束
以后才记录，所以在应用反应执行效率出现问题的时候查询慢查询日志不能定位问题，此时可以使用
SHOW PROCESSLIST命令查看当前MySQL正在进行的线程，包括线程的状态、是否锁表等，可以实
时查看SQL的执行情况，同时对一些锁表操作进行优化。找到执行效率低的SQL语句后，就可以通过
SHOW PROFILE、EXPLAIN 或trace等丰富来继续优化语句。



### ❓Q27: SHOW PROFILE的作用?

通过SHOW PROFILE可以分析SQL语句性能消耗，例如查询到SQL会执行多少时间，并显示CPU、
内存使用量，执行过程中系统锁及表锁的花费时间等信息。例如SHOW PROFILE CPU/MEMORY/BLOCK
IO FOR QUERY N分别查询id为N的SQL语句的CPU、内存以及I0的消耗情况。



### ❓Q28: trace是干什么的?

从MySQL5.6开始，可以通过trace文件进一步获取优化器是是如何选择执行计划的，在使用时需要先
打开设置，然后执行一-次SQL,最后查看information_ schema.optimizer. _trace 表而都内容，该表为
联合i表，只能在当前会话进行查询，每次查询后返回的都是最近一次执行的SQL语句。

### ❓Q29: EXPLAIN 的字段有哪些，具有什么含义?

执行计划是SQL调优的一个重要依据，可以通过EXPLAIN命令查看SQL语句的执行计划，如果作用在
表上，那么该命令相当于DESC。EXPLAIN 的指标及含义如下:



### ❓Q30:有哪些优化SQL的策略?

优化COUNT查询
COUNT是一个特殊的函数，它可以统计某个列值的数量，在统计列值时要求列值是非空的，不会统计
NULL值。如果在COUNT中指定了列或列的表达式，则统计的就是这个表达式有值的结果数，而不是
NULL.
COUNT的另一一个作用是统计结果集的行数，当MySQL确定括号内的表达式不可能为NULL时，实际
上就是在统计行数。当使用COUNT(*)时, *不会扩展成所有列，它会忽略所有的列而直接统计所有的
行数。
某些业务场景并不要求完全精确的COUNT值，此时可以使用近似值来代替，EXPLAIN 出来的优化器估
算的行数就是一个不错的近似值，因为执行EXPLAIN并不需要真正地执行查询。
遇常来说COUNT都需要扫描大量的行才能获取精确的结果，因此很难优化。在MySQL层还能做的就
只有覆盖扫描了，如果还不够就需要修改应用的架构，可以增加汇总表或者外部缓存系统。
优化关联查询
确保ON或USING子句中的列上有索引，在创建索引时就要考虑到关联的顺序。
确保任何GROUP BY和ORDER BY的表达式只涉及到-个表中的列，这样MySQL才有可能使用索引
来优化这个过程。
在MySQL 5.5及以下版本尽量避免子查询，可以用关联查询代替，因为执行器会先执行外部的SQL再
执行内部的SQL.
优化GROUP BY
如果没有通过ORDER BY子旬显式指定要排序的列，当查询使用GROUP BY时，结果*** 自动按照分组
的字段进行排序，如果不关心结果集的顺序，可以使用ORDER BY NULL禁止排序。
优化LIMIT分页
在偏移量非常大的时候，需要查询很多条数据再舍弃，这样的代价非常高。要优化这种查询，要么是在
页面中限制分页的数量，要么是优化大偏移量的性能。最简单的办法是尽可能地使用覆盖索引扫描，而
不是查询所有的列，然后根据需要做-次关联操作再返回所需的列。
还有一种方法是从上一-次取数据的位置开始扫描，这样就可以避免使用OFFSET.其他优化方法还包括
使用预先计算的汇总表，或者关联到一个冗余表，冗余表只包含主键列和需要做排序的数据列。
优化UNION查询
MySQL通过创建并填充临时表的方式来执行UNION查询，除非确实需要服务器消除重复的行，否则一
定要使用UNION ALL,如果没有ALL关键字, MySQL 会给临时表加上DISTINCT选项，这会导致对整
个临时表的数据做唯一性检查，这样做的代价非常高。
使用用户自定义变量
在查询中混合使用过程化和关系化逻辑的时候，自定义变量可能会非常有用。用户自定义变量是一个用
来存储内容的临时容器，在连接MySQL的整个过程中都存在，可以在任何可以使用表达式的地方使用
自定义变量。例如可以使用变量来避免重复查询刚刚更新过的数据、统计更新和插入的数量等。
优化INSERT
需要对一-张表插入很多行数据时，应该尽量使用一次性插入多个值的INSERT语句，这种方式将缩减客
户端与数据库之间的连接、关闭等消耗,效率比多条插入单个值的INSERT语句高。也可以关闭事务的
自动提交，在插入完数据后提交。当播入的数据是按主键的顺序插入时，效率更高。



### ❓Q31: MySQL 主从复制的作用?

复制解决的基本问题是让一台服务器的数据与其他服务器保持同步，一台主库的数据可以同步到多台备
库上，备库本身也可以被配置成另外一台服务器的主库。主库和备库之间可以有多种不同的组合方式。
MySQL支持两种复制方式:基于行的复制和基于语句的复制，基于语句的复制也称为逻辑复制，从
MySQL 3.23版本就已存在，基于行的复制方式在5.1版本才被加进来。这两种方式都是通过在主库.上
记录二进制日志、在备库重放日志的方式来实现异步的数据复制。因此同一时刻备库的数据可能与主库
存在不一致，并且无法包装主备之间的延迟。
MySQL复制大部分是向后兼容的，新版本的服务器可以作为老版本服务器的备库，但是老版本不能作
为新版本服务器的备库，因为它可能无法解析新版本所用的新特性或语法，另外所使用的二进制文件格
式也可能不同。
复制解决的问题:数据分布、负载均衡、备份、高可用性和故障切换、MySQL升级测试。



### ❓Q32: MySQL 主从复制的步骤?

①在主库上把数据更改记录到二进制日志中。②备库将主库的日志复制到自己的中继日志中。③备库
读取中继日志中的事件，将其重放到备库数据之上。
第一步是在主库.上记录二进制日志，每次准备提交事务完成数据更新前，主库将数据更新的事件记录到
二进制日志中。MySQL会按事务提交的顺序而非每条语句的执行顺序来记录二进制日志，在记录二进
制日志后，主库会告诉存储引擎可以提交事务了。
下一步，备库将主库的二进制日志复制到其本地的中继日志中。备库首先会启动一个工作的I0线程,
I0线程跟主库建立一个普通的客户端连接，然后在主库.上启动一个特殊的二进制转储线程，这个线程会
读取主库.上二进制日志中的事件。它不会对事件进行轮询。如果该线程追赶上了主库将进入睡眠状态,
直到主库发送信号量通知其有新的事件产生时才会被唤醒，备库10线程会将接收到的事件记录到中继
日志中。
备库的SQL线程执行最后一步，该线程从中继日志中读取事件并在备库执行，从而实现备库数据的更
新。当SQL线程追赶上10线程时，中继日志通常已经在系统缓存中，所以中继日志的开销很低。SQL
线程执行的时间也可以通过配置选项来决定是否写入其自己的二进制日志中。