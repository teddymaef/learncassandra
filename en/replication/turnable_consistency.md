# Turnable Consistency

<sup>[1](#ref_1)</sup>Consistency refers to how up-to-date and synchronized a row of Cassandra data is on all of its replicas. Cassandra extends the concept of [eventual consistency](http://en.wikipedia.org/wiki/Eventual_consistency) by offering tunable consistency for any given read or write operation, the client application decides how consistent the requested data should be.

## Write Consistency Levels

The consistency level specifies the number of replicas on which the write must succeed before returning an acknowledgment to the client application.

| Level | Description | Usage |
| -- | -- | -- |
| ANY | A write must be written to at least one node. If all replica nodes for the given row key are down, the write can still succeed after a [hinted handoff](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_about_hh_c.html#concept_ds_ifg_jqx_zj) has been written. If all replica nodes are down at write time, an ANY write is not readable until the replica nodes for that row have recovered. | Provides low latency and a guarantee that a write never fails. Delivers the lowest consistency and highest availability compared to other levels. |
| ALL | A write must be written to the commit log and memory table on all replica nodes in the cluster for that row. | Provides the highest consistency and the lowest availability of any other level. |
| EACH_QUORUM | A write must be written to the commit log and memory table on a quorum of replica nodes in all data centers. | Used in multiple data center clusters to strictly maintain consistency at the same level in each data center. For example, choose this level if you want a read to fail when a data center is down and the QUORUM cannot be reached on that data center. |
| LOCAL_ONE | A write must be sent to, and successfully acknowledged by, at least one replica node in the local datacenter. | In a multiple data center clusters, a consistency level of ONE is often desirable, but cross-DC traffic is not. LOCAL_ONE accomplishes this. For security and quality reasons, you can use this consistency level in an offline datacenter to prevent automatic connection to online nodes in other data centers if an offline node goes down. |
| LOCAL_QUORUM | A write must be written to the commit log and memory table on a quorum of replica nodes in the same data center as the coordinator node. Avoids latency of inter-data center communication. | Used in multiple data center clusters with a rack-aware replica placement strategy ( NetworkTopologyStrategy) and a properly configured snitch. Fails when using SimpleStrategy. Use to maintain consistency at locally (within the single data center). |
| LOCAL_SERIAL | A write must be written conditionally to the commit log and memory table on a quorum of replica nodes in the same data center. | Used to achieve [linearizable consistency](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_tunable_consistency_c.html#concept_ds_f4h_hwx_zj) for lightweight transactions by preventing unconditional updates. |
| ONE | A write must be written to the commit log and memory table of at least one replica node. | Satisfies the needs of most users because consistency requirements are not stringent. The replica node closest to the coordinator node that received the request serves the request |

**Caution:**

Even at consistency level ONE or LOCAL_QUORUM, the write is still sent to all replicas for the written key, even replicas in other data centers. The consistency level just determines how many replicas are required to respond that they received the write.

## Read Consistency Levels

| Level | Description | Usage |
| -- | -- | -- |
| ALL | Returns the record with the most recent timestamp after all replicas have responded. The read operation will fail if a replica does not respond. | Provides the highest consistency of all levels and the lowest availability of all levels. |
| EACH_QUORUM | Returns the record with the most recent timestamp once a quorum of replicas in each data center of the cluster has responded. | Same as LOCAL_QUORUM |
| LOCAL_SERIAL | Same as SERIAL, but confined to the data center. | Same as SERIAL |
| LOCAL_QUORUM | Returns the record with the most recent timestamp once a quorum of replicas in the current data center as the coordinator node has reported. Avoids latency of inter-data center communication. | Used in multiple data center clusters with a rack-aware replica placement strategy ( NetworkTopologyStrategy) and a properly configured snitch. Fails when using SimpleStrategy. |
| LOCAL_ONE | Returns a response from the closest replica, as determined by the snitch, but only if the replica is in the local data center. | Same usage as described in the table about write consistency levels. |
| ONE | Returns a response from the closest replica, as determined by the snitch. By default, a read repair runs in the background to make the other replicas consistent. | Provides the highest availability of all the levels if you can tolerate a comparatively high probability of stale data being read. The replicas contacted for reads may not always have the most recent write. |
| QUORUM | Returns the record with the most recent timestamp after a quorum of replicas has responded regardless of data center. | Ensures strong consistency if you can tolerate some level of failure. |
| SERIAL | Allows reading the current (and possibly uncommitted) state of data without proposing a new addition or update. If a SERIAL read finds an uncommitted transaction in progress, it will commit the transaction as part of the read. | Lightweight transactions. |
| TWO | Returns the most recent data from two of the closest replicas. | Similar to ONE. |
| THREE | Returns the most recent data from three of the closest replicas. | Similar to TWO. |

## About QUORUM Level

The QUORUM level writes to the number of nodes that make up a quorum. A quorum is calculated, and then rounded down to a whole number, as follows:

(sum_of_replication_factors / 2) + 1

The sum of all the replication_factor settings for each data center is the sum_of_replication_factors.

For example, in a single data center cluster using a replication factor of 3, a quorum is 2 nodes―the cluster can tolerate 1 replica nodes down. Using a replication factor of 6, a quorum is 4―the cluster can tolerate 2 replica nodes down. In a two data center cluster where each data center has a replication factor of 3, a quorum is 4 nodes―the cluster can tolerate 2 replica nodes down. In a five data center cluster where each data center has a replication factor of 3, a quorum is 8 nodes.

If consistency is top priority, you can ensure that a read always reflects the most recent write by using the following formula:

(nodes_written + nodes_read) > replication_factor

For example, if your application is using the QUORUM consistency level for both write and read operations and you are using a replication factor of 3, then this ensures that 2 nodes are always written and 2 nodes are always read. The combination of nodes written and read (4) being greater than the replication factor (3) ensures strong read consistency.

## References

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
