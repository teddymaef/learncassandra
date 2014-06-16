# Cassandra能做什么？

Cassandta采用去中心化的架构，也就是说所有的节点都是平等的。 Cassandra自动化地在环或者说数据库集群的所有节点之间进行数据分发。开发人员或者管理员无法，也没有必要通过程序来控制数据分发，因为集群里所有节点的数据分区对用户是透明的。

Cassandra提供了内建的可定制的replication机制，在整个集群的节点上保存冗余的数据副本。也就是说，如果集群上的任意节点发生故障，集群上的其他节点还有一个或者多个副本。Replication可以设置成只在本地的数据中心之间进行，也可以设置成多个数据中心之间或者多个云端区域之间。

<sup>[1](#ref_1)</sup>Cassandra具有线性扩展性，也就是说可以简单的在线添加新节点从而增加集群的处理能力。例如，如果2个节点可以每秒处理100,000个事务，4个节点就能每秒处理200,000个事务，而8个节点就能每秒处理400,000个事务：

![Figure 1](../assets/cassandra.png)

## 参考
1. <a name="ref_1"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/gettingStartedCassandraIntro.html
