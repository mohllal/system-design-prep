# Peer-to-Peer (P2P) Networks

Peer-to-peer (P2P) networks are decentralized networks where each participant (peer) acts as both a client and a server. Unlike traditional client-server models, P2P networks distribute the workload among all peers, increasing redundancy and reducing single points of failure.

## Use Cases

- File sharing: torrents for distributing large files.
- Content distribution: platforms like IPFS.
- Communication: P2P messaging apps like Skype.
- Cryptocurrency: decentralized transaction management (e.g., Bitcoin).

## Types of P2P networks

- Pure P2P: no central server or authority; all nodes have equal roles.
- Hybrid P2P: contains central servers or super peers that facilitate connections but does not store data.
- Overlay P2P: create a virtual network on top of an existing network (usually the Internet). Overlay P2P networks often employ distributed hash tables (DHTs) or other routing mechanisms to locate and retrieve resources efficiently.

## Distributed hash tables (DHTs)

DHTs are structured routing algorithms that use a distributed hash table to map keys to peers responsible for storing corresponding data.

Examples include [Chord](https://en.wikipedia.org/wiki/Chord_(peer-to-peer)), [Kademlia](https://en.wikipedia.org/wiki/Kademlia), and [Pastry](https://en.wikipedia.org/wiki/Pastry_(DHT)). DHTs organize peers into a structured overlay network, providing efficient and scalable routing with logarithmic time complexity for resource location.

## Gossip protocol

The gossip protocol is a decentralized peer-to-peer communication technique to transmit messages in an enormous distributed system.

The key concept of gossip protocol is that every node periodically sends out a message to a subset of other random nodes. The entire system will receive the particular message eventually with a high probability.

### Trade-offs

P2P networks provide benefits:

- Fault-tolerant: messages flow via many routes making it tolerant against unreliable networks.
- Scalable: each server interacts with a limited number of servers and sends a fixed number of messages.
- Decentralized: it uses a peer-to-peer communication model.

But come with costs:

- Eventually consistent: it’s slower compared to multicast. Also gossip behavior depends on network topology.
- Difficult to debug and test: non-deterministic and distributed nature makes it hard to debug and reproduce failures.
- High bandwidth usage: A single message will get sent many times across different servers.

### Use cases in P2P networks

- Membership management: nodes in a P2P network use gossip protocols to maintain an updated list of active nodes. This helps new nodes to discover existing nodes and integrate into the network.
- Data dissemination: gossip protocols can be used to propagate updates, such as new files or version changes, across the network efficiently.
- Load balancing: nodes can use gossip to share load information and redistribute tasks dynamically to balance the load across the network.
- Failure detection and recovery: gossip protocols help in detecting node failures and disseminating this information to other nodes so they can take corrective actions, such as rerouting data.

### Gossip Algorithm

The high-level overview of the gossip algorithm is the following:

1. Information dissemination
    - Each node periodically selects one or more random nodes from the network and exchanges information with them.
    - The information exchange can include data updates, membership lists, or other state information.
    - After the exchange, both nodes update their information based on what they have received.
2. Random node selection
    - Nodes select peers at random to communicate with periodically.
    - This randomness ensures that the information spreads quickly and uniformly throughout the network.
3. Convergence
    - Over time, as nodes continue to gossip, the entire network converges to a consistent state where all nodes have received the updated information.
    - The speed of convergence depends on the frequency of gossip exchanges (a.k.a cycles) and the number of nodes each node gossips with.
4. Failure detection
    - Gossip protocols can be used for failure detection by having nodes periodically share lists of known active nodes.
    - If a node fails to hear about another node after several rounds of gossip, it can assume that the node has failed and propagate this information.

## External references

- [How Peer to Peer (P2P) Network works](https://www.youtube.com/watch?v=2v6KqRB7adg&ab_channel=ByteMonk)
- [Bittorrent & P2P - Peer-to-Peer Network Applications](https://www.youtube.com/watch?v=23uTlbdCKbw&ab_channel=EpicNetworksLab)
- [Kademlia - a Distributed Hash Table implementation to power the overlay network of BitTorrent](https://www.youtube.com/watch?v=_kCHOpINA5g&ab_channel=ArpitBhayani)
- [Everything You Need to Know About Gossip Protocol](https://newsletter.systemdesign.one/p/gossiping-protocol)
- [Gossip Protocol Explained](https://highscalability.com/gossip-protocol-explained/)
- [Introduction to Gossip](https://managementfromscratch.wordpress.com/2016/04/01/introduction-to-gossip/)
- [Understanding gossip protocols](https://www.youtube.com/watch?v=QQ2n1UX3Qwg&ab_channel=CodeSync)