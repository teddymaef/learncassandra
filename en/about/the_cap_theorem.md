# The CAP Theorem

<sup>[1](#ref_1)</sup>The CAP theorem, also known as Brewer's theorem, states that it is impossible for a distributed computer system to simultaneously provide all three of the following guarantees:
* Consistency (all nodes see the same data at the same time)
* Availability (a guarantee that every request receives a response about whether it was successful or failed)
* Partition tolerance (the system continues to operate despite arbitrary message loss or failure of part of the system)

According to the theorem, a distributed system cannot satisfy all three of these guarantees at the same time.

## Cassandra and CAP

Cassandra is typically classified as an AP system, meaning that availability and partition tolerance are generally considered to be more important than consistency in Cassandra. But Cassandra can be tuned with replication factor and consistency level to also meet C.

## References
1. <a name="ref_1"></a>http://en.wikipedia.org/wiki/CAP_theorem
