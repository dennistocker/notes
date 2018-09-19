# Berkeley DB

## Getting Started with Berkeley DB

1. 如果DB支持duplicate records，使用handle智能获取duplicate records的第一条记录，可以使用cursor来获取所有记录。
2. access methods的分类
    1. Btree：支持复合类型key，支持duplicate records
    2. Hash: 支持复合类型key，支持duplicate records
    3. Queue：logical record number作为key，定长，尾部插入，头部删除，快速存取，支持record level locking
    4. Recno：logical record number作为key，可定长可不定长
3. access methods的选取
    1. key类型：复合类型key，只能选取Btree或Hash
    2. Btree和Hash
        1. DB比较小时，或者读取有序时，选用btree
        2. DB特别大时，选用Hash（Btree key有序，Hash存储空间小）
        3. Queue和Recno： 高并发，data定长选用queue，data可变长时选用Recno
4. close DB
    1. 在关闭DB前应该保证不再使用该DB（例如，关闭该DB的所有cursor）
    2. DB的所有handle都被关闭之后，默认会将缓存sync到磁盘上，也可以主动调用DB->sync()来强制sync缓冲
5. delete data
    1. 如果DB支持duplicate records，使用DB->del()会删除所有记录，可以使用cursor类删除某一条指定记录
6. replace data
    1. 即使使用cursor修改数据时，也不能修改key，这时的key值总是被忽略
    2. 如果替换的数据时sorted duplicate records，所提供的compare function中计算出来的新旧值必须相等
7. Secondary database
    1. 使用Secondary database时需要提供一个callback来产生Secondary database的key，一般从primary db的key或者data来生成这个key
    2. Secondary db只有在callback返回0时才会添加数据
    3. 更新primary db时如果有必要会自动更新Secondary db。除了删除数据之外，不能向一个Secondary db中发起write操作
    4. 从Secondary db中读取数据时，会自动返回primary db中的data，如果需要开源另外返回primary db的key
    5. Secondary db一般会支持duplicate records
    6. 可以从primary db的一条记录中产生Secondary db的多条记录，在callback中产生多个Secondary db的key
    7. 关闭primary db之前要关闭Secondary db，即使他们在同一个线程中进行
    8. 多个Secondary db的联合查询使用Join cursors
8. BDB的设置
    1. page size的设置：一般最好和系统IO block size
    2. cache size的设置


## Getting Started with Berkeley DB Transaction Processing

1. 数据库的ACID特性
    1. atomicity: 原子性
    2. consistency：一致性
    3. isolation：独立性
    4. durability：持久性
2. BDB support different isolation levels, you can configure your application to see uncommitted read, which means that one transaction can see data that has been modified but not yes commmitted by another transaction.
3. 要支持transaction，在打开DB_ENV时应制定TXN，LOCK，LOG，MPOOL的flag，and you must open the database within a transaction.（打开DB时指定DB_AUTO_COMMIT）
4. 在关闭transaction之前关闭cursor，在关闭db之前关闭所有的transaction，在关闭primary db之前关闭所有的Secondary db，在关闭DB ENV之前关闭所有的db
5. Any transaction handle that has been commmitted or aborted can no longer used by your application.
6.  Neve have more than on active transaction in your thread at a time.
7. Thread-Safe
    1. DB_ENV, DB can be tread-safe so long as the DB_THREAD flag is provided to the open() method.
    2. DBC, DB_TXN are not thread-safe, access must be serialized by the application across threads of control.
