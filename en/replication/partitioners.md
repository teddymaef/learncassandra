# Partitioners

<sup>[1](#ref_1)</sup>A partitioner determines how data is distributed across the nodes in the cluster. Basically, a partitioner is a hash function for computing the token (it's hash) of a partition key. Each row of data is uniquely identified by a partition key and distributed across the cluster by the value of the token.

The partitioner configuration is a global configuration for the entire cluster. The Murmur3Partitioner is the default partitioning strategy for new Cassandra clusters and the right choice for new clusters in almost all cases.

**Cassandra offers the following partitioners:**

* Murmur3Partitioner (default): uniformly distributes data across the cluster based on MurmurHash hash values.
* RandomPartitioner: uniformly distributes data across the cluster based on MD5 hash values.
* ByteOrderedPartitioner: keeps an ordered distribution of data lexically by key bytes

Both the Murmur3Partitioner and RandomPartitioner uses tokens to help assign equal portions of data to each node and evenly distribute data from all the column families (tables) throughout the ring.

<sup>[2](#ref_2)</sup>ByteOrderedPartitioner is for ordered partitioning. It orders rows lexically by key bytes. Only when using ordered partitioner, it allows ordered row scan by partition key. This means you can scan rows as though you were moving a cursor through kind of a traditional primary index in RDBMS. For example, if your application has user names as the partition key, you can scan rows for users whose names fall between Jake and Joe. This type of query is not possible using randomly partitioned partition keys because the keys are stored in the order of their hash values (not sequentially).

Although having the capability to do range scans on rows sounds like a desirable feature of ordered partitioners, **using an ordered partitioner is not recommended for the following reasons:**

* Difficult load balancing
* Sequential writes can cause hot spots
* Uneven load balancing for multiple tables

## References

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architecturePartitionerAbout_c.html#concept_ds_dwv_npf_fk
2. <a name="ref_2"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architecturePartitionerBOP_c.html
