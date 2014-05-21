# Data Replication

In this chapter, we will introduce how data is distributed & replicated in Cassandra nodes.

<sup>[1](#ref_1)</sup>Cassandra is designed as a peer-to-peer system that makes copies of the data and distributes the copies among a group of nodes.

Data is organized by column family (table). A row of data is identified by the partition key of the row. Cassandra uses the consistent hashing mechanism to distribute data across a cluster. Each cluster has a partitioner configured for hashing each partition key. The hash value of each partition key determines which node the data of the row should be stored on.

Copies of rows are called replicas. When data is first written, it is also referred to as the first replica of the data.

## References

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeAbout_c.html
