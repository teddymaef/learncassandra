# What Can Cassandra Do?

The architecture of Cassandra is “masterless”, meaning all nodes are the same. Cassandra provides automatic data distribution across all nodes that participate in a “ring” or database cluster. There is nothing programmatic that a developer or administrator needs to do or code to distribute data across a cluster because data is transparently partitioned across all nodes in a cluster.

Cassandra also provides built-in and customizable replication, which stores redundant copies of data across nodes that participate in a Cassandra ring. This means that if any node in a cluster goes down, one or more copies of that node’s data is available on other machines in the cluster. Replication can be configured to work across one data center, many data centers, and multiple cloud availability zones.

<sup>[1](#ref_1)</sup>Cassandra supplies linear scalability, meaning that capacity may be easily added simply by adding new nodes online. For example, if 2 nodes can handle 100,000 transactions per second, 4 nodes will support 200,000 transactions/sec and 8 nodes will tackle 400,000 transactions/sec:

![Figure 1](../assets/cassandra.png)

## References
1. <a name="ref_1"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/gettingStartedCassandraIntro.html
