# Microservices Architecture

Microservice is an architectural style to developing a single application as a suite of small and independent services, each running in its own process and communicating with lightweight mechanisms, often an HTTP resource API, messaging queues, or event buses.

These services are built around business capabilities and independently deployable by fully automated deployment pipelines. There is a bare minimum of centralized management of these services, which may be written in different programming languages and use different data storage technologies.

## Characteristics

- Decentralized data management: each microservice manages its own database, enabling it to be independent and loosely coupled from other services.
- Independent deployability: microservices can be deployed independently, allowing for continuous deployment and faster iterations.
- Organized around business capabilities: each microservice is aligned with a specific business capability, making it easier to develop, understand, and maintain.
- Smart endpoints and dumb pipes: endpoints/services that produce and consume messages are smart, but the pipe between the endpoints is dumb. In other words, concentrate the logic in the containers and refrain from leveraging (and coupling to) sophisticated buses and messaging services.
- Design for failure: services are designed so that they can tolerate the failure of other services. Any service call could fail due to unavailability of the supplier, the client has to respond to this as gracefully as possible.

## Communication patterns

- Synchronous Communication: typically done via HTTP/REST or gRPC where one service directly requests data or execute a task from another.
- Asynchronous Communication: utilizes message queues or event streaming platforms (like Kafka), which allows for more resilient and decoupled systems.
- Choreography vs. orchestration: in microservices, communication can be managed through choreography (where services react to events) or orchestration (where a central service directs the workflow).

## Trade-offs

Microservices provide benefits:

- Strong module boundaries: microservices reinforce modular structure.
- Independent deployment: small services are easier to deploy, and since they are autonomous, are less likely to cause system failures when they go wrong.
- Technology diversity: microservices can be developed in multiple languages, development frameworks and data-storage technologies.

But come with costs:

- Distributed systems: Distributed systems are harder to program, since remote calls are slow and are always at risk of failure.
- Eventual consistency: maintaining strong consistency is extremely difficult for a distributed system, which means every service has to manage eventual consistency.
- Operational complexity: Services are redeployed regularly which comes with extra operational overhead requiring robust automation, monitoring, and orchestration tools.

## Microservice granularity

Microservice granularity specifies the scope of business functionality in a service operation.

By definition a coarse-grained service operation has broader scope than a fine-grained service, although the terms are relative. The former typically requires increased design complexity but can reduce the number of calls required to complete a task.

The guiding principle is that a microservice should be small enough to encapsulate a single business capability but large enough to manage its responsibilities independently.

## References

- [Microservices](https://martinfowler.com/articles/microservices.html)
- [Microservice Prerequisites](https://martinfowler.com/bliki/MicroservicePrerequisites.html)
- [Polyglot Persistence](https://martinfowler.com/bliki/PolyglotPersistence.html)
- [Microservice Trade-Offs](https://martinfowler.com/articles/microservice-trade-offs.html)
- [Microservice Premium](https://martinfowler.com/bliki/MicroservicePremium.html)
- [How to break a Monolith into Microservices](https://martinfowler.com/articles/break-monolith-into-microservices.html)
- [Microservice Testing](https://martinfowler.com/articles/microservice-testing)
- [Microservice Principles: Smart Endpoints and Dumb Pipes](https://medium.com/@nathankpeck/microservice-principles-smart-endpoints-and-dumb-pipes-5691d410700f)
- [Monolith First](https://martinfowler.com/bliki/MonolithFirst.html)
- [Microservices by Martin Fowler](https://www.youtube.com/watch?v=wgdBVIX9ifA)
- [Don’t start with a monolith when your goal is a microservices architecture](https://martinfowler.com/articles/dont-start-monolith.html)
