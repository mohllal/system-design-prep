# Saga

The Saga is a pattern used to manage distributed transactions, ensuring data consistency across multiple microservices.

Unlike traditional ACID transactions that are handled within a single database, Sagas are long-running transactions that span multiple services, each executing a series of operations with compensating actions to maintain data integrity in case of failures.

Each step of a Saga that is followed by a step that can fail (for business reasons) must have a corresponding compensating transaction.

## Types

### Choreography-based Sagas

Choreography is an event-driven approach. When using choreography, there isn’t a central coordinator telling the Saga participants what to do. Instead, the Saga participants subscribe to each other’s events and respond accordingly.

When a service updates its data, it simply emits a domain event announcing what it has done. Other services subscribe to those events, which trigger updates that emit additional events.

Trade-offs:

- No centralized logic: this can be good and bad
- Useful for small/simple workflows
- Difficult to conceptualize if a lot of services are involved.
- Harder to debug & test if a lot of services are involved

### Orchestration-based Sagas

Orchestration uses a Saga orchestrator (object) that tells the Saga participants what to do. The Saga orchestrator communicates with the participants using request/asynchronous, point-to-point asynchronous communication using a message broker, response-style interaction.

To execute a Saga step, it sends a command message to a participant telling it what operation to perform. After the Saga participant has performed the operation, it sends a reply message to the orchestrator. The orchestrator then processes the reply message and determines which Saga step to perform next and handles compensations if any step fails.

Trade-offs:

- Centralized logic: this can be good and bad
- Easier to understand the workflow since its defined in a central location
- Full control over the workflow steps via commands
- Single point of failure
- Easier to debug and test

## Considerations

### Why two-phase commit (2PC) isn't an option?

- 2PC can become a bottleneck in large-scale distributed systems due to its tightly coupled synchronous model.
- The coordinator in 2PC is a single point of failure. If it fails, participants can be left in a blocked state which reduces the overall system throughput.
- The need for multiple round-trips to achieve consensus among all participants introduces significant latency.

In the context of the CAP theorem, 2PC prioritizes Consistency (C) over Availability (A) and Partition Tolerance (P) while Saga pattern prioritizes Availability (A) and Partition Tolerance (P) over immediate Consistency (C).

### How to atomically update the database and send messages to a message broker within each Saga step?

In both types of Sagas, each step of Saga updates a database (e.g. a business object or Saga orchestrator) and sends a message/event to a message broker.

These two actions must be done atomically in order to avoid data inconsistencies and bugs. For example, if a service sent a message after committing the database transaction there is a risk of it crashing before sending. Similarly, if a service sends a message in the middle of a database transaction there is a risk that the transaction will be rolled back. In both scenarios, the application is left in an inconsistent state.

This usually approached by using of these patterns:

- Transactional Outbox pattern: publish a message by inserting it into a table (e.g. named `OUTBOX`, `EVENTS`, or any other name) as part of the database transaction. A separate process retrieves the message from that table and sends it to the message broker.

- Event Sourcing pattern: uses events to persist domain objects and thereby combines updating the database and publishing events into a single, inherently atomic operation.

## References

- [Pattern: Saga](https://microservices.io/patterns/data/Saga.html)
- [Managing data consistency in a microservice architecture using Sagas - Implementing a choreography-based Saga](https://microservices.io/post/sagas/2019/08/15/developing-sagas-part-3.html)
- [Managing data consistency in a microservice architecture using Sagas - Implementing an orchestration-based Saga](https://microservices.io/post/sagas/2019/12/12/developing-sagas-part-4.html)
- [Microservice Orchestration Vs Choreography](https://softobiz.com/microservice-orchestration-vs-choreography/)
- [Data Consistency in Microservice Using Sagas](https://www.youtube.com/watch?v=txlSrGVCK18&ab_channel=InfoQ)
- [Applying the Saga Pattern](https://www.youtube.com/watch?v=xDuwrtwYHu8&ab_channel=GOTOConferences)
- [Event Choreography & Orchestration (Sagas)](https://codeopinion.com/event-choreography-orchestration-sagas/)
