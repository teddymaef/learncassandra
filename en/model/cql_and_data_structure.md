# CQL & Data Structure?

The recommended API for creating schemas in Cassandra since 0.7 is via CQL. But Cassandra encourages developer to share schema information to achieve more transparency. Why? Because although CQL looks very much like SQL, they don't work in a similar way internally. Remember, for each column family, don’t think of a relational table. Instead, think of a nested sorted map data structure.

## A Simple Example

For example, creating ColumnFamily(Table) with CQL:
```SQL
CREATE TABLE example (
    field1 int PRIMARY KEY,
    field2 int,
    field3 int);
```

And insert a few example rows:
```SQL
INSERT INTO example (field1, field2, field3) VALUES (1,2,3);
INSERT INTO example (field1, field2, field3) VALUES (4,5,6);
INSERT INTO example (field1, field2, field3) VALUES (7,8,9);
```
How is the data stored?
```text
RowKey: 1
=> (column=, value=, timestamp=1374546754299000)
=> (column=field2, value=00000002, timestamp=1374546754299000)
=> (column=field3, value=00000003, timestamp=1374546754299000)
-------------------
RowKey: 4
=> (column=, value=, timestamp=1374546757815000)
=> (column=field2, value=00000005, timestamp=1374546757815000)
=> (column=field3, value=00000006, timestamp=1374546757815000)
-------------------
RowKey: 7
=> (column=, value=, timestamp=1374546761055000)
=> (column=field2, value=00000008, timestamp=1374546761055000)
=> (column=field3, value=00000009, timestamp=1374546761055000)
```
For each item above, there are 3 important things to look at: the row key (RowKey: <?>), the column name (column=<?>) and the column value (value=<?>). From these examples, we can make a couple of initial observations about the mapping from CQL statements to their internal representations.
* The value of the CQL primary key is used internally as the row key (which in the new CQL paradigm is being called a “partition key”)
* The names of the non-primary key CQL fields are used internally as columns names, and the values of the non-primary key CQL fields are then internally stored as the corresponding column values
* The columns of each *RowKey* is sorted by column names

You may have also noticed that these rows all contain columns with no column name and no column value. This is not a bug! It’s actually a way of handling the fact that it should be possible to declare the existence of field1=&lt;some number&gt; without necessarily specifying values for field2 or field3.

## A More Complex Example

A more complicated example:

```SQL
CREATE TABLE example (
    partitionKey1 text,
    partitionKey2 text,
    clusterKey1 text,
    clusterKey2 text,
    normalField1 text,
    normalField2 text,
    PRIMARY KEY (
        (partitionKey1, partitionKey2),
        clusterKey1, clusterKey2
        )
    );
```
Here we’ve named the fields to indicate where they’ll end up in the internal representation. And we’ve also pulled out all the stops. Our primary key is not only compound, but it also uses compound partition keys, (this is represented by the double nesting of the parentheses of the PRIMARY KEY) and compound cluster keys (more on partition vs. cluster keys in a bit).

And insert a row of data:

```SQL
INSERT INTO example (
    partitionKey1,
    partitionKey2,
    clusterKey1,
    clusterKey2,
    normalField1,
    normalField2
    ) VALUES (
    'partitionVal1',
    'partitionVal2',
    'clusterVal1',
    'clusterVal2',
    'normalVal1',
    'normalVal2');
```
How is the data stored?

```test
RowKey: partitionVal1:partitionVal2
=> (column=clusterVal1:clusterVal2:, value=, timestamp=1374630892473000)
=> (column=clusterVal1:clusterVal2:normalfield1, value=6e6f726d616c56616c31, timestamp=1374630892473000)
=> (column=clusterVal1:clusterVal2:normalfield2, value=6e6f726d616c56616c32, timestamp=1374630892473000)
```
* Looking at *partitionVal1* and *partitionVal2* we see that these are concatenated together and used internally as the *RowKey* (a.k.a. the partition key)
* *clusterVal1* and *clusterVal2* are concatenated together and used in the names of each of the non-primary key columns
* The actual values of *normalfield1* and *normalfield2* are encoded as the values of columns *clusterVal1:clusterVal2:normalfield1* and *clusterVal1:clusterVal2:normalfield2* respectively, that *is6e6f726d616c56616c31* becomes *normalVal1* and *6e6f726d616c56616c32* becomes *normalVal2*
* The columns of each *RowKey* is sorted by column names, and since the values of *clusterKey1* and *clusterKey2* are part of the column names, they are actually sorted by values of *clusterKey1* and *clusterKey2* first, and then the original column names specified in CQL

## Map, List & Set

An example of how sets, lists, and maps work under the hood:

```SQL
CREATE TABLE example (
    key1 text PRIMARY KEY,
    map1 map<text,text>,
    list1 list<text>,
    set1 set<text>
    );
```

Insert:

```SQL
INSERT INTO example (
    key1,
    map1,
    list1,
    set1
    ) VALUES (
    'john',
    {'patricia':'555-4326','doug':'555-1579'},
    ['doug','scott'],
    {'patricia','scott'}
    )
```

How is data stored?

```text
RowKey: john
=> (column=, value=, timestamp=1374683971220000)
=> (column=map1:doug, value='555-1579', timestamp=1374683971220000)
=> (column=map1:patricia, value='555-4326', timestamp=1374683971220000)
=> (column=list1:26017c10f48711e2801fdf9895e5d0f8, value='doug', timestamp=1374683971220000)
=> (column=list1:26017c12f48711e2801fdf9895e5d0f8, value='scott', timestamp=1374683971220000)
=> (column=set1:'patricia', value=, timestamp=1374683971220000)
=> (column=set1:'scott', value=, timestamp=1374683971220000)
```

Internal column names for map, list and set items have different patterns:
* For map, each item in the map becomes a column, and the column name is the combination of the map column name and the key of the item, the value is the value of the item
* For list, each item in the list becomes a column, and the column name is the combination of the map column name and the UUID of the item order in the list, the value is the value of the item
* For set, each item in the set becomes a column, and the column name is the combination of the map column name and the item value, the value is always empty

## References

1. http://www.opensourceconnections.com/2013/07/24/understanding-how-cql3-maps-to-cassandras-internal-data-structure/
2. http://www.opensourceconnections.com/2013/07/24/understanding-how-cql3-maps-to-cassandras-internal-data-structure-sets-lists-and-maps/
