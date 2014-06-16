# CQL

<sup>[1](#ref_1)</sup>CQL由类似SQL的语句组成，包括修改，查询，保存，变更数据的存储方式等等功能。每一行语句由分号（;）结束。

例如，下面是一个合法的CQL：
```SQL
SELECT * FROM MyTable;

UPDATE MyTable
  SET SomeColumn = 'Some Value'
  WHERE columnName = 'Something Else';
```
这里一共两条语句。每条语句可以写成一行也可以写成多行。

## 大小写

在CQL里，keyspace，column和table的名称是忽略大小写的，除非用双引号（"）括起来，才是大小写敏感的。如果不用双引号括起来，即使CQL写成大写，也会被保存为小写。

例如：
```SQL
CREATE TABLE test (
  Foo int PRIMARY KEY,
  "Bar" int
);
```
CQL的关键字都是忽略大小写的。例如，关键字SELECT和select是等价的。

## CQL数据类型

<sup>[2](#ref_2)</sup>CQL内建了以下这些列的数据类型:

| CQL类型 | 常量类型 | 说明 |
| -- | -- | -- |
| ascii | strings | US-ASCII字符串 |
| bigint | integers | 64位有符号long |
| blob | blobs | 任意的16进制格式的bytes |
| boolean | booleans | true或false |
| counter | integers | 分布式counter值 (64位long) |
| decimal | integers, floats | 可变精度浮点数 |
| double | integers, floats | 64位IEEE-754浮点数 |
| float | integers, floats | 3位IEEE-754浮点数 |
| inet | strings | IPv4或IPv6格式的IP地址 |
| int | integers | 32位有符号整数 |
| list&lt;T&gt; | n/a | 有序集合, T可以是任意非集合CQL数据类型，例如：int, text等 |
| map&lt;K,V&gt; | n/a | 哈希表, K和V可以是任意非集合CQL数据类型，例如：int, text等 |
| set&lt;T&gt; | n/a | 无序集合, T可以是任意非集合CQL数据类型，例如：int, text等 |
| text | strings | UTF-8编码字符串 |
| timestamp | integers, strings | 日期+时间 |
| uuid | uuids | [标准UUID](http://en.wikipedia.org/wiki/Universally_unique_identifier)格式 |
| timeuuid | uuids | Type 1 UUID |
| varchar | strings | UTF-8编码字符串 |
| varint | integers | 任意精度整数 |

## CQL命令参考

完整的CQL命令参考，请参照：[CQL commands](http://www.datastax.com/documentation/cql/3.1/cql/cql_reference/cqlCommandsTOC.html).

## 参考

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cql/3.1/cql/cql_reference/cql_lexicon_c.html
2. <a name="ref_2"></a>http://www.datastax.com/documentation/cql/3.1/cql/cql_reference/cql_data_types_c.html
