# CQL & 数据架构

从Cassandra 0.7开始，推荐使用CQL来创建column family结构。
同时Cassandra鼓励开发人员分享column family结构。 为什么呢？因为，虽然CQL看起来和SQL很像，他们的内部原理完全不同。记住，不要把一个column family想像成关系型数据库的表，而要想像成一个嵌套的键值有序的哈希表。

## 简单例子

例如，用CQL创建一个ColumnFamily(Table)：
```SQL
CREATE TABLE example (
    field1 int PRIMARY KEY,
    field2 int,
    field3 int);
```

然后插入一些行：
```SQL
INSERT INTO example (field1, field2, field3) VALUES (1,2,3);
INSERT INTO example (field1, field2, field3) VALUES (4,5,6);
INSERT INTO example (field1, field2, field3) VALUES (7,8,9);
```
数据怎么存储呢？
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
对于每一个插入的行，有三点需要注意：row key (RowKey: <?>)，列名(column=<?>)和列值(value=<?>)。从上面的例子，我们可以做出一些基本的关于CQL如何映射到Cassandra内部存储结构的总结：
* row key的值是CQL中定义的主键的值。（在CQL术语里面，row key被称作“partition key”）
* CQL中的非主键的字段的字段名，映射到内部的列名，字段值映射到内部的列值
* 每一个*RowKey*下的左右字段，是按列名排序存储的

你可能也注意到了，每一个row，都有一个列，列名和列值都为空。这并不是bug。事实上这是为了支持只有row key字段有值，而没有任何其他列有值的情况。

## 更复杂的例子

一个更复杂一点的例子:

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
这里我们用字段在内部存储时的类型来命名字段。而且我们已经包含了所有情形。这里的主键不仅仅是复合主键，而且是复合partition key （在PRIMARY KEY部分的前半段，用括号括起的部分）和复合cluster key。

然后插入一些行:

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
数据怎么存储呢？

```text
RowKey: partitionVal1:partitionVal2
=> (column=clusterVal1:clusterVal2:, value=, timestamp=1374630892473000)
=> (column=clusterVal1:clusterVal2:normalfield1, value=6e6f726d616c56616c31, timestamp=1374630892473000)
=> (column=clusterVal1:clusterVal2:normalfield2, value=6e6f726d616c56616c32, timestamp=1374630892473000)
```
* 注意*partitionVal1*和*partitionVal2*，我们可以发现*RowKey*（也称为partition key）是这两个字段值的组合
* *clusterVal1*和*clusterVal2*这两个cluster key的  *值*（注意是值不是字段名称）的组合，成为了每一个非主键列名的前缀
* 非主键列的值，比如*normalfield1*和*normalfield2*的值，是列名加上cluster key的值之后的列的值
* 每一个row中的列，是按列名排序的，而因为cluster key的值成为非主键列名的前缀，每个row下的所有的列，实际上首先按照cluster key的值排序，然后再按照CQL里的列名排序

## Map, List & Set

下面是一个set，list和map类型的例子：

```SQL
CREATE TABLE example (
    key1 text PRIMARY KEY,
    map1 map<text,text>,
    list1 list<text>,
    set1 set<text>
    );
```

插入数据：

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

数据怎么存储呢？

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

map，list和set的内部存储各不相同：
* 对于map，每一个map的元素成为一列，列名是map字段的列名和这个元素的键值的组合，列值就是这个元素的值
* 对于list，每一个list的元素成为一列，列名是list字段的列名和一个代表元素在list里的index的UUID的组合，列值就是这个元素的值
* 对于set，每一个set的元素成为一列，列名是set字段的列名和这个元素的值的组合，列值总是空值

## References

1. http://www.opensourceconnections.com/2013/07/24/understanding-how-cql3-maps-to-cassandras-internal-data-structure/
2. http://www.opensourceconnections.com/2013/07/24/understanding-how-cql3-maps-to-cassandras-internal-data-structure-sets-lists-and-maps/
