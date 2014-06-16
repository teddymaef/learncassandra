# 数据Replication

这一章，我们将介绍数据如何在Cassandra的节点之间进行分发和replicate。

<sup>[1](#ref_1)</sup>Cassandra是一个点对点系统，所以，创建数据副本和数据的分发都是在一组节点之间互相进行的。

数据是按照column family（table）进行组织。一行数据，通过这一行的partition key唯一标识。 Cassandra使用一致性哈希（consistent hashing）机制在集群里分发数据。每个集群，配置了一个分区器（partitioner）来对每个partition key进行哈希运算。哈希运算的结果决定了，这一行数据应该被保存到哪个节点。

一行数据的一个副本被称作一个replica。数据第一次被保存，也被称作创建数据的第一个replica。

## 参考

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeAbout_c.html
