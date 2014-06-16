# 并发控制

Cassandra并不支持类似关系型数据库的基于回滚和锁机制实现ACID的事务，Cassandra的类似事务的一致性，通过持久化事务，[可调节的一致性](../replication/turnable_consistency.html)和轻量级事务来实现。

## 轻量级事务

<sup>[1](#ref_1)</sup>尽管持久化事务和[可调节的一致性](../replication/turnable_consistency.html)已经可以满足许多用例，有一些情况，确实需要更强的原子控制。轻量级事务（lightweight transaction），又称（compare and set），使用线性化（linearizable）的一致性来满足需求。

例如，如果想确保一个用户帐号插入的唯一性，可以使用IF NOT EXISTS语句：

```SQL
INSERT INTO customer_account (customerID, customer_email)
    VALUES (‘LauraS’, ‘lauras@gmail.com’)
    IF NOT EXISTS;
```

UPDATE也可以使用IF语句来比较一个或者多个字段的值：

```SQL
UPDATE customer_account
    SET customer_email=’laurass@gmail.com’
    IF customer_email=’lauras@gmail.com’;
```
在后台，Cassandra需要在发起事务的节点和集群中的其它节点之间有4次应答实现一个事务。所以，轻量级事务，对性能有影响。因此，只在绝对必要时，才应该使用轻量级事务，其他情况，一般都能通过[可调节的一致性](../replication/turnable_consistency.html)实现。

注意，[SERIAL一致性级别](../replication/turnable_consistency.html) 允许读取当前数据，包括uncommitted的数据。

## 原子化（Atomicity）

<sup>[2](#ref_2)</sup>在Cassandra里，写操作在行级别是原子化的，，也就是说，一次插入或者更新一行的多个列是一个原子操作。但是，因为Cassandra不支持类似关系型数据库的事务，Cassandra的写操作请求有可能返回失败，但实际上，数据已经写到某个replica了。

例如，如果写操作使用QUORUM级别，replication factor值为3， Cassandra需要写集群中所有节点，并且等待至少两个节点返回成功，如果某一个节点写失败，但是其他节点写成功，Cassandra会返回写失败，但是，那些写成功的节点的数据不会被回滚。

Cassandra使用timestamp来决定每一列的数据是否最新。timestamp是由客户端程序提供的。timestamp越近，代表值越新，如果有多个客户端同时更新一列数据，timestamp值最新的数据最终会被同步到所有节点。

## 持久性（Durability）

<sup>[3](#ref_3)</sup>Cassandra的写操作是durable的。对每一个节点的所有写操作，在返回成功之前，一定会确保保存到内存中的memtable和磁盘上的commit log。如果服务器在内存中的memtable数据还没保存到磁盘上之前宕机，等服务器重启时，commit log中的数据会被用来自动恢复重建memtable中的数据，所以，不会有数据丢失。

## 参考

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_ltwt_transaction_c.html
2. <a name="ref_2"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_atomicity_c.html
3. <a name="ref_3"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_durability_c.html
