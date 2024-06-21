# Leader Election

Leader election is the process of designating a single node as the organizer of some task distributed among several servers (nodes). It's crucial for achieving consistency and coordination in distributed systems.

## Use cases

- Distributed databases: coordinating writes to avoid conflicts.
- Cluster management: ensuring only one master node is active.
- Consensus Algorithms: facilitating agreement on shared state.

## Consensus algorithms

### Paxos algorithm

A consensus algorithm where a leader is elected as a part of achieving consensus.

Steps:

1. A proposer (potential leader) sends a proposal with a unique ID to a majority of acceptors.
2. If acceptors agree, they send a promise not to accept lower IDs.
3. The proposer then sends a commit message.

### Raft algorithm

Raft achieves consensus via an elected leader. A server in a raft cluster is either a leader or a follower, and can be a candidate in the precise case of an election (leader unavailable). The leader is responsible for log replication to the followers.

It regularly informs the followers of its existence by sending a heartbeat message. Each follower has a timeout (typically between 150 and 300 ms) in which it expects the heartbeat from the leader. The timeout is random in each node in the cluster and is reset on receiving the heartbeat. If no heartbeat is received the follower changes its status to candidate and starts a leader election.

Steps:

1. Nodes start in a follower state.
2. If no heartbeat is received, a follower becomes a candidate and starts an election.
3. Candidates request votes from other nodes.
4. A node never vote a candidate node if the candidate node has a shorter log than itself to prevent nodes with stale data becoming leaders.
5. If a candidate receives a majority of votes, it becomes the leader.

## Leases

Leases are another approach to leader election that provides a time-bound leadership.

### How Leases Work

Lease: A time-bound permission granted to a node to act as the leader.

Steps:

1. Nodes contend for a lease from a lease manager (often implemented via a distributed lock service).
2. The lease manager grants a lease to one of the contenders, making it the leader for a fixed duration.
3. The leader must renew its lease periodically to maintain leadership.
4. If the lease expires and is not renewed, other nodes can contend for a new lease.

### Example: Using etcd for lease-based leader election

```python
import etcd3
import threading
import time

ETCD_HOST = '127.0.0.1'
ETCD_PORT = 2379
LEASE_TTL = 10
LEADER_KEY = '/election/leader'

def elect_leader():
  etcd = etcd3.client(host=ETCD_HOST, port=ETCD_PORT)
  lease = etcd.lease(LEASE_TTL)

  def renew_lease():
    print("Renewing the leader lease...")
    
    while True:
      time.sleep(LEASE_TTL / 2)
      lease.refresh()

  leader_thread = threading.Thread(target=renew_lease)
  leader_thread.daemon = True

  while True:
    try:
      etcd.put(LEADER_KEY, 'leader', lease=lease)
      print("I am the leader")
      leader_thread.start()

      while True:
        time.sleep(1)
    except etcd3.exceptions.LeaseExistError:
      print("I am a follower")
      time.sleep(LEASE_TTL)


if __name__ == "__main__":
  elect_leader()
```

## Technologies

- [Zookeeper](https://zookeeper.apache.org/) - a centralized service for maintaining configuration information, naming, and providing distributed synchronization.
- [etcd](https://etcd.io/) - a distributed, reliable key-value store.

## External references

- [Leader Election in Distributed Systems](https://aws.amazon.com/builders-library/leader-election-in-distributed-systems/)
- [Exploring the Role of Consensus Algorithms in Distributed System Design](https://dzone.com/articles/exploring-the-role-of-consensus-algorithms-in-dist)
- [Distributed Systems: Consensus](https://www.youtube.com/watch?v=rN6ma561tak&ab_channel=MartinKleppmann)
- [Consensus in Distributed Systems](https://w3.cs.jmu.edu/kirkpams/OpenCSF/Books/csf/html/DistConsensus.html)
- [Paxos Made Moderately Complex](https://paxos.systems/how/)
- [The Paxos Algorithm](https://www.youtube.com/watch?v=d7nAGI_NZPk&ab_channel=GoogleTechTalks)
- [The Raft Consensus Algorithm](https://raft.github.io/)
- [Understand RAFT without breaking your brain](https://www.youtube.com/watch?v=IujMVjKvWP4&ab_channel=CoreDump)
- [Understanding the Raft consensus algorithm: an academic article summary](https://www.freecodecamp.org/news/in-search-of-an-understandable-consensus-algorithm-a-summary-4bc294c97e0d/)
- [Paxos vs Raft: Have we reached consensus on distributed consensus?](https://www.youtube.com/watch?v=JQss0uQUc6o&t=138s&ab_channel=PaPoCWorkshop)
- [What is etcd?](https://www.youtube.com/watch?v=OmphHSaO1sE&t=297s&ab_channel=IBMTechnology)
