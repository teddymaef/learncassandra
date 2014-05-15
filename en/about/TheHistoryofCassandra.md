# The History of Cassandra

<sup>[1](#ref_1)</sup>Apache Cassandra was developed at Facebook to power their Inbox Search feature by Avinash Lakshman (one of the authors of Amazon's Dynamo) and Prashant Malik. It was released as an open source project on Google code in July 2008. In March 2009, it became an Apache Incubator project. On February 17, 2010 it graduated to a top-level project.

**Releases after graduation include:**

* 0.6, released Apr 12 2010, added support for integrated caching, and Apache Hadoop MapReduce
* 0.7, released Jan 08 2011, added secondary indexes and online schema changes
* 0.8, released Jun 2 2011, added the Cassandra Query Language (CQL), self-tuning memtables, and support for zero-downtime upgrades
* 1.0, released Oct 17 2011, added integrated compression, leveled compaction, and improved read performance
* 1.1, released Apr 23 2012, added self-tuning caches, row-level isolation, and support for mixed ssd/spinning disk deployments
* 1.2, released Jan 2 2013, added clustering across virtual nodes, inter-node communication, atomic batches, and request tracing
* 2.0, released Sep 4 2013, added lightweight transactions (based on the Paxos consensus protocol), triggers, improved compactions, CQL paging support, prepared statement support, SELECT column alias support

## References
1. <a name="ref_1"></a>http://en.wikipedia.org/wiki/Apache_Cassandra
