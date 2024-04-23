# Hashing

Hashing is a process of converting input data, or keys, into a fixed-size value or hash code. It's a one-way function, meaning it's practically impossible to reverse-engineer the original input from the hash.

## Popular hashing algorithms

- Message Digest Algorithm 5 (MD5): Produces a 128-bit hash value. Widely used but considered weak for security purposes due to vulnerabilities.
- Secure Hash Algorithm (SHA) family: Includes SHA-1, SHA-256, SHA-512, SHA-3, and others. These algorithms produce hash values of varying lengths and are widely used for cryptographic applications due to their stronger resistance to collisions compared to MD5 and SHA-1.

## Hashing uniformity

Hashing uniformity refers to the ability of a hashing function to evenly distribute data values over its output hash space. A good hashing algorithm should aim for uniformity to minimize collisions and ensure efficient storage for its output hash space.

## Hashing techniques

- Consistent hashing: used in distributed systems, particularly for load balancing and caching. It ensures that when the hash table is resized, only a minimal number of keys need to be remapped.

Pseudocode example for the implemention:

```python
def hash(key):
  # Hash function to convert a key to a hash value
  # This could be MD5, SHA-1, or any other suitable hash function
  pass

def add_node(node):
  for i in range(replicas):
    hash_value = hash(node.id + i)
    hash_ring[hash_value] = node

def delete_node(node):
    for i in range(replicas):
      hash_value = hash(node.id + i)
      if hash_value in hash_ring:
        del hash_ring[hash_value]

def get_node_for_key(key):
    hash_value = hash(key)

    # Find the nearest node clockwise on the hash ring
    for hash_key in sorted(hash_ring.keys()):
      if hash_key >= hash_value:
        return hash_ring[hash_key]
  
    # If no node is found, return the first node in the ring
    return hash_ring[list(hash_ring.keys())[0]]
```

- Rendezvous hashing (or Highest Random Weight): a variation of consistent hashing where each node in the system computes a preference list based on the hash values of the keys and chooses the key it prefers the most (e.g. highest weight).

Pseudocode example for the implemention:

```python
def calculate_hash(key, node):
    # Function to calculate the hash value for a key and node
    # Example: return hash(key + node)
    pass

def get_node_for_key(key, nodes):
    max_weight = -1
    selected_node = None

    # Find the node with the highest weight
    for node in nodes:
        weight = calculate_hash(key, node)
        if weight > max_weight:
            max_weight = weight
            selected_node = node
    return selected_node
```

## External references

- [Hashing Algorithms](https://jscrambler.com/blog/hashing-algorithms)
- [A Guide to Consistent Hashing](https://www.toptal.com/big-data/consistent-hashing)
- [Consistent Hashing: Algorithmic Tradeoffs](https://dgryski.medium.com/consistent-hashing-algorithmic-tradeoffs-ef6b8e2fcae8)
