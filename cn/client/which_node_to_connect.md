# 连接哪个节点？

一个客户端请求会连接集群上的哪个节点由客户端driver的配置决定。

例如，如果使用[Datastax drivers](http://www.datastax.com/download)，主要由下面这两个客户端配置决定连接到哪个节点：

* Contact points
* Load balancing policies

## Contact Points

Contact points设置的值是一个或者多个节点IP的列表。当driver在客户端创建Cluster实例时，driver首先会按照指定的顺序尝试连接contact points中的节点地址。如果第一个节点连接失败，则尝试连接第二个。只要任何一个节点连接成功，则停止尝试连接后面的节点地址。

为什么此时不需要连接所有的节点，是因为集群中的任何一个节点都保存了集群上所有节点的元数据，也就是说，只要连上一个节点，driver就能得到所有其他节点的配置信息。接下来，driver会使用获得到所有节点的配置信息构造连接池（构造连接池的时候，driver会连接所有的节点，但是，不是根据contact points中指定的节点地址，而是根据从服务端获得的所有节点的元数据）。这也意味着，contact points设置中，不必要指定集群中所有节点的地址。最佳实践是，在contact points中设置相对于客户端响应最快的所有节点的地址。

## Load Balancing Policies

默认情况下，一个客户端的Cluster实例管理了集群中所有节点的连接，并且，将客户端请求随机连接到任意节点。不过，在某些情况下，尤其是多数据中心时，默认行为的性能可能不是最优的。

例如，如果集群包含两个数据中心，一个在中国，一个在美国。如果客户端在中国，那么，应该避免直接连接美国的节点，不然太慢。

Cluster实例的Load balancing policies设置，决定了，客户端请求如何分配节点的连接。

Datastax driver中最常用的内置Load balancing policies是DCAwareLoadBalancePolicy。它可以指定客户端请求只连接到指定的数据中心的节点。

当然，你也可以实现自定义的load balanacing policies，实现自定义的客户端连接分配。

## Coodinator

<sup>[1](#ref_1)</sup>当客户端读写请求连接到某个节点时，这个被连接的节点被称作本次请求的coordinator。

一个coordinator的职责是客户端请求和真正拥有数据的节点之间的代理（Proxy）。coordinator根据服务端的[分区器配置](../replication/partitioners.html)和[replication策略设置](../replication/replication_strategies.html)来决定应该和集群中的哪些节点通信。

## 参考

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureClientRequestsAbout_c.html
