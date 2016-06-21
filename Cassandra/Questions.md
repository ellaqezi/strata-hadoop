# Quiz/Practice test questions

|Question|Answer|
|---|---|
|The CAP theorem states that only two out of three characteristics can be met by a distributed database system simultaneously. Which of the two characteristics does Apache Cassandra value most?|Availability and Partition tolerance|
|Apache Cassandra achieves high availability by|having no single point of failure|
|Durability is?| the property that writes, once completed, will survive permanently, even if the server is killed or crashes or loses power.|
|What does the gossip protocol do?|share known state and location with other nodes|
|Which of these Cassandra technologies work together to keep track of the cluster data center and rack topology?|Gossip and Snitch|
|Apache Cassandra can be downloaded in which formats, including DataStax Community variants?|Debian and Tarball|
|Clients should have ________________ so that the column values will replace older values based on their timestamp.|Synchronized clocks|
|Which of the following is not true for Apache Cassandra?|Cassandra can compute the minimum value for a column with CQL|
|In a Cassandra instance, a table called Orders holds order information. Each time an order is placed, an entry must also be placed in a denormalized table OrdersByCustomer to keep track of order history per customer. How does Cassandra handle this?|Cassandra will do nothing. An additional write must be specified in the application that fulfills the wrote to the Orders and OrdersByCustomer tables|
|When defining a table in Apache Cassandra, a _________________ must be defined.|Primary key|
|The main function of a keyspace is to control ___________________.|Replication|
|An application tracks a user's habits. Every time a user clicks a link on a page within your site, the time of the event is recorded as well as the link clicked. In order to write an efficient query, all data must be stored in a single partition. Which of the following tables best models the needs of the application?|CREATE TABLE habits (userid UUID, clicktime timestamp, link text, PRIMARY KEY(userid, clicktime));|
|You have designed a query to return all users by a designated state and designated age from a table holding all user information. Which approach is best for efficient queries like this?|Create the users table. Create a table that stores partition keys group by state and age. Query the second table.|
|Which of the following is a valid Cassandra data type?|timestamp|
|Which of the following are DML?|SELECT, ALTER TABLE, DROP are not DML|
|An SSTable is immutable, meaning that it cannot be modified.|TRUE|
|Compaction does NOT do which of the following?|Redistribute SSTables to rebalance node workload|
|Tombstones are _____________?|Markers that a delete has occurred - the standard write mechanisms are used to propagate the delete to other replicas|
|Which of the following statements about writes is incorrect?|Cassandra sends write requests to the minimum number of replica nodes needed to fulfill consistency requirements|
|What is the smallest atomic unit of storage in Cassandra?|partition|
|What is a cell?|key-value pair|
|What is a partition?|group of cells|
|What is the significance of the partition key?|Cassandra hashes the key value to determine which node the partition resides on|
|What is an upsert?|INSERTs may cause UPDATEs; UPDATEs may causes INSERTs|
|What purpose do clustering columns serve?|Provide uniqueness within the partition as well as ordering criteria|
|What is the relationship between a partition key and a clustering column?|Partition keys determine a grouping criteria whereas clustering columns determine ordering criteria|
