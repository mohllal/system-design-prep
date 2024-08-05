# CAP and PACELC Theorems

CAP and PACELC theorems describe the trade-offs that must be made in distributed systems.

CAP states that only two out of Consistency, Availability, and Partition Tolerance can be achieved during network partitions. PACELC extends this by stating that even without partitions, there is a trade-off between Latency and Consistency.

## CAP theorem

CAP theorem states that a distributed data store can provide only two out of the following three guarantees:

- Consistency (C): every read receives the most recent write or an error.
- Availability (A): every request receives a (non-error) response, without the guarantee that it contains the most recent write.
- Partition tolerance (P): the system continues to operate despite an arbitrary number of messages being dropped (or delayed) by the network between nodes.

### Consistency

Ensures that all nodes see the same data at the same time. This means that if a system is consistent, every read operation will return the most recent write.

For example, in a consistent system, once a record is updated, all subsequent reads will reflect that update.

### Availability

Guarantees that every request receives a response. However, this does not guarantee that the response contains the most recent write.

For example, in an available system, every request will receive a response, but some responses might be outdated if the latest updates have not propagated to all nodes.

### Partition tolerance

The system continues to function even when network partitions occur. This means that the system can tolerate any network failure that doesn't result in a total network collapse. However, it does not mean that all operations will continue as usual without any disruptions.

For example, in a partition-tolerant system, even if some nodes cannot communicate with others due to network issues, the system as a whole will continue to operate.

### Trade-offs

The theorem implies that in the presence of a network partition, one has to choose between consistency and availability:

- CP systems (consistency/partition tolerance): these systems will ensure consistency across all nodes but might not be available for writes and reads during a partition.
  Example: [HBase](https://hbase.apache.org/).

- AP systems (availability/partition Tolerance): these systems will ensure availability of data but might return outdated data during a partition.
  Example: [Cassandra](https://cassandra.apache.org/_/index.html) and [CouchDB](https://couchdb.apache.org/).

- CA systems (consistency/availability): these systems are, theoretically, not possible in a distributed system as they cannot handle partitions while ensuring both consistency and availability.

The choice of which two properties to guarantee often depends on the specific requirements of the application:

- If consistency is crucial (e.g., banking systems), then a CP system might be preferred.
- If availability is crucial (e.g., social media feeds), then an AP system might be preferred.

## PACELC theorem

The PACELC theorem is an extension to the CAP theorem that expands on it by adding another dimension to the trade-off when there is no partition.

It states that in case of network partitioning (P) in a distributed computer system, one has to choose between availability (A) and consistency (C) (as per the CAP theorem), but else (E), even when the system is running normally in the absence of partitions, one has to choose between latency (L) and loss of consistency (C).

### Breakdown of PACELC

- Partition (P):
A/C trade-off (CAP theorem): in the event of a network partition, systems must choose between consistency and availability.

  - Consistency (C): ensuring all nodes have the same data, even if some requests are rejected.
  - Availability (A): ensuring all requests receive a response, even if some responses may not reflect the latest write.

- Else (E):
L/C trade-off: when there is no network partition, systems must choose between latency and consistency.

  - Latency (L): ensuring low response time for requests.
  Example: [Cassandra]((https://cassandra.apache.org/_/index.html)) is considered AP/EL system that prioritizes availability during partitions and low latency when there is no partition.

  - Consistency (C): ensuring all nodes have the same data at the cost of potentially higher response time.
  Example: [HBase](https://hbase.apache.org/) is considered CP/EC system that prioritizes consistency (in both network partition happening or not) over the availability and low-latency.

## External references

- [CAP theorem](https://en.wikipedia.org/wiki/CAP_theorem)
- [PACELC theorem](https://en.wikipedia.org/wiki/PACELC_theorem)
- [CAP Theorem Definition](https://www.scylladb.com/glossary/cap-theorem/)
- [PACELC Theorem Definition](https://www.scylladb.com/glossary/pacelc-theorem/)
- [An Illustrated Proof of the CAP Theorem](https://mwhittaker.github.io/blog/an_illustrated_proof_of_the_cap_theorem/)
- [CAP Theorem Simplified](https://www.youtube.com/watch?v=BHqjEjzAicA&ab_channel=ByteByteGo)
- [Problems with CAP, and Yahoo’s little known NoSQL system](https://dbmsmusings.blogspot.com/2010/04/problems-with-cap-and-yahoos-little.html)
