# Event-Driven Architecture (EDA)

Event-Driven Architecture (EDA) is a software architecture paradigm in which services communicate by emitting and responding to events. An event is a significant change in state, such as a user clicking a button, an item being added to a cart, or a transaction being completed.

In EDA, events are the primary means of triggering processing and communicating between decoupled services. Producers of event records often have no knowledge of who the consumers might be, nor whether consumers exist at all.

## Components

- Event producers: emit events when a significant change occurs.
- Event consumers: listen for and process events.
- Event bus or message Broker: facilitates communication between producers and consumers (e.g., Kafka, RabbitMQ).

## Patterns

Several patterns are commonly used in EDA to structure the interaction between services:

- Event notification: a service emits an event to notify other services of a change in state.
- Event-carried state transfer: an event contains the state change information, allowing other services to update their soft state.
- Event sourcing: the state of the system is derived from a sequence of events rather than the current state.
- CQRS (Command Query Responsibility Segregation): separates read and write operations, and more commonly database models, to optimize performance and scalability.

## Trade-offs

EDA provides benefits:

- Scalability: services can scale independently, and event queues can buffer load spikes.
- Loose coupling: services communicate with each other without needing to know the specifics of their implementation.
- Fault tolerance and resilience: since services are decoupled, so failures in one service do not directly impact others.
- Flexibility: easy to add new event consumers without modifying existing producers.
- Asynchronous processing: improves performance by handling tasks asynchronously.

But comes with costs:

- Complexity: increased architectural complexity and need for reliable event delivery.
- Debugging and monitoring: harder to trace the flow of events across services.
- Losing sight of that larger-scale flow: it can be hard see such a flow as it's not explicit in any program text.
- Eventual consistency: data consistency across services can be challenging to manage.

## Use cases

- External integrations with third-part systems where it’s hard to guarantee any type of availability.
- Perform specific business behaviors apart of a long-running business process that span different logical boundaries.
- Notify consumers when state has changed so they can update their copy of the data which can be helpful in having various read models across different consumers.

## Considerations

- Idempotency: ensuring event handlers are idempotent to handle duplicate events, handling at-least-one delivery guarantee, gracefully.
- Event schema: defining a clear and consistent event schema to avoid integration issues.
- Event processing order: ensuring the correct order of processing for events can be challenging, especially when enabling events/messages from message queues, or topics, to be processed concurrently by multiple consumers (also called, competing consumers pattern).

## References

- [Pattern: Event-driven architecture](https://microservices.io/patterns/data/event-driven-architecture.html)
- [What do you mean by “Event-Driven”?](https://martinfowler.com/articles/201701-event-driven.html)
- [Event Collaboration](https://martinfowler.com/eaaDev/EventCollaboration.html)
- [Not Just Events: Developing Asynchronous Microservices](https://www.youtube.com/watch?v=kyNL7yCvQQc&ab_channel=GOTOConferences)
- [Event Driven Architecture, The Hard Parts : Events Vs Messages](https://medium.com/simpplr-technology/event-driven-architecture-the-hard-parts-events-vs-messages-0fcfc7243703)
- [Event-Driven Architecture: I do not think it means what you think it means](https://www.youtube.com/watch?v=iAA7PTqs4xY&ab_channel=CodeOpinion)
- [Introduction to Event-Driven Architecture](https://medium.com/microservicegeeks/introduction-to-event-driven-architecture-e94ef442d824)
- [Commands & Events: What’s the difference?](https://codeopinion.com/commands-events-whats-the-difference/)
- [Event Carried State Transfer: Keep a local cache!](https://codeopinion.com/event-carried-state-transfer-keep-a-local-cache/)
- [Real-World Event Driven Architecture! 4 Practical Examples](https://codeopinion.com/real-world-event-driven-architecture-4-practical-examples/)
- [Event Driven Architecture — 5 Pitfalls to Avoid](https://medium.com/wix-engineering/event-driven-architecture-5-pitfalls-to-avoid-b3ebf885bdb1)
- [How Event Driven Architectures Go Wrong & How to Fix Them](https://www.youtube.com/watch?v=_dbyp3rL_4Q&ab_channel=GOTOConferences)
- [Gotchas! in Event Driven Architecture - What you need to be aware of](https://www.youtube.com/watch?v=NzEaI1UtiGk&ab_channel=CodeOpinion)
