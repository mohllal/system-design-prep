# Layered Architecture

Layered architecture is an architectural style that organizes a system into layers, each with distinct responsibilities.

## Key Layers

A typical layered architecture includes the following layers:

1. **Presentation layer (UI layer)**
   - Responsible for handling the user interface and user experience.
   - Interacts with the user and translates their inputs into actions.
   - Examples: Web UI (React, Angular), mobile apps.

2. **Application layer (Service layer)**
   - Coordinates the business logic and workflow.
   - Handles interactions between the presentation layer and the domain/business layer.
   - Manages use cases and user actions.
   - Examples: REST APIs, microservices.

3. **Domain layer (Business logic layer)**
   - Contains the core business logic and domain rules.
   - Independent of external services like databases or UI frameworks.
   - Central to business rules and decisions.
   - Examples: Business objects, services that implement business logic.

4. **Persistence layer (Data access layer)**
   - Responsible for interacting with databases, file systems, or other storage systems.
   - Encapsulates all database operations (CRUD) and abstracts them from higher layers.
   - Examples: Repositories, DAOs (Data Access Objects).

5. **Infrastructure layer**
   - Supports other layers by providing low-level system services.
   - Manages logging, authentication, network calls, or file system operations.
   - Often used as a utility layer for cross-cutting concerns like security and logging.
   - Examples: Authentication services, file handling, messaging systems.

## Key Principles

1. **Separation of Concerns**
   - Each layer has a well-defined responsibility, isolating changes to specific parts of the system.
   - Example: Changes in the business rules only affect the domain layer.

2. **Loose Coupling**
   - Layers should interact with each other through well-defined interfaces, reducing dependencies.
   - Encourages the substitution of layers without affecting the overall system.

3. **High Cohesion**
   - Each layer should be focused on a single purpose, increasing maintainability and readability.

4. **Dependency Direction**
   - The flow of dependency should go top-down (Presentation -> Application -> Domain -> Persistence).
   - Upper layers depend on lower layers but never vice-versa, ensuring isolation of concerns.

## Open vs Closed Layers

In layered architecture, layers can be categorized as **open** or **closed** based on how they interact with each other:

1. Closed Layers:
   - A closed layer only communicates with the layer directly below it.
   - This strict communication ensures better separation of concerns and modularity.
   - **Example**: The presentation layer can only interact with the application layer, which in turn communicates with the domain layer, and so on.
   - **Benefit**: Clear boundaries and reduced complexity, as each layer relies only on its adjacent lower layer.

2. Open Layers:
   - An open layer can bypass the layer directly below it and communicate with lower layers.
   - This flexibility can sometimes improve performance by skipping unnecessary layers but introduces tighter coupling between layers.
   - **Example**: The presentation layer can directly interact with the persistence layer to fetch data, bypassing the application and domain layers.
   - **Benefit**: Reduced overhead in cases where intermediate layers aren't necessary, but can lead to more fragile systems if overused.

### Choosing Between Open and Closed Layers

- Closed layers are more common in most systems as they enforce a structured flow and are easier to maintain and extend.
- Open layers should be used cautiously, only when there's a significant performance improvement or architectural reason, and should be well-documented to avoid confusing future developers.

## Pros and Cons

### Pros

- Simplicity: Organizing the system into layers provides a clear and logical structure, making it easier to understand and reason about the system.
- Reusability: Layers, especially the business logic, can be reused across different applications or UIs.

### Cons

- Performance overhead: Layering can introduce additional latency, especially if there's heavy inter-layer communication.
- Rigidity: Over-engineering or forcing layers for simple use cases can lead to unnecessary complexity.
- Scalability limitations: Without proper attention, tightly coupling layers can limit the system's scalability.

## Best Practices

1. Define clear boundaries:
    - Each layer should have a well-defined purpose and avoid leaking responsibilities across layers.
2. Use dependency injection:
    - Implementing DI to inject dependencies between layers, enabling better testability and flexibility.
3. Handle cross-cutting concerns separately:
    - Using the infrastructure layer for logging, security, or caching to prevent them from mixing with business logic.
4. Keep layers thin:
   - Avoid overloading layers with logic that doesn’t belong. For instance, keep validation logic in the domain layer and UI rendering logic in the presentation layer.

## References

- [N-tier architecture](https://www.karanpratapsingh.com/courses/system-design/n-tier-architecture)
- [N-tier architecture style by Microsoft](https://learn.microsoft.com/en-us/azure/architecture/guide/architecture-styles/n-tier)
- [Understanding Layered Software Architecture](https://systemdesignschool.io/blog/layered-software-architecture)
- [Lesson 158 - Layered Architecture](https://www.youtube.com/watch?v=Y9bKZCYxFuI&ab_channel=MarkRichards)
