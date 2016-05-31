#Cassandra

##Notes

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
