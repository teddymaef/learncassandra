# CQL

<sup>[1](#ref_1)</sup>CQL consists of statements. Like SQL, statements change data, look up data, store data, or change the way data is stored. Statements end in a semicolon (;).

For example, the following is valid CQL syntax:
```SQL
SELECT * FROM MyTable;

UPDATE MyTable
  SET SomeColumn = 'Some Value'
  WHERE columnName = 'Something Else';
```
This is a sequence of two CQL statements. This example shows one statement per line, although a statement can usefully be split across lines as well.

## Uppercase and Lowercase

Keyspace, column, and table names created using CQL are case-insensitive unless enclosed in double quotation marks. If you enter names for these objects using any uppercase letters, Cassandra stores the names in lowercase. You can force the case by using double quotation marks.

For example:
```SQL
CREATE TABLE test (
  Foo int PRIMARY KEY,
  "Bar" int
);
```
CQL keywords are case-insensitive. For example, the keywords SELECT and select are equivalent.

## CQL Data Types

<sup>[2](#ref_2)</sup>CQL defines built-in data types for columns:

| CQL Type | Constants | Description |
| -- | -- | -- |
| ascii | strings | US-ASCII character string |
| bigint | integers | 64-bit signed long |
| blob | blobs | Arbitrary bytes (no validation), expressed as hexadecimal |
| boolean | booleans | true or false |
| counter | integers | Distributed counter value (64-bit long) |
| decimal | integers, floats | Variable-precision decimal |
| double | integers, floats | 64-bit IEEE-754 floating point |
| float | integers, floats | 32-bit IEEE-754 floating point |
| inet | strings | IP address string in IPv4 or IPv6 format* |
| int | integers | 32-bit signed integer |
| list&lt;T&gt; | n/a | A collection of one or more ordered elements, T could be any non-collection CQL data type, such as int, text, etc |
| map&lt;K,V&gt; | n/a | A key-value dictionary, K and V could be any non-collection CQL data type, such as int, text, etc |
| set&lt;T&gt; | n/a | A collection of one or more elements, T could be any non-collection CQL data type, such as int, text, etc |
| text | strings | UTF-8 encoded string |
| timestamp | integers, strings | Date plus time, encoded as 8 bytes since epoch |
| uuid | uuids | A UUID in [standard UUID](http://en.wikipedia.org/wiki/Universally_unique_identifier) format |
| timeuuid | uuids | Type 1 UUID only |
| varchar | strings | UTF-8 encoded string |
| varint | integers | Arbitrary-precision integer |

## CQL Command Reference

For a complete CQL command reference, see [CQL commands](http://www.datastax.com/documentation/cql/3.1/cql/cql_reference/cqlCommandsTOC.html).

## References

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cql/3.1/cql/cql_reference/cql_lexicon_c.html
2. <a name="ref_2"></a>http://www.datastax.com/documentation/cql/3.1/cql/cql_reference/cql_data_types_c.html
