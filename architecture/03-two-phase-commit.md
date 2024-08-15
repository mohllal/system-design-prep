# Two-Phase Commit (2PC)

Two-phase commit (2PC) is a distributed protocol that ensures all nodes in a distributed database either commit or roll back a transaction, achieving atomicity and durability in the face of failures for transactions that access data in multiple partitions or shards across different notes.

The essence of two-phase commit is that it carries out an update in two phases:

1. The prepare phase asks each node if it can promise to carry out the update.
2. The commit phase actually carries it out.

## Roles

- Coordinator: manages the transaction, sends prepare and commit/abort requests (which may be one of the participants involved in that transaction).
- Participants: the nodes or database instances that execute the transaction.

## Phases

### Prepare

1. The coordinator sends a `prepare` request to all participants.
2. Each participant performs the transaction locally, acquires the needed locks, writes the changes to a temporary durable storage (not yet committed e.g. WAL) and replies to the coordinator with either a `vote-commit` or `vote-abort` (if not succeed to do so).

### Commit

1. If the coordinator receives `vote-commit` from all participants, it sends a `commit` request.
2. If any participant votes to abort, the coordinator sends an `abort` request.
3. Participants complete the transaction by either committing or rolling back the transaction based on the coordinator’s instruction.

Note: in addition to durably writing the writes that are directly required by the transaction, the protocol itself requires additional writes that must be made durable before it can proceed. For example, a participant has veto power until the point it votes `vote-commit` in the `prepare` phase and after that point, it cannot change its vote.

But what if it crashes right after voting `vote-commit`? When it recovers it might not know that it voted `vote-commit`, and still think it has veto power and go ahead and abort the transaction via `vote-abort`. To prevent this, it must write its vote durably before sending the `vote-commit` vote back to the coordinator.

## Problems

### Blocking

If the coordinator fails during the commit phase, participants may remain in a locked state, unable to commit or abort the transaction.

If the coordinator fails before sending a message with the final decision to (at least one) participant/s, the participants cannot get together to make a decision amongst themselves - they can’t abort because maybe the coordinator decided to commit before it failed, and they can’t commit because maybe the coordinator decided to abort before it failed.

Thus, they have to block, wait until the coordinator recovers, in order to find out the final decision. In the meantime, they cannot process transactions that conflict with the stalled transaction since the final outcome of the writes of that transaction are yet to be determined.

One popular workaround for that is achieved by running 2PC over replica consensus protocols and ensuring that important state for the protocol is replicated at all times across different nodes. Unfortunately, this reduces performance, since the protocol requires that these replica consensus rounds occur sequentially, and thus they may add significant latency to the protocol.

### Latency

Introduces latency due to the two-phase communication process.

This latency increase alone can already be an issue for many applications, but a potentially larger issue is that participant nodes do not know the final outcome of a transaction until mid-way through the second phase. Until they know the final outcome, they have to be prepared for the possibility that it might abort, and thus they typically prevent conflicting transactions from making progress until they are certain that the transaction will commit.

## References

- [Two-Phase Commit by Martin Fowler](https://martinfowler.com/articles/patterns-of-distributed-systems/two-phase-commit.html)
- [Starbucks Does Not Use Two-Phase Commit](https://www.enterpriseintegrationpatterns.com/ramblings/18_starbucks.html)
- [Two Phase Commit - Distributed Transactions](https://www.youtube.com/watch?v=7DoT2sTGulc&ab_channel=Jordanhasnolife)
- [Notes about "Distributed Transactions" Lecture by MIT](https://timilearning.com/posts/mit-6.824/lecture-12-distributed-transactions/)
- [It’s Time to Move on from Two Phase Commit](https://dbmsmusings.blogspot.com/2019/01/its-time-to-move-on-from-two-phase.html)
- [Consensus Protocols: Two-Phase Commit](https://www.the-paper-trail.org/post/2008-11-27-consensus-protocols-two-phase-commit/)
