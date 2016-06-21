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
|What command bulk-loads data files?|COPY|
|What command drops all records from an existing table?|TRUNCATE|
|What command adds/removes columns to/from a table?|ALTER|
|Which is NOT a Cassandra column type?|DICTIONARY<>|
|What command executes a file of CQL statements?|SOURCE|
|Cassandra counters are always 100% accurate.|FALSE|
|Which is NOT an advantage of conceptual data modeling?|Creates optimized tables for your hardware resources|
|Which is NOT a type found in a conceptual data model?|Constraint types|
|Attribute types cannot be...|block|
|How do you determine the key of a 1-1 relationship?|Key attributes of either participating entity types|
|How do you determine the key of a 1-n relationship?|Key attributes of entity type on the many side|
|How do you determine the key of a m-n relationship?|Key attributes of both participating entity types|
|What does disjoint mean?|An entity can only participate in only one subtype role|
|What is an application workflow?|Tasks formed by causual dependencies|
|What is a table's main purpose in a Cassandra database?|Serve a query|
|What are the two preferrable table query strategies?|Partition per query and partition+ per query|
|Why do we nest data in Cassandra?|Support a partition per query access pattern|
|Choose the option that lists the mapping rules in proper order|Entities and relationships, equality search attributes, inequiality search attributes, ordering attributes, key attributes|
|What is the recommended partition size limit for Cassandra 2.0 or earlier?|100MB|
|What Cassandra structure aides with consistency between duplicate data copies?|logged batch|
|When are client side joins appropriate?|When your resources don't support the necessary data duplication|
|How do lightweight transactions differ from normal updates/inserts?|They compare (validation a condition) before setting (read before write)|
|How do Cassandra PRIMARY KEYs differ from relational?|Cassandra keys support a query and provide uniqueness; relational keys provide only uniqueness.|
|Which is not an advantage of a surrogate key?|They are human friendly and easy to remember|
|Which partition splitting scheme gives you the most control?|Adding an artificial column|
