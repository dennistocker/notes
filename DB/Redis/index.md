# Redis

## Redis 监控

redis-cli info
1. 内存使用
    1. used_memory
    2. used_memory_peak
2. 持久化
    1. rdb_last_save_time
    2. rdb_changes_since_last_save
3. 主从复制
    1. master_link_status
        1. up：正常
        2. down：错误
4. fork性能
    1. lastest_fork_usec: 最近一次fork所用时间
5. 配置一致
    1. config set的配置不会同步到配置文件
6. 慢日志
    1. slowlog
7. 监控服务
    1. redis自带sentinel
    2. redis live：使用info和monitor，dashboard
    3. redis faina：query analysis，使用monitor
    4. redmon：使用info和monitor，dashboard
8. 数据分布
    1. redis自带redis-sample
    2. redis-audit
    3. redis-rdb-tools

## 常用NoSQL

* Redis
* MongoDB
* Neo4j
* tokyo cabinet
* Cassandra
* Sophia
* LevelDB
* Berkeley DB
* BeanDB（豆瓣）
* bitcask（日志型的数据文件，基于hash表的索引数据，hint file）
