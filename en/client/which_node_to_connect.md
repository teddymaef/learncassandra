# Which Node to Connect

Which node to connect depends on the configuration of the client side driver.

For example, if you are using the [Datastax drivers](http://www.datastax.com/download), there are two main configuration at client side which controls which node to talk to:

* Contact points
* Load balancing policies

## Contact Points

Contact points setting is a list of one or many node address. When creating a Cluster instance at client side, the driver tries to connect to the nodes specified in the "contact points" in order. If it fails to connect to the first node, try the next ... As long as one node is successfully connected, it does not try to connect the other nodes anymore.

Why it is not necessary to connect to all the contact points is because, each node contains the metadata of all the other nodes, meaning as long as one is connected, the driver could get infomation of all the nodes in the cluster. The driver will then use the metadata of the entire cluster got from the connected node to create the connection pool. This also means, it is not necessary to set address of all your nodes to the contact points setting. The best practice is to set the nodes which responds the fastest to the client as contact points.

## Load Balancing Policies

By default, a client side Cluster instance manages connections to all the nodes in the cluster and ramdonly connect to one node for any client request, which might not be performant enough, especially when you have multiple data centers.

For example, if you have a Cluster consists of nodes in two data centers, ont at China and one at US. If your client is at China, you don't want to connect to a US node, because it might be too slow.

"Load balancing policies" setting on a Cluster instance determines the strategy of assigning connection of nodes to client requests.

One of the most commonly used build-in load balancing policy is the DCAwareLoadBalancePolicy, which could make the Cluster only assign connections of the nodes in the specified data center to any client requests.

You are also allowed to implement your own custom load balanacing policies.

## Coodinator

<sup>[1](#ref_1)</sup>When a client connects to a node and issues a read or write request, that node serves as the coordinator for that particular client operation.

The job of the coordinator is to act as a proxy between the client application and the nodes (or replicas) that own the data being requested. The coordinator determines which nodes in the ring should get the request based on the cluster configured [partitioner](../replication/partitioners.html) and [replication strategy](../replication/replication_strategies.html).

## Reference

1. <a name="ref_1"></a>http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureClientRequestsAbout_c.html
