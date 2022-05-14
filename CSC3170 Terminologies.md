# CSC3170 Terminologies

**A prime attribute** is a member of some candidate key.

**A nonprime attribute** is not a prime attribute — that is, it is  not a member of any candidate key

K is a **superkey** of  R if values for K are sufficient to identify a unique tuple of each possible relation r(R).

Superkey K is a **candidate key** if K is minimal.

One of the candidate keys is selected to be the **primary key**.



事物 ACID 特性

Atomic 原子性： 数据库事物是一个整体，要么提交全部commit成功，要么全部失败回滚rollback，不可分割

Consistency 一致性：事务总是从一个状态变更成另一个状态。

Isolation 隔离性：事务之间彼此互不影响，一个事务提交之前，对其他事务是不可见的。

Durability 持久性：一个事务一旦提交成功，就会永久性地存储在数据库中。



Mysql 四种隔离级别：

1、未提交读 READ UNCOMMITTED: 一个事务在提交之前，对其他事务是可见的。有可能遇到“脏读”

2、提交读 READ COMMITTED: 事务在提交之前，对其他事务是不可见的。存在“不可重复读”（两次查询的间隙，可能有事务提交了修改《UPDATE》）的问题，但解决了“脏读”问题。

3、可重复读 REPEATABLE READ: 在同一事务中多次读取的数据是一样的。解决了脏读和不可重复读的问题，存在“幻读”（两次查询的间隙，可能有事务插入《INSERT》或删除《DELETE》了新的记录）

4、可串行化SERIALIZABLE: 强制事务串行化执行。即一个事务一个事务挨个执行，可以解决以上所有问题。



锁：

1、共享锁/读锁：互不阻塞，优先级低

2、排他锁/写锁：阻塞其他锁，优先级高，即确保一个事务写入时不受其他事务的影响。

3、锁粒度：锁定的数据量越小（粒度越小），并发程度越高，但相应的加锁、检测锁、释放锁所用的系统开销也随之变大。

4、策略锁：

​	1、表锁：锁住整张表，读锁互不阻塞，写锁阻塞其他所有读写锁（同一张表）。开销最小。

​	2、行级锁：对每一行数据（记录）加锁，开销大，并发能力高。



死锁：与OS死锁相似，多个事务互相持有对方所有要申请资源的锁不释放，造成环路死锁。

Mysql InnoDB检测到死锁循环依赖后，回滚持有最少行级锁的事务。



InnoDB默认的隔离级别是RR（REPEATABLE READ），RR解决脏读、不可重复读、幻读等问题，使用的是MVCC。MVCC全称Multi-Version Concurrency Control，即多版本的并发控制协议。它最大的优点是读不加锁，因此读写不冲突，并发性能好。InnoDB实现MVCC，多个版本的数据可以共存，主要基于以下技术及数据结构：

1. 隐藏列：InnoDB中每行数据都有隐藏列，隐藏列中包含了本行数据的事务id、指向undo log的指针等。
2. 基于undo log的版本链：每行数据的隐藏列中包含了指向undo log的指针，而每条undo log也会指向更早版本的undo log，从而形成一条版本链。
3. ReadView：通过隐藏列和版本链，MySQL可以将数据恢复到指定版本。但是具体要恢复到哪个版本，则需要根据ReadView来确定。所谓ReadView，是指事务（记做事务A）在某一时刻给整个事务系统（trx_sys）打快照，之后再进行读操作时，会将读取到的数据中的事务id与trx_sys快照比较，从而判断数据对该ReadView是否可见，即对事务A是否可见。



MySQL的InnoDB引擎，在默认的REPEATABLE READ的隔离级别下，实现了可重复读，同时也解决了幻读问题。它使用Next-Key Lock算法实现了行锁，并且不允许读取已提交的数据，所以解决了不可重复读的问题。另外，该算法包含了间隙锁，会锁定一个范围，因此也解决了幻读的问题。
