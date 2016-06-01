# Data Models in Cassandra

##Notes
* queries take center stage in schema design
* data suited for cassandra
    * Most domains
    * Transactional or operational data
    * Demands of web, mobile, and internet of things (IOT)
    * High availability!
    * Scale!
    * Volume, Velocity, Variety
* Chen notation: describe entities, relationships, attributes, etc.
    * entities: rectangles
    * relationships: lines between entities
    * cardinality: m, n, 1-n, 1-1
    * attributes: ovals, two rings if multiple values for attribute can exist i.e. genres, tags
* Limitations
    * Cassandra 2.0 and earlier
        * Partition size: About 100 MB or less
        * number of values: 100,000
    * Cassandra 2.1+
        * Partition size: 100s of MB per partition
        * number of values: hundreds of thousands
    * **you CANNOT update partition keys**
    * Calculating number of values
        * if you have regular columns, use general formula
        * if you have ONLY primary key and static use 2nd formula
        ![formula](http://mathurl.com/hoy5wpr.png)
        $$N_v = N_r \times (N_c - N_{pk} - N_s) + N_s$$

## Data Modelling
### Chebotko Diagrams
    * ERD: entity relationship diagram
    * logical and physical data model
    * no data types, data types in physical data model
    * optimization between logical and physical
    * how data is organized as well as flow
    * UDT: user-defined type, to group data together, queries are not run against UDTs
    * S: static columns
    * {}: collections i.e. lists, sets, tuples
    * K: partition key
    * C: clustering columns
### Data modelling principles
* know your data and queries, nesting and duplicating data
* seems like cassandra has much data duplication
    * no joins
    * disk space is cheap
    * therefore, data duplication is OK
    * consistency, syncing multiple tables in batch
### Key constraints
* partitioning strategy
    * how is data partitioned and store
    * uniqueness is not primary concern
    * combination of columns to make up your key
* cardinality affects key
    * 1:1   either
    * m:n   combination
    * 1:n   key on many side
### Query and schema design
* schema design is directly affected by queries
    * how you want to pull out data defines how schema is structured

```
(ideal) partition per query > partition+ per query >> (anti-patterns) table scan and multi-table
```
* design organizes data to efficiently run queries
        * partition+: multiple partitions per query i.e. access multiple nodes to retrieve result
        * a single partition **ALWAYS** lands on a single node
    * partition may be single- or multi-row
    * coordinator is contacted first, picked randomly and is responsible for read/write
### Nest data
* organizes multiple entities in a single partition
* clustering mechanisms
    * clustering columns
        * primary key can be a combination of columns
            * primary key has partition and clustering components i.e. K and all C[↑↓]
        * partition key identifies entity other entities will nest into
    * collection columns
    * user-defined type columns

#### Clustering columns
denoted by C, ↑: ascending, ↓: descending

|actors_by_video| primary key component |
|---| ---: |
|video_id |  K|
|actor_name | C↑|
|character_name |C↑|

#### User-defined types
UDT will look like a table, defines nesting feature

### Duplicate data
* duplication allows for fast queries
    * duplication can scale while joins cannot
* "joins" are not done on reads but rather on writes leading to duplication

## Mapping rules
* query-driven methodology
* _numbering of mapping rules is a must-know for the certification exam_

### MR1: Entities and Relationships
* mapping tables
* everything in conceptual model will map to logical model
    * entity is a table
    * attributes are columns in table
        * underlined attribute (unique) will be primary key
* each relationship is a row in the table

### MR2: Equality search attributes
* ordered set of columns, primary key and clustering columns
* equality search attributes MUST come first in ordering of primary key
    * i.e. search videos by title then primary key should be **title** [K] + type [K] + video_id [C↑]

### MR3: Inequality search attributes
* all inequality **FOLLOW** equality searches
> you CANNOT mix them up
* once you have an inequality you won't use following columns in the primary key sequence
* inequality searches are ">" or "<"
* **inequality search attributes become clustering columns**

### MR4: Ordering attributes
* Beware of ordering
* ordering is based on clustering columns
* unlike in relational, cassandra **pre-orders** and pre-joins

### MR5: Key attributes
* throw id (usually some uuid) in there for uniqueness
* key attribute has to be in the primary key, does not have to come first
    * it does not have to be the partition key of the table
    * it may be a clustering column

### Applying Mapping rules
```
user_id = ? and uploaded_timestamp > ?
ORDER BY uploaded_timestamp DESC
```
|videos_by_user|
|---|
|user_id    K|
|uploaded_timestamp C↓|
|video_id   C↑|
|...|

## Physical data model

`Physical model = Logical model + data types`

### Data duplication
* byproduct of the modelling principles
* is NOT replication
* consider data consistency: how do you keep tables in sync
* OK: duplication across tables, partitions, rows
* Not OK: non-constant duplication factor
    * re-think your use-case
    * apply limits