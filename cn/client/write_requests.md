# 写请求

<sup>[1](#ref_1)</sup>如果所有的节点在同一个数据中心，coordinator会将写请求发到所有的replica。如果所有的replica节点可用，无论客户端指定的[consistency level](../replication/turnable_consistency.html)是什么，每一个replica都会处理写请求。写操作的consistency level只决定了coordinator在通知客户端写操作成功之前，多少个replica节点必须返回成功。

例如，一个单数据中心集群有10个节点，replication factor值为3，一个写请求，会被发给所有的三个replica节点。如果，客户端指定的consistency level是ONE，那么，只要任何一个replica节点通知coordinator写成功，coordinator就会返回客户端写成功。一个节点返回写成功，表示，写数据已经保存到这个节点的内存中的memtable和磁盘上的commit log。

![Figure 1](../assets/write_access.png)

<sup>[2](#ref_2)</sup>如果是多数据中心部署，Cassandra为了优化写性能，对于远程数据中心，会在每个远程数据中心的节点中，选择一个作为远程数据中心中的coordinator。因此，和远程数据中心里的replica nodes的通信，本地数据中心的coordinator，只需要和每个远程数据中心的这一个coordinator节点通信。

如果使用consistency level ONE或者LOCAL_QUORUM，coordinator只需要和本地数据中心的节点通信。因此，可以避免，跨数据中心的的通信影响写操作性能。

![Figure 2](../assets/write_access_multidc.png)

## 参考

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureClientRequestsWrite.html
2. <a name="ref_2"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureClientRequestsMultiDCWrites_c.html
