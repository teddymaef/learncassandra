# 分区器（Partitioners）

<sup>[1](#ref_1)</sup>分区器决定了数据如何在集群内被分发。简单来说，一个就是一个用来计算partition key哈希值的一个哈希函数。每一行数据，由partition key的值唯一标识，并且根据partition key的哈希值决定如何在集群内被分发。

一个集群，有一个全局唯一的分区器的配置。Cassandra的默认的分区器是Murmur3Partitioner，它一般能满足绝大多数情况的需要。

**Cassandra提供了一下这些分区器：**

* Murmur3Partitioner（默认）：基于MurmurHash哈希算法
* RandomPartitioner：基于MD5哈希算法
* ByteOrderedPartitioner：根据partition key的bytes进行有序分区

Murmur3Partitioner和RandomPartitioner都使用哈希值来平均分配一个column family的数据到集群上的所有节点。

<sup>[2](#ref_2)</sup>ByteOrderedPartitioner用于基于partition key的bytes进行有序分区。它允许按照partition key进行条件范围查询。也就是说，可以像关系型数据库的主键那样，通过游标，有序便利所有的partition key。例如，如果使用用户名作为partition key，可以范围查询所有名字在Jake和Jeo之间的用户。这样的条件范围查询，在使用随机分区器时，是不允许的。

尽管范围查询听上去很有吸引力，**并不推荐使用有序的分区器，因为：**

* 负载均衡更困难
* 顺序的分区容易造成热点（hotspot）
* 多个表的数据的负载均衡不平均

## 参考

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architecturePartitionerAbout_c.html#concept_ds_dwv_npf_fk
2. <a name="ref_2"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architecturePartitionerBOP_c.html
