#Cassandra

##Notes
* define replication during the creation of keyspace (which is similar to `create database` in SQL)
```
CREATE KEYSPACE somekeyspace
WITH REPLICATION = {
'class':'NetworkTopologyStrategy', 'dc-west':2, 'dc-east':3
}
```
* configuration in cassandra.yaml file
    * every node has this file
    * you can disable hinted handoff
    * a node will store a hint by default for 3 hours, may be changed
* replication will typically not occur on the same physical rack
* Datacenters: split spark cluster from cassandra cluster
    * so that spark applications do not trip cassandra
* consistency level can be set at the session, per query, etc

## Architecture
* ideally 3 - 5 TB (only) SSD per node
* maximum 1000 nodes for cassandra
    * scale Cassandra horizontally
* coordinator node picked by driver
* you never have to take the cluster down (not for updating, adding nodes, taking out nodes, etc)
    * seed nodes introduce the rest of the cluster to the joining nodes
    * any node can be seed nodes (2 or 3 nodes that will always be there, valid ip addresses)

## Peer-to-peer
* partitioned tolerance
    * if coordinator think 2/3 nodes are down, it will still allow a read/write from the single node that is up
    * **BUT you decide if the transaction was valid**
* single leader
    * load balance reads to followers, writes to leader
    * eventual consistency, won't be able to read your own writes (maybe 2ms before writes get propagated)
* many leaders aka sharding
    * split brain problem: occurs when some nodes aren't accessible and follower elects itself as leader, resulting in multiple leaders

## VNodes
* Regular token range assignment
    * token assignment not evenly spread
* vnodes is switched on by default
    * _spark users usually do NOT use vnodes_
    * _solr sharding doesn't play well with vnodes_
* each node has several tokens it manages
    * splitting the ring to multiple pieces
    * range assignment ratios remains as is but spread out through the ring
* default 256 virtual nodes per node

## Gossip
* talk to three nodes, arbitrarily, to exchange status (yours, theirs, others you've and they've talked to)
    * status: cluster metadata e.g. endpoint state, heartbeat state, application state
    * probabilistic
* 1 min. to propagate status across cluster
    * no theoretical upper bound but in practice, 1 min no matter how big the cluster
* very low bandwidth trickle

## Snitch
* richer concept of location
* tells a node the topology of the cluster
* cloud vs non-cloud snitches
* GossipingPropertyFileSnitch -> look in cassandra-rackdc.properties
```
dc=DC1
rack=RAC1
```

## Replication
* the higher the replication, token range shifts clockwise around the circle
* topology changes are either change in snitch or change keyspace or moving a rack to a different datacenter
* quorum: more than half i.e. 3 replications, 2/3 is valid


## Consistency
* CAP theorem
    * Availability > Partition Tolerance > Consistency
    * Cassandra sits between Availability and Partition Tolerance

When `R + W > n`, you get strong consistency

    R: nodes being read
    W: nodes being written to
    n: replication factor

* quorum: consistency
* one and one: latency

## Failed writes
* hints are kept for 3 hours by default
* throttling

Consistency level of ANY: hinted handoff enabled, all three nodes are down
if only a hint was done, still a "valid" write