# Caching

Caching is the practice of storing frequently accessed data in a temporary storage area to reduce access latency, improve performance, and alleviate the load on primary data sources.

Caching can be useful in scenarios where:

- There's a significant discrepancy in access times between primary storage and cache.
- The data is accessed frequently or repeatedly.
- The cost of fetching data from the primary source is high.
- The data is relatively static or doesn't change frequently.

## Caching types

There are several caching types:

- In-Memory caching: data is stored in volatile memory, providing fast access but limited by memory size.
- Distributed caching: data is stored across multiple nodes in a distributed system, enhancing scalability and fault tolerance.
- Client-side caching: data is cached on the client side, reducing the need for server requests.
- Web caching: intermediate caching performed by proxy servers or content delivery networks (CDNs) to serve content closer to the client.

## Data staleness

Data staleness refers to the outdatedness of cached data compared to the primary data source. It can be mitigated using strategies like:

- Expiration time: setting a time limit for cached data validity.
- Cache invalidation: explicitly invalidating or updating cached data when changes occur in the primary source.
- versioning: associating a version number with cached data to detect staleness.

## Caching strategies

### Writing strategies

- Write-through cache: data is written to both the cache and the primary data source simultaneously.
  Optimizes write performance but may introduce latency due to synchronous writes.
- Write-back cache: data is initially written only to the cache and asynchronously written to the primary data source.
  Improves write performance by decoupling write operations from primary storage but carries a risk of data loss or inconsistency.
- Write-around cache: data is written directly to the primary data source and only the data which is read is stored in the cache.
  Prevents cache pollution with infrequently accessed data but may increase read latency for data not present in the cache.

### Read strategies

- Read-through cache: automatically retrieves data from the cache if available (cache hit), fetching from the primary data source only when not (cache miss) then cache the missing data before return it to the client.
  Optimizes read performance by minimizing cache misses but may introduce latency for cache misses, data that has never been cached before, especially if fetching data from the primary source is slow and lacks control over caching policies.
- Cache-aside (lazy loading): requires explicit management by the application, which decides when to retrieve data from the cache or the primary data source based on cache misses.
  Offers flexibility and control over caching decisions but requires additional code complexity and risks cache inconsistency without careful management.

The main differences between read-through and cache-aside are:

- In a cache-aside strategy the application is responsible for fetching the data and populating the cache, while in a read-through, the logic is done automatically by a library or by the cache itself.
- Unlike cache-aside, the data model in read-through cache cannot be different than that of the database.

## Caching eviction policies

Eviction policies determine which items are removed from the cache when it reaches capacity.

- Least recently used (LRU): evicts the least recently used items and is typically implemented using a timestamp for last access.
- Most recently used (MRU): evicts the most recently used items. It is the opposite of least recently used (LRU) policy.
- Least frequently used (LFU): evicts the least frequently accessed items and is typically implemented using a number of accesses counter.

## External references

- [Caching Best Practices](https://aws.amazon.com/caching/best-practices/)
- [Introduction to Database Caching](https://www.prisma.io/dataguide/managing-databases/introduction-database-caching)
- [Cache Eviction Policies](https://www.codecademy.com/article/cache-eviction-policies)
- [The Complexity of Caching](https://codeopinion.com/the-complexity-of-caching/)
- [Cache invalidation isn’t a hard problem](https://codeopinion.com/cache-invalidation-isnt-a-hard-problem/)
