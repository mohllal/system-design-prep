# Latency and Throughput

Latency and throughput are key metrics used to measure network/system performance.

They work together to determine overall connectivity and efficiency, with each affecting the other.

## Latency

**Definition**: Time delay between sending a request and receiving a response.

**Measured in**: Milliseconds (ms) or microseconds (μs)

**Components of Latency:**

```mermaid
graph TD
    A[Total Latency] --> B[Propagation Delay]
    A --> C[Transmission Delay] 
    A --> D[Processing Delay]
    A --> E[Queuing Delay]
    
    B --affected by--> F[Distance / Signal speed]
    C --affected by--> G[Packet size / Bandwidth]
    D --affected by--> H[Server processing time]
    E --affected by--> I[Network congestion]
```

**Types:**

- **Network Latency**: Time for packet to travel across network
- **Application Latency**: Time for application to process request
- **Database Latency**: Time for database query execution
- **Disk Latency**: Time for disk I/O operations

## Throughput

**Definition**: Amount of data successfully transmitted/processed per unit time.

**Measured in**: bits per second (bps), megabits per second (Mbps), or gigabits per second (Gbps)

**Key Distinction:**

- **Bandwidth**: Theoretical maximum capacity of a link/system
- **Throughput**: Actual data transfer/processing rate achieved
- **Goodput**: Useful data transfer/processing rate (excluding headers, retransmissions, errors)

```mermaid
graph LR
    A[Bandwidth<br/>100 Mbps] --> B[Throughput<br/>85 Mbps]
    B --> C[Goodput<br/>75 Mbps]
```

## Relationship Between Latency and Throughput

**Common Misconception**: High bandwidth always means low latency

**Reality**: They measure different aspects and can be independent

```mermaid
graph TD
    A[Network Performance] --> B[Latency<br/>How fast?]
    A --> C[Throughput<br/>How much?]
    
    B --> D[User Experience<br/>Responsiveness]
    C --> E[System Capacity<br/>Total volume]
```

## Performance Trade-offs

### Latency vs Throughput Examples

```mermaid
quadrantChart
    title Network Performance Characteristics
    x-axis Low Latency --> High Latency
    y-axis Low Throughput --> High Throughput
    
    quadrant-1 High Throughput, Low Latency
    quadrant-2 High Throughput, High Latency  
    quadrant-3 Low Throughput, High Latency
    quadrant-4 Low Throughput, Low Latency
    
    Gaming/Trading: [0.1, 0.2]
    Video Streaming: [0.6, 0.9]
    File Downloads: [0.8, 0.9]
    IoT Sensors: [0.3, 0.1]
```

## Optimization Strategies

### Reducing Latency

**Geographic Distribution:**

- Content Delivery Networks (CDNs)
- Edge computing and regional data centers
- Anycast routing

**Protocol Optimization:**

- HTTP/2 multiplexing (eliminates head-of-line blocking)
- HTTP/3 with QUIC (faster connection setup)
- Connection pooling and keep-alive

**Application-Level:**

- Database query optimization
- Efficient processing of requests
- Asynchronous processing

**Network-Level:**

- Reduce network hops
- Quality of Service (QoS) prioritization
- Direct peering agreements

### Improving Throughput

**Bandwidth Scaling:**

- Increase link capacity
- Load balancing across multiple connections
- Network interface bonding

**Compression:**

- gzip/brotli for text content
- Image and video compression
- Protocol-level compression

**Concurrency:**

- Parallel processing
- Pipelining requests
- Batch operations

**Caching Strategies:**

- Browser caching
- Reverse proxy caching
- Application-level caching

## Real-World Considerations

### Bandwidth-Delay Product

**Formula**: Bandwidth × Round-Trip Time = Amount of data "in flight"

**Impact**: Determines optimal TCP window size and buffer requirements

```mermaid
graph LR
    A[1 Gbps Link] --> B[100ms RTT]
    B --> C[12.5 MB in flight]
    C --> D[Need large buffers]
```

### Little's Law Application

**Formula**: Average number of requests = Arrival rate × Average response time

**Usage**: Capacity planning and performance modeling

### Common Bottlenecks

**Latency Bottlenecks:**

- Physical distance (speed of light limitation)
- DNS resolution time
- TLS handshake overhead
- Database query performance

**Throughput Bottlenecks:**

- Network bandwidth limits
- Server CPU/memory resources
- Database connection pools
- Disk I/O capacity

## Further References

- [What's the Difference Between Throughput and Latency?](https://aws.amazon.com/compare/the-difference-between-throughput-and-latency/)
- [CDN Performance Optimization](https://www.cloudflare.com/learning/cdn/performance/)
