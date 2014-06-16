# CAP定理

<sup>[1](#ref_1)</sup>CAP定理, 又称布鲁斯定理, 它指出一个分布式计算机系统无法同时满足下面三点：
* 一致性（所有节点在同一时间返回相同的数据）
* 可用性 （保证对每个客户端请求无论成功与否都有响应）
* 分区容忍性（系统中任意信息的丢失或失败不会影响系统的继续运行）

根据该定理，任何一个分布式系统只能满足以上三点中的两点而不可能满足全部满足三点。

## Cassandra & CAP

Cassandra一般被认为是满足AP的系统，也就是说Cassandra认为可用性和分区容忍性比一致性更重要。不过Cassandra可以通过调节replication factor和consistency level来满足C。

## 参考
1. <a name="ref_1"></a>http://en.wikipedia.org/wiki/CAP_theorem
