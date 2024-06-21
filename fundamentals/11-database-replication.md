# Database Replication

Data replication involves copying data from one database server (the primary) to one or more database servers (the replicas). This process ensures that the same data is available on multiple servers, improving data availability, reliability, and performance.

## Benefits

- High availability: ensures data remains accessible even if one server fails.
- Load balancing: distributes read operations across multiple read servers, reducing load on the primary server.
- Disaster recovery: provides backups in case of data loss or corruption.
- Geographical distribution: places data closer to users in different locations, reducing latency.

## Methods

- Synchronous replication: data is copied to replicas at the same time it is written to the primary database. Ensures consistency but can increase write latency.
- Asynchronous replication: data is copied to replicas after it has been written to the primary database. Reduces latency but might result in slight data inconsistency.
- Semi-synchronous replication: a hybrid approach where the primary server waits for at least one replica to acknowledge receipt of the data before committing.

## Types

- Transactional replication: replicates data changes (inserts, updates, deletes) as they occur.
- Snapshot replication: copies data at a specific point in time. Useful for relatively static data or initial data seeding.
- Merge replication: consists of two databases being combined into a single database. As a result, any changes to data can be updated from the publisher to the subscribers.

## Architectures

### Master-slave replication

In master-slave replication, there is one primary server (master) that handles all write operations. Replicas (slaves) asynchronously replicate data from the master and handle read operations.

Example: MongoDB uses master-slave replication for scaling read operations.

Challenges:

- Single point of failure: the master server can become a bottleneck or a single point of failure.
- Read eventual consistency: ensuring consistent reads across replicas can be challenging without proper synchronization, especially in asynchronous method.
- Replication lag: Replicas might lag behind the master due to network latency or processing delays.

### Multi-master replication

In multi-master replication, multiple nodes of a database cluster accepting the write requests. As soon as a master node gets the write request, it accepts it and applies the changes. Once the update is made on one master, it is propagated to all other master nodes of the cluster asynchronously, making the data eventually consistent.

Example: DynamoDB uses multi-master replication to support distributed applications.

Challenges:

- Conflict resolution: resolving conflicts when multiple nodes modify the same data concurrently can be complex and requires sophisticated algorithms.
- Concurrency control: Ensuring data integrity and preventing conflicts in highly concurrent environments requires robust concurrency control mechanisms.
- Performance overhead: Synchronizing data changes across multiple masters can introduce performance overhead, especially under heavy write workloads.

### Masterless replication (a.k.a leaderless replication)

In masterless replication, also known as peer-to-peer or decentralized replication, is a replication strategy where each database node in a distributed system can independently handle both read and write operations. Unlike traditional master-slave or master-master architectures, there is no dedicated master node controlling the replication process.

Example: Apache Cassandra uses masterless replication to ensure fault tolerance and scalability across nodes.

Challenges:

- Consistency management: ensuring eventual consistency across all nodes without a centralized coordinator can be complex and requires careful design of conflict resolution mechanisms.
- Data distribution: efficiently distributing data across nodes to avoid hotspots and maintain balanced cluster performance.
- Concurrency control: implementing mechanisms to handle concurrent writes and resolving conflicts that may arise when multiple nodes update the same data simultaneously.

## External references

- [What is data replication?](https://www.ibm.com/topics/data-replication)
- [Database replication](https://www.scylladb.com/glossary/database-replication/)
- [Database Replication Explained](https://www.youtube.com/watch?v=bI8Ry6GhMSE&ab_channel=Exponent)
- [All Types of Database Replication Discussed](https://www.youtube.com/watch?v=aE2UPg3Ckck&ab_channel=HusseinNasser)
- [What is a master-replica setup and why it matters?](https://arpitbhayani.me/blogs/master-replica-replication)
- [What is multi-master replication and why do we need it?](https://arpitbhayani.me/blogs/multi-master-replication/)
- [What is leaderless replication and how it works?](https://arpitbhayani.me/blogs/leaderless-replication)
