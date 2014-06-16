# Replication策略

<sup>[1](#ref_1)</sup>Cassandra在多个节点存储replica来确保可靠性和容错性。Replication策略决定了replica保存到哪些节点。

replica的总数由replication factor控制。replication factor 1表示一行数据，只会有一个replica，被保存在唯一一个节点上。replication factor 2表示一行数据有两个副本，每个副本保存在不同的节点上。所有的replica同等重要；没有主次replica之分。一般的规则是，replication factor不应该超过集群里的节点数量。不过，可以先增大replication factor，再添加更多节点。如果replication factor配置超过节点数量，写操作会被拒绝。读操作不受影响。

**Cassandra内置了两种类型的replication策略：**
* SimpleStrategy：用于单个数据中心。如果有可能以后会有多个数据中心，用NetworkTopologyStrategy
* NetworkTopologyStrategy：对绝大多数部署方式，都强烈推荐该策略。因为今后的扩展更容易

**关于每个数据中心，配置几个replica，一般主要考虑以下两个因素：**

* 保证读操作没有跨数据中心的延时损耗
* 如何处理硬件故障的情形

**对于多数据中心，最常用的两种配置replica的策略是：**

* 每个数据中心2个replica：基于这种配置，对于每个数据中心，即使单个节点故障，还是能够支持consistency level ONE的本地读
* 每个数据中心3个replica：基于这种配置，对于每个数据中心，即使单个节点故障，还是能够支持consistency level LOCAL_QUORUM的本地读；即使2两个节点故障，还是能够支持consistency level ONE的本地读

## 参考

1. <a name="ref_1"><a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html
