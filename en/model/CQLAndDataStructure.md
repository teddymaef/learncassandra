# How CQL Maps to Internal Data Structure?
The recommended API for creating schemas in Cassandra since 0.7 is via CQL. But Cassandra encourages developer to share schema information to achieve more transparency. Why? Because although CQL looks very much like SQL, they don't work in a similar way internally.

For example, creating ColumnFamily(Table) with CQL:

``` sql
CREATE TABLE timeline (
    user_id varchar,
    tweet_id int,
    author varchar,
    body varchar,
    PRIMARY KEY (user_id));
```
