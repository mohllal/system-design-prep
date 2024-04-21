# Load Balancing

Load balancing is used to efficiently distribute incoming network traffic across multiple servers or resources. It ensures that no single resource becomes overwhelmed, thus improving the reliability, availability, and scalability of the system.

## Scaling strategies

- Vertical scaling (a.k.a scaling up): involves adding more resources, such as CPU, memory, or storage, to a single server to handle increased load.
- Horizontal scaling (a.k.a scaling out): involves adding more machines or instances to distribute the load across multiple servers.

## Load balancing algorithms

- Round robin: requests are distributed evenly across a group of servers in a circular order.
- Least connections: requests are routed to the server with the fewest active connections, aiming to balance the load more evenly.
- IP hash: the client's IP address is used to determine which server receives the request. This ensures that a particular client consistently communicates with the same server, useful for maintaining session state.
- Weighted round robin: assigns a weight to each server based on its capacity, allowing more powerful servers to handle proportionally more requests.
- Least response time: routes traffic to the server with the shortest response time, aiming to optimize performance.
- Adaptive load balancing: utilizes real-time monitoring and analysis of server performance to dynamically adjust routing decisions based on factors like server health, response time, and capacity.

## Load balancer types

- Hardware load balancer: dedicated physical devices optimized for high-performance load balancing.
- Software load balancer: load balancing implemented in software, often as part of application delivery controllers or reverse proxies.
- Cloud load balancer: Load balancing services provided by cloud providers, offering scalability, reliability, and integration with other cloud services.

## Active-Active vs Active-Passive

- In an active-active load balancing setup, all nodes or instances in the system are actively handling requests simultaneously. Traffic is distributed evenly across all available servers.

- In an active-passive load balancing setup, one node is active and handling all requests while one or more passive nodes are on standby. The passive nodes take over if the active node fails (failover).

Active-Active:

- Availability: high; all nodes are active and handle traffic.
- Performance: better load distribution and resource utilization.
- Complexity: more complex; requires synchronization and data consistency.
- Scalability: easier to scale horizontally by adding more nodes.
- Cost: generally higher, as all nodes are in use.

Active-Passive:

- Availability: lower; one active node, others are standby.
- Performance: single node handles traffic, passive nodes are idle.
- Complexity: simpler to manage; no need for synchronization.
- Scalability: limited by the capacity of the active node; failover to standby nodes.
- Cost: potentially lower, with fewer active resources.

## External references

- [Horizontal scaling vs vertical scaling: Choosing your strategy](https://www.digitalocean.com/resources/article/horizontal-scaling-vs-vertical-scaling)
- [What Is Load Balancing?](https://www.nginx.com/resources/glossary/load-balancing/)
- [Active-Active vs Active-Passive Cluster to Achieve High Availability in Scaling Systems](https://www.youtube.com/watch?app=desktop&v=d-Bfi5qywFo&ab_channel=HusseinNasser)
- [Fail-over and High-Availability (Explained by Example)](https://www.youtube.com/watch?v=Zgy1miPsTNs&ab_channel=HusseinNasser)
