# Cassandra的历史

<sup>[1](#ref_1)</sup>Apache Cassandra最早是Facebook为了改进他们的Inbox搜索功能，由Avinash Lakshman（Amazon Dynamo的作者之一）和Prashant Malik写的。2008年七月成为Google Code上的开源项目。2009年三月成为Apache Incubator项目。2010年2月17日升级为Apache的顶级项目。

**成为顶级项目之后的版本更新记录:**

* 0.6, released Apr 12 2010, added support for integrated caching, and Apache Hadoop MapReduce
* 0.7, released Jan 08 2011, added secondary indexes and online schema changes
* 0.8, released Jun 2 2011, added the Cassandra Query Language (CQL), self-tuning memtables, and support for zero-downtime upgrades
* 1.0, released Oct 17 2011, added integrated compression, leveled compaction, and improved read performance
* 1.1, released Apr 23 2012, added self-tuning caches, row-level isolation, and support for mixed ssd/spinning disk deployments
* 1.2, released Jan 2 2013, added clustering across virtual nodes, inter-node communication, atomic batches, and request tracing
* 2.0, released Sep 4 2013, added lightweight transactions (based on the Paxos consensus protocol), triggers, improved compactions, CQL paging support, prepared statement support, SELECT column alias support

## 参考
1. <a name="ref_1"></a>http://en.wikipedia.org/wiki/Apache_Cassandra
