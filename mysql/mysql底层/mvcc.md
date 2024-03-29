# 多版本并发控制
* ## 多版本并发控制解决的问题：
    * 在多个事务并发访问资源时，可能会发生并发安全问题。为了解决多个事务同时对资进行读写的问题，可以采用如下解决方案：
      * 多事务同时对资源进行读取：并发安全
      * 多事务同时对资源进行写操作：非并发安全，可以对写操作进行加锁来解决并发安全问题
      * 多事务同时对资源进行读和写操作：非并发安全
        1. 对读操作和写操作都加锁：性能较差
        2. 通过MVCC机制来解决并发读写的问题，对写操作加锁，并用MVCC机制来使读操作读到先前的值
* ## 多版本并发控的概念：
  * MVCC通过数据库行的多个版本管理来实现数据库的并发控制。当一个写事务正在更新数据行的值时，通过MVCC机制，可以在另一个事物对数据行进行读操作时读取到被写事务更新之前的值。
  * 通过MVCC机制，可以更好的解决事务在读-写操作同时发生时的性能问题。MVCC可以避免在写事务时另一个读事务必须等待当前写事务释放排他锁，而是可以通过MVCC读取资源被写事务修改之前的值。

* ## mysql中读操作的种类：
  * 当前读：读取数据库中当前的值，为了解决并发安全问题，需要对读操作进行加锁操作，可以尝试加排他锁或者共享锁，当当前事务对资源进行写操作时，读事务会阻塞直到写事务释放锁：
    ```mysql
    # 为select语句加上共享锁
    select * from user where... lock in share mode

    # 为select语句加上排他锁
    select * from user where... for update
    ```
  * 快照读：mysql中默认select语句的读取方式，当当前事务对资源进行读操作时，如果另一个事务正在对资源进行写操作，那么读操作并不会阻塞而是会读取资源被写事务修改之前的值
  
* ## MVCC原理
  * 隐藏字段：对于使用innodb存储引擎的表，其聚簇索引包含两个隐藏字段：
    1. trx_id：每个事务对每条记录进行更改时，该trx_id字段都会记载最后一次修改该行记录的事务id
    2. roll_pointer：指向undo log中关于修改该条数据的记录
  * ReadView：ReadView是MVCC机制中事务对数据进行快照读时产生的一个读视图。
    * ReadView原理：
      * 当事务开启时，会产生当前数据库系统的一个快照，innodb为每个事务构造了一个数组，用来维护当前系统中活跃事务的ID（活跃事务为当前开启了但是尚未提交的事务id）
      * ReadView中保存了如下信息：
        * creator_trx_id：创建当前ReadView的事务id
        * trx_ids：记录创建失误时当前mysql系统中活跃的事务id集合(已经开始但是还没有被提交的事务id)
        * up_limit_id：trx_ids中最小的事务id
        * low_limit_id：表示生成ReadView中当前系统应该分配给下一个事务的id
      * MVCC仅针对读已提交和可重复读的情况，在读已提交的情况下，事务中每执行一次select操作，ReadView都会重复生成；而在可重复读的隔离级别下，事务仅仅会在第一次select操作时生成ReadView
      * MVCC细节：
        * 当select语句想要对一条记录中的数据进行读取时，首先会查看记录的trx_id是是否对当前事务的读操作是可见的，判断事务是否可见的标准如下所示：
          * 如果记录的trx_id大于low_limit_id，那么说明在创建ReadView时对记录进行修改的事务还没有被创建，当然修改对当前读事务来说是不可见的
          * 如果trx_id小于up_limit_id，那么说明对该记录进行修改的事务id小于创建ReadView时最小的活跃事务id，在创建ReadView时修改记录的事物已经被提交，修改对当前事务来说可见
          * 如果trx_id位于up_limit_id和low_limit_id之间，那么：
            * trx_id如果与trx_ids中保存的某个活跃事务id相同，那么说明在创建ReadView时修改事务的id尚未被提交，修改对当前失误不可见
            * 如果trx_id与trx_ids中每个活跃事物id都不相同，那么修改事务在创建ReadView事物之时已经被提交，修改对当前事务可见
        * 根据上述规则，如果想要读取的记录trx_id对当前事务来说可见，那么获取该事务id所对应的数据值；如果trx_id对当前ReadView来说不可见，那么沿着roll_pointer沿着undo log向前寻找，知道找到对当前ReadView可见的事务id；如果undo log中所有的事务id对当前ReadView来说都不可见，那么对当前事物来说数据表中该条记录并不可见

* ## MVCC与幻读问题
  * MVCC仅在读已提交和可重复读的情况下起作用，而关于幻读问题，MVCC仅在可重复读的隔离级别下解决。
    *  在可重复读的隔离级别下,ReadView仅仅在第一次select语句时生成，故而在同一事务中多次读取之间，其他事务插入了新数据，那么该新数据对应的trx_id在readView创建时应该处于活跃或者未创建的状态，故而对ReadView所对应事物来说，即使其他事务插入了新数据，那么新插入的数据也不可见。
    *  而在读已提交的隔离级别下，ReadView在每次读操作的情况下都会被创建，故而在两次读操作之间，如果新的数据被插入，那么新插入的数据对后一次读操作创建的ReadView来说是已经提交的数据，是可见的。故而MVCC在读已提交的隔离级别下并不能够解决幻读的问题。