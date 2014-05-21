# Data Caching

<sup>[1](#ref_1)</sup>Cassandra includes integrated caching and distributes cache data around the cluster for you. The integrated cache solves the cold start problem by virtue of saving your cache to disk periodically and being able to read contents back in when it restarts. So you never have to start with a cold cache.

**There are two layers of cache:**
* Partition key cache
* Row cache

## How Does Caching Work?

![Figure 1](../assets/how_cache_works.png)

<sup>[2](#ref_2)</sup>One read operation hits the row cache, returning the requested row without a disk seek. The other read operation requests a row that is not present in the row cache but is present in the partition key cache. After accessing the row in the SSTable, the system returns the data and populates the row cache with this read operation.

## Tips for Efficient Cache Use

<sup>[3](#ref_3)</sup>Some tips for efficient cache use are:
* Store lower-demand data or data with extremely long rows in a table with minimal or no caching.
* Deploy a large number of Cassandra nodes under a relatively light load per node.
* Logically separate heavily-read data into discrete tables.

## References

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/operations/ops_configuring_caches_c.html
2. <a name="ref_2"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/operations/ops_how_cache_works_c.html
3. <a name="ref_3"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/operations/ops_cache_tips_c.html
