# redo log & undo log
* ## undo log 
  * undo log通常用于事务的回滚操作，用来保证事务的原子性。
    * 每当发生数据修改操作时（update、insert、delete），关于当前修改操作的相反操作会被记录到undo log中，通常用于在回滚时将数据回复到修改之前的值。
    * undo log默认被记录到mysql的表空间中，因而对undo log进行追加时，对表中页数据进行修改时也会产生redo log，对undo log的追加会通过fsync操作持久化到redo log中。这样即使在一个事务尚未被提交或是回滚之前，mysql服务器崩溃，下次重启时，也可以通过redo log恢复对数据的修改和undo log的内容，回滚事务时也能将数据恢复到崩溃前尚未被事务修改的状态。