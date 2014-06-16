# 可调节的一致性

<sup>[1](#ref_1)</sup>一致性指一行数据的所有replica是否最新和同步。Cassandra扩展了[最终一致性](http://en.wikipedia.org/wiki/Eventual_consistency)的概念，对一个读或者写操作，所谓可调节的一致性的概念，指发起请求的客户端，可以通过consistency level参数，指定本次请求，需要的一致性。

## 写操作的Consistency Level

写操作的consistency level指定了写操作在通知客户端请求成功之前，必须确保已经成功完成写操作的replica的数量。

| 级别 | 描述 | 用法 |
| -- | -- | -- |
| ANY | 任意一个节点写操作已经成功。如果所有的replica节点都挂了，写操作还是可以在记录一个[hinted handoff](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_about_hh_c.html#concept_ds_ifg_jqx_zj)事件之后，返回成功。如果所有的replica节点都挂了，写入的数据，在挂掉的replica节点恢复之前，读不到。| 最小的延时等待，并且确保写请求不会失败。相对于其他级别提供最低的一致性和最高的可用性。 |
| ALL | 写操作必须将指定行的数据写到所有replica节点的commit log和memtable。| 相对于其他级别提供最高的一致性和最低的可用性。 |
| EACH_QUORUM | 写操作必须将指定行的数据写到每个数据中心的quorum数量的replica节点的commit log和memtable。| 用于多数据中心集群严格的保证相同级别的一致性。例如，如果你希望，当一个数据中心挂掉了，或者不能满足quorum数量的replica节点写操作成功时，写请求返回失败。|
| LOCAL_ONE | 任何一个本地数据中心内的replica节点写操作成功。| 对于多数据中心的情况，往往期望至少一个replica节点写成功，但是，又不希望有任何跨数据中心的通信。LOCAL_ONE正好能满足这样的需求。|
| LOCAL_QUORUM | 本地数据中心内quorum数量的replica节点写操作成功。避免跨数据中心的通信。 | 不能和SimpleStrategy一起使用。用于保证本地数据中心的数据一致性。|
| LOCAL_SERIAL | 本地数据中心内quorum数量的replica节点有条件地（conditionally）写成功。| 用于轻量级事务（lightweight transaction）下实现[linearizable consistency](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_tunable_consistency_c.html#concept_ds_f4h_hwx_zj)，避免发生无条件的（unconditional）更新。。 |
| ONE | 任意一个replica节点写操作已经成功。| 满足大多数用户的需求。一般离coordinator节点具体最近的replica节点优先执行。|

**注意：**

即使指定了consistency level ON或LOCAL_QUORUM，写操作还是会被发送给所有的replica节点，包括其他数据中心的里replica节点。consistency level只是决定了，通知客户端请求成功之前，需要确保写操作成功的replica节点的数量。

## 读操作的Consistency Level

| 级别 | 描述 | 用法 |
| -- | -- | -- |
| ALL | 向所有replica节点查询数据，返回所有的replica返回的数据中，时间戳最新的数据。如果某个replica节点没有相应，读操作会失败。| 相对于其他级别，提供最高的一致性和最低的可用性。|
| EACH_QUORUM | 向每个数据中心内quorum数量的replica节点查询数据，返回时间戳最新的数据。| 同LOCAL_QUORUM。|
| LOCAL_SERIAL | 同SERIAL，但是只限制为本地数据中心。| 同SERIAL。|
| LOCAL_QUORUM | 向每个数据中心内quorum数量的replica节点查询数据，返回时间戳最新的数据。避免跨数据中心的通信。| 使用SimpleStrategy时会失败。|
| LOCAL_ONE | 返回本地数据中心内离coordinator节点最近的replica节点的数据。| 同写操作Consistency level中该级别的用法。|
| ONE | 返回由snitch决定的最近的replica返回的结果。默认情况下，后台会触发read repair确保其他replica的数据一致。 | 提供最高级别的可用性，但是返回的结果不一定最新。|
| QUORUM | 读取所有数据中心中quorum数量的节点的结果，返回合并后timestamp最新的结果。| 保证很强的一致性，虽然有可能读取失败。 |
| SERIAL | 允许读取当前的（包括uncommitted的）数据，如果读的过程中发现uncommitted的事务，则commit它。| 轻量级事务。 |
| TWO | 返回两个最近的replica的最新数据。| 和ONE类似。 |
| THREE | 返回三个最近的replica的最新数据。| 和TWO类似。|

## 关于QUORUM级别

QUORUM级别确保数据写到指定quorum数量的节点。一个quorum的值由下面的公式四舍五入计算而得：

(sum_of_replication_factors / 2) + 1

sum_of_replication_factors指每个数据中心的所有replication_factor设置的总和。

例如，如果某个单数据中心的replication factor是3，quorum值为2-表示集群可以最多容忍1个节点down。如果replication factor是6，quorum值为4-表示集群可以最多容忍2个节点down。如果是双数据中心，每个数据中心的replication factor是3，quorum值为4-表示集群可以最多容忍2个节点down。如果是5数据中心，每个数据中心的replication factor of 3，quorum值为8 。

如果想确保读写一致性可以使用下面的公式：

(nodes_written + nodes_read) > replication_factor

例如，如果应用程序使用QUORUM级别来读和写，replication factor 值为3，那么，该设置能够确保2个节点一定会被写入和读取。读节点数加上写写点数（4）个节点比replication factor （3）大，这样就能确保一致性。

## 参考

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
