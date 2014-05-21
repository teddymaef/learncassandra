# Indexing

<sup>[1](#ref_1)</sup>An index (former name: secondary index) provides means to access data in Cassandra using non-primary key fields other than the partition key. The benefit is fast, efficient lookup of data matching a given condition. Actually, if there is no index on a normal column, it is even not allowed to conditionally query by the column.

An index indexes column values in a separate, hidden column family (table) from the one that contains the values being indexed. The data of an index is local only, which means it will not be replicated to other nodes.

**Caution:**

* By the current version (2.0.7) of Apache Cassandra, you could only query by an indexed column with equality comparison condition. Range select or order-by by an indexed column is not supported. The reason is the keys stored in the hidden column family are unsorted.
* All the data of the hidden column family of an index is only stored locally on each node, which means to query by an indexed column, the requests are sent to all the nodes, waiting for all the resonses, and then the results are merged. So if you have many nodes, the query response slows down as more machines are added to the cluster..

## When to Use An Index?

<sup>[2](#ref_2)</sup>Cassandra's built-in indexes are best on a table having many rows that contain the indexed value. The more unique values that exist in a particular column, the more overhead you will have for querying and maintaining the index. For example, suppose you had a playlists table with a billion songs and wanted to look up songs by the artist. Many songs will share the same column value for artist. The artist column is a good candidate for an index.

## When Not to Use An Index?

<sup>[2](#ref_2)</sup>Do not use an index in these situations:
* On high-cardinality columns because you then query a huge volume of records for a small number of results
* In tables that use a counter column
* On a frequently updated or deleted column
* To look for a row in a large partition unless narrowly queried

## References

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cql/3.1/cql/ddl/ddl_primary_index_c.html
2. <a name="ref_2"></a>http://www.datastax.com/documentation/cql/3.1/cql/ddl/ddl_when_use_index_c.html
