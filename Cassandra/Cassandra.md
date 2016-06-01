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
    * so that spark applications do not trip cassandra IO
* consistency level can be set per session, per query, etc

## Architecture
* ideally 3 - 5 TB (only) SSD per node
* maximum 1000 nodes for cassandra
    * scale Cassandra horizontally
* coordinator node picked by driver
* seed nodes introduce the rest of the cluster to the joining nodes
* any node can be seed nodes (2 or 3 nodes that will always be there, valid ip addresses)
> you never have to take the cluster down (not for updating, adding nodes, taking out nodes, etc)

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

* Consistency levels
    * quorum: consistency
    * one and one: latency

## Failed writes
* hints are kept for 3 hours by default
* throttling
* read repair is asynchronous, on whole partition

Consistency level of ANY:
Given hinted hand-off is enabled and all three nodes are down, even if only a hint was handed off, still a "valid" write

>read up on Merkle-tree

## Nodetool Repair
* Nodetool is the main management tool for Cassandra
    * get status and control cluster
* should be done atleast weekly, off-hours maintenance
* repair more often than your grace period, default 10 days

## Write Path
* Storage (based on moving disks, big sequential write is better than making many little writes)
    * CommitLog
        * sequential, no index structures
        * one commit log per node/server
        * logs of mutations, etc.
        * flush all at once (all MemTables included)
    * MemTable, for speed
        * one MemTable per table
        * contains only recent updates
        * in RAM
    * SSTable (immutable)
        * set of SSTables per table
        * in HDD
        * flush tables whenever there are writes
* deletions are just writes, tombstone records to be

## Reading data
_Reads from MemTable and SSTable_
* read from MemTable > SSTable
    * if entire row is in MemTable, all well and good
    * if one column is recently updated, retrieval for that column may be done from MemTable **but**
    other columns required may not be on MemTable so then SSTables have to be read
* Read from SSTable
    * partition summary
    * row-caching is NOT recommended
    * caching done in key-cache for records that were difficult to retrieve such as the one below

| Token | Byte Offset |
|---    |---          |
| 36    | 6,224       |

## Compaction Strategies
* time-series optimized
* read-heavy
* write-heavy


# Q & A
1. When to choose Cassandra?
    * it has to be big data, when you're not comfortable managing using legacy tools.
    * transactional
    * always ON
    > Scheduled maintenance windows are a thing of the past.

2. Anti-patterns for Cassandra?
    * used as a queue (use Kafka instead, or in combination with)
    * large (immutable) blobs

3. Solr and Cassandra?
    * there used to be solyndra (now dead)
        * solr core for table, define schema and store schema in cassandra table
        * each node is indexed locally so solr index is always local
    * cassandra read is 5 ms > search is at 50 - 500 ms (doable for full text search) > still better than elastic search

4. How are joins done on Cassandra?
    * no joins in cassandra, integrate spark and do the join with spark

5. Why de-normalize?
    * you do normalize in relational databases, but under the pressure of scale, normalization is given less and less priority
    * you give up consistency due to replication
    > you were going to do it anyway, so de-normalize from the beginning

6. When to switch technologies on enterprise systems?
    * rebuilding a working thing on promising tech, risky
    * fixing a broken thing using new tech, good
    * building new things with promising new tech, safest bet

7. Hadoop integration?
    * no open-source hadoop integration
    * you do NOT run HDFS on the same node
        * CFS: make cassandra tables to look like hdfs i.e. hdfs mocking
    > funny to say that Hadoop is now a legacy technology, no more development in map reduce perhaps in maintenance
    ...hdfs on the other hand is a cockroach and will most likely be around for the foreseeable future

8. Security?
    * LDAP and kerberos integration (commercial), perhaps eventually in open source

9. Schema migration?
    * tooling is less sophisticated
        * there exists for RDBMS a tool to source control schema, could eventually exist for cassandra as well
    * fixed key structure, so key migrations are hard to migrate
    * for non-key migrations, then it's the same as what's done on relational databases

10. What are tokens?
    * An element on the ring that depends on the partitioner
    * A token determines the node's position on the ring and the portion of data it is responsible for
