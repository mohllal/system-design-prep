# Database Sharding

Database sharding is the process of storing a large database across multiple machines improving database performance and scalability.

Sharding involves breaking up one’s data into two or more smaller chunks, called logical shards. The logical shards are then distributed across separate database nodes, referred to as physical shards, which can hold multiple logical shards.

## Sharding vs partitioning

Partitioning is more a generic term for dividing data across tables or databases. Sharding is one specific type of partitioning, part of what is called horizontal partitioning.

- Partitioning can be either horizontal or vertical, where data within a single database is split into smaller pieces (partitions).
- Sharding refers to horizontal partitioning, where a large database is split into smaller, distinct databases, each on a separate server.

## Horizontal vs vertical partitioning

- Horizontal partitioning (sharding): divides the data into rows. Each shard holds a subset of the rows.

- Vertical partitioning: Divides the data into columns. Each partition holds a subset of the columns.

Use cases: horizontal partitioning is typically used for handling large datasets by spreading the load across multiple servers, while vertical partitioning can be useful for separating data that is accessed together frequently.

## Shared-nothing architecture

Each physical shard operates independently and is unaware of other shards. Only the physical shards that contain the requested data will process the data in parallel.

## Trade-offs

Sharding provides benefits:

- Improved performance and scalability
  - Load distribution: spreads database load across multiple servers, reducing pressure on individual servers.
  - Parallel processing: Enables faster query response times through parallel query processing.
- Horizontal scalability: allows adding more shards to handle increased data and load seamlessly.
- Enhanced availability and reliability: Fault isolation by limiting the impact of a failure to the affected shard, not the entire system.
- Optimized resource utilization: better allocation of resources like memory and CPU tailored to each shard's needs.

But comes with costs:

- Increased complexity: adds complexity to database operations such as backup, restore, and scaling.
- Data distribution challenges: a poor choice of sharding strategy can lead to uneven data distribution which will create data hotspots.
- Higher latency for cross-shard operations: slower due to the need to aggregate data from multiple shards.

## Sharding architectures

### Range-based sharding (dynamic sharding)

Range-based sharding involves sharding data based on ranges of a given value.

Pros: easy to implement and understand.
Cons: Risk of uneven data distribution thus creating data hotspots.

### Hash-based sharding (key-based sharding)

Hash-based sharding uses a hash function is applied to the shard key to determine the shard. Broadly speaking, a shard key should be static, meaning it shouldn’t contain values that might change over time.

To ensure that entries are placed in the correct shards and in a consistent manner, the values entered into the hash function should all come from the same column. This column is known as a shard key.

Pros: ensures even data distribution when using a proper hashing function.
Cons: adding or removing physical shard requires re-sharding of some, if not all, data.

### Directory-based sharding

Directory-based sharding uses a lookup table to keep track of which shard contains which data.

Directory-based sharding is a good choice over range based sharding in cases where the shard key has a low cardinality — meaning, it has a low number of possible values — and it doesn’t make sense for a shard to store a range of keys.

Pros: flexibility in changing sharding logic.
Cons: the lookup table itself can become a bottleneck (hotspot).

### Geo-based sharding

Geo sharding splits and stores database information according to geographical location.

Pros: works well if data access patterns are predominantly based on geography, then this works well.
Cons: can result in uneven data distribution.

## External references

- [What is Database Sharding?](https://aws.amazon.com/what-is/database-sharding/)
- [Understanding Database Sharding](https://www.digitalocean.com/community/tutorials/understanding-database-sharding)
- [Shared-nothing architecture](https://en.wikipedia.org/wiki/Shared-nothing_architecture)