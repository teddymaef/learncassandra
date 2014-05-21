# Replication Strategies

<sup>[1](#ref_1)</sup>Cassandra stores replicas on multiple nodes to ensure reliability and fault tolerance. A replication strategy determines the nodes where replicas are placed.

The total number of replicas across the cluster is referred to as the replication factor. A replication factor of 1 means that there is only one copy of each row on one node. A replication factor of 2 means two copies of each row, where each copy is on a different node. All replicas are equally important; there is no primary or master replica. As a general rule, the replication factor should not exceed the number of nodes in the cluster. However, you can increase the replication factor and then add the desired number of nodes later. When replication factor exceeds the number of nodes, writes are rejected, but reads are served as long as the desired consistency level can be met.

**Two replication strategies are available:**
* SimpleStrategy: Use for a single data center only. If you ever intend more than one data center, use the NetworkTopologyStrategy
* NetworkTopologyStrategy: Highly recommended for most deployments because it is much easier to expand to multiple data centers when required by future expansion, it specifies how many replicas you want in each data center

**There are the two primary considerations when deciding how many replicas to configure in each data center:**

* Being able to satisfy reads locally without incurring cross data-center latency
* Failure scenarios

**The two most common ways to configure multiple data center clusters are:**

* Two replicas in each data center: This configuration tolerates the failure of a single node per replication group and still allows local reads at a consistency level of ONE
* Three replicas in each data center: This configuration tolerates either the failure of a one node per replication group at a strong consistency level of LOCAL_QUORUM or multiple node failures per data center using consistency level ONE

## References

1. <a name="ref_1"><a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html
