# redo log 介绍

redo log 是 InnoDB 存储引擎独有的一个日志文件，用于高效的保证数据的持久性。当有数据页被修改时，该数据也就变成了脏页(dirty page), 但是 InnoDB 不会立刻将该数据页的数据写会磁盘，而是会将 Buffer Pool 中的数据页添加到 Flush List 中，并在将来的某个时刻才将脏页的数据写入到磁盘中。但是这种机制存在一个问题：如果 MySQL 宕机了，就会丢失未写入磁盘的脏页中对数据的修改。InnoDb 为了解决这种情况，提供了 redo log.