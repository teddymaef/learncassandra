# Concurrency Control

Cassandra does not use RDBMS ACID transactions with rollback or locking mechanisms, but instead offers atomic, isolated, and durable transactions with eventual/tunable consistency that lets the user decide how strong or eventual they want each transaction’s consistency to be.

## Lightweight Transactions

<sup>[1](#ref_1)</sup>While durable transactions with eventual/tunable consistency is quite satisfactory for many use cases, situations do arise where more is needed. Lightweight transactions, also known as compare and set, that use linearizable consistency can probably fulfill those needs.

For example, if a user wants to ensure an insert they are about to make into a new accounts table is unique for a new customer, they would use the IF NOT EXISTS clause:

```SQL
INSERT INTO customer_account (customerID, customer_email)
    VALUES (‘LauraS’, ‘lauras@gmail.com’)
    IF NOT EXISTS;
```

Modifications via UPDATE can also make use of the IF clause by comparing one or more columns to various values:

```SQL
UPDATE customer_account
    SET customer_email=’laurass@gmail.com’
    IF customer_email=’lauras@gmail.com’;
```
Behind the scenes, Cassandra is making four round trips between a node proposing a lightweight transaction and any needed replicas in the cluster to ensure proper execution so performance is affected. Consequently, reserve lightweight transactions for those situations where they are absolutely necessary; Cassandra’s normal eventual consistency can be used for everything else.

A [SERIAL consistency level](../replication/turnable_consistency.html) allows reading the current (and possibly uncommitted) state of data without proposing a new addition or update. If a SERIAL read finds an uncommitted transaction in progress, it will commit it as part of the read.

## Atomicity

<sup>[2](#ref_2)</sup>In Cassandra, a write is atomic at the row-level, meaning inserting or updating columns in a row is treated as one write operation. Cassandra does not support transactions in the sense of bundling multiple row updates into one all-or-nothing operation. Nor does it roll back when a write succeeds on one replica, but fails on other replicas. It is possible in Cassandra to have a write operation report a failure to the client, but still actually persist the write to a replica.

For example, if using a write consistency level of QUORUM with a replication factor of 3, Cassandra will replicate the write to all nodes in the cluster and wait for acknowledgement from two nodes. If the write fails on one of the nodes but succeeds on the other, Cassandra reports a failure to replicate the write on that node. However, the replicated write that succeeds on the other node is not automatically rolled back.

Cassandra uses timestamps to determine the most recent update to a column. The timestamp is provided by the client application. The latest timestamp always wins when requesting data, so if multiple client sessions update the same columns in a row concurrently, the most recent update is the one that will eventually persist.

## Durability

<sup>[3](#ref_3)</sup>Writes in Cassandra are durable. All writes to a replica node are recorded both in memory and in a commit log on disk before they are acknowledged as a success. If a crash or server failure occurs before the memory tables are flushed to disk, the commit log is replayed on restart to recover any lost writes. In addition to the local durability (data immediately written to disk), the replication of data on other nodes strengthens durability.

## References

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_ltwt_transaction_c.html
2. <a name="ref_2"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_atomicity_c.html
3. <a name="ref_3"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_durability_c.html
