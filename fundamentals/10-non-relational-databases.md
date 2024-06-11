# Non-Relational Databases

Non-relational databases, also known as NoSQL databases are a class of database management systems that use various data models, including document-oriented, key-value, wide-column, and graph formats.

## Types

### Document-oriented databases

Document-oriented databases store data in a semi-structured format, typically using `JSON` or `BSON` documents. Each document can have its own unique structure, and fields within documents can vary. Data is often stored in collections (equivalent to tables in relational databases), and documents within these collections can be indexed for efficient querying.

### Key-Value databases

Key-value stores are the simplest form of NoSQL databases, storing data as a collection of key-value pairs. The key is used to uniquely identify the data, and the value can be any type of data, ranging from simple strings to complex objects. Data in key-value stores is often stored in memory for fast access, with the option to persist data to disk for durability.

### Graph databases

Graph databases are designed to represent and store data as graphs, consisting of nodes (entities) and edges (relationships). Nodes and edges can have properties, and queries in graph databases are typically expressed as graph traversals. This allows for efficient querying of complex relationships between entities.

### Time-series databases

Time-series databases specialize in storing and querying time-series data, which is data that is indexed by time. They are optimized for handling data points that are generated sequentially over time, such as sensor data, financial data, and application metrics. Time-series databases often use compression and aggregation techniques to efficiently store and query large volumes of time-series data.

### Column-oriented databases

Column-oriented databases store data in columns rather than rows, which can be more efficient for analytical workloads that involve querying large datasets for specific columns. Data in column-oriented databases is typically stored in columnar storage formats, such as [Apache Parquet](https://parquet.apache.org/) or [Apache ORC](https://orc.apache.org/), which allow for efficient compression and encoding of column data.

## BASE properties

BASE is an acronym that stands for Basically available, soft state, and eventually consistent. It is a set of properties that emphasize the trade-offs made by non-relational databases for achieving high availability and scalability.

- Basically available: the system guarantees availability, even in the face of network partitions and node failures by using a highly distributed approach to database management.
- Soft state: the state of the system may change over time, even without input. This implies that the data stored in the system may be temporary or transient.
- Eventually consistent: the system will eventually become consistent, given that the system is given enough time to converge. In other words, updates to the system will propagate and be reflected in all replicas, achieving eventual consistency.

## Querying

Querying in non-relational databases varies depending on the data model used by the database. Some common methods include:

- Document-oriented databases: querying is typically done using document-oriented query languages like MongoDB's query language or Couchbase's N1QL. These languages allow for querying based on the structure and content of documents.
- Key-value stores: querying is usually limited to simple operations like GET and PUT based on the keys of the stored data.
- Wide-column stores: querying is performed using column-based query languages like [Cassandra Query Language (CQL)](https://cassandra.apache.org/doc/stable/cassandra/cql/) or HBase's query interface. These languages allow for querying based on column families and columns within those families.
- Graph databases: querying is done using graph traversal languages like [Gremlin](https://en.wikipedia.org/wiki/Gremlin_(query_language)) or [Cypher](https://en.wikipedia.org/wiki/Cypher_(query_language)), which allow for complex graph-based queries to be executed.

## Denormalization

Data denormalization is used to improve read performance by reducing the need for complex joins and queries. Denormalization intentionally introduces redundancy to optimize for specific read-heavy use cases.

### Examples of Data Denormalization in Different NoSQL Databases

#### Denormalization in document-oriented databases

In document-oriented databases, denormalization often involves embedding related data within a single document. For example, instead of storing users and their posts in separate collections and performing joins at query time, user documents can embed an array of post documents.

```json
{
  "_id": "user123",
  "name": "John Doe",
  "email": "john.doe@example.com",
  "posts": [
    {
      "postId": "post1",
      "content": "This is the first post",
      "timestamp": "2024-06-11T12:00:00Z"
    },
    {
      "postId": "post2",
      "content": "This is the second post",
      "timestamp": "2024-06-12T12:00:00Z"
    }
  ]
}
```

### Denormalization in key-value databases

In key-value stores, denormalization can involve storing composite values that combine related information. For example, storing user profile and session information together as a single value associated with a user key.

```plaintext
Key: user:123
Value: {
  "profile": {
    "name": "John Doe",
    "email": "john.doe@example.com"
  },
  "session": {
    "lastLogin": "2024-06-11T12:00:00Z",
    "sessionId": "abc123"
  }
}
```

### Denormalization in graph databases

In graph databases, denormalization can involve duplicating nodes and relationships to speed up traversal queries. For example, creating direct relationships between frequently queried nodes even if those relationships can be derived from existing ones.

Here the `VIEWED` is created as a separate relationship although it can be derived directly from the `PURCHASED` relationship.

```plaintext
Nodes:
  (User: {id: "user123", name: "John Doe"})
  (Product: {id: "product1", name: "Product 1"})
  (Product: {id: "product2", name: "Product 2"})

Relationships:
  (User)-[:VIEWED]->(Product)
  (User)-[:PURCHASED]->(Product)
```

### Denormalization trade-offs

While denormalization can significantly enhance read performance, it comes with trade-offs:

- Increased storage requirements: redundant data can lead to increased storage usage.
- Data consistency challenges: maintaining consistency across duplicate data can be complex, especially in distributed systems.
- Complexity in write operations: write operations can become more complex and costly, as updates may need to be propagated to multiple logical (e.g. collections, items, etc...) and physical (partition, shard, etc...) locations.

## Technologies

- Document-based databases
  - [MongoDB](https://www.mongodb.com/)
  - [Couchbase](https://www.couchbase.com/products/server/)
  - [CouchDB](https://couchdb.apache.org/)
- Key-value databases
  - [Redis](https://redis.io/)
  - [Memcached](https://memcached.org/)
  - [etcd](https://etcd.io/)
- Wide-column databases
  - [Cassandra](https://cassandra.apache.org/)
  - [HBase](https://hbase.apache.org/)
  - [ScyllaDB](https://www.scylladb.com/)
- Graph databases
  - [Neo4j](https://neo4j.com/product/neo4j-graph-database/)
  - [Amazon Neptune](https://aws.amazon.com/neptune/)
  - [JanusGraph](https://janusgraph.org/)
- Time-series databases
  - [InfluxDB](https://www.influxdata.com/)
  - [Prometheus](https://prometheus.io/)
  - [TimescaleDB](https://www.timescale.com/)
- Column-oriented databases
  - [Cassandra](https://cassandra.apache.org/)
  - [HBase](https://hbase.apache.org/)
  - [GCP BigTable](https://cloud.google.com/bigtable)

## External references

- [What is a NoSQL database?](https://cloud.google.com/discover/what-is-nosql)
- [What Is BASE in database engineering?](https://www.lifewire.com/abandoning-acid-in-favor-of-base-1019674)
- [What’s the difference between an ACID and a BASE database?](https://aws.amazon.com/compare/the-difference-between-acid-and-base-database/)
- [SQL vs. NoSQL Explained](https://www.youtube.com/watch?v=_Ss42Vb1SU4&ab_channel=Exponent)
- [7 Database Paradigms](https://www.youtube.com/watch?v=W2Z7fbCLSTw&ab_channel=Fireship)
- [What are time Series databases?](https://www.youtube.com/watch?v=vuNEWixlsWY&ab_channel=CodewithIrtiza)
- [Graph Databases Will Change Your Freakin' Life (Best Intro Into Graph Databases)](https://www.youtube.com/watch?v=GekQqFZm7mA&ab_channel=CodingTech)
- [Column vs Row Oriented Databases Explained](https://www.youtube.com/watch?v=Vw1fCeD06YI&ab_channel=HusseinNasser)
- [Database denormalization](https://www.youtube.com/watch?v=4bTq0GdSeQs&t=8s&ab_channel=Decomplexify)
