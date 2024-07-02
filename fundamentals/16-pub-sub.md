# Pub/Sub

The Pub/Sub (Publish/Subscribe) is a messaging pattern where senders of messages, called publishers, do not send their messages directly to specific receivers, called subscribers. Instead, messages are published to a topic or channel, and subscribers express interest in one or more topics, receiving only messages that are relevant to them.

## Use cases

- Real-time event streaming: ideal for systems that require real-time data processing, such as monitoring and analytics.
- Microservices communication: facilitates communication between different microservices in a loosely coupled manner.
- Event notification: notify subscribers about state changes in the system.
- Fan-out processing: the process of sending a single message simultaneously to numerous subscribers is known as fan-out processing. It is used in distributing data or events to numerous consumers. For instance, Pub/Sub can be used to fan out data to multiple subscribers, each of which can independently process the data in parallel and feed it to multiple downstream systems.

## Trade-offs

Pub/Sub provides benefits:

- Decoupling: publishers and subscribers are loosely coupled as they don't need to know about each other.
- Scalability: it allows for scalable communication. Multiple subscribers can receive the same message, making it suitable for broadcast-style information dissemination.
- Flexibility: new subscribers can be added without modifying the existing publishers. This makes it easy to extend the system.
- Asynchronous communication: publishers can send messages without waiting for subscribers to process them, leading to better performance and responsiveness.
- Load distribution: by distributing messages among multiple subscribers, the system can balance the load more effectively.

But comes with costs:

- Delivery guarantees: ensuring reliable message delivery (especially exactly-once delivery) can be challenging and may require additional mechanisms like acknowledgments and retries.
- Handling back-pressure: consumers may struggle to keep up with the volume of messages directed to them by the broker, leading to potential overload and failure if not properly managed.

## Components

- Publisher: publisher is the service that sends messages.
- Subscriber: subscriber is the service that receives messages.
- Topic: topic is the subject or the information feed. The publisher can push messages to the topic, which will broadcast messages to the subscribers.
- Message: message holds the received or transmitted data throughout the system.
- Broker: broker is responsible for guiding the messages throughout the system. It acts as a middleman to establish and exchange communication between the publisher and subscriber. It can hold and a list of topics and their respective subscribers, which helps it route the messages received from the publishers to be sent to appropriate subscribers.
- Routing: routing is the process of messages flowing within the system from publishers to subscribers and ensuring message delivery to correct subscribers based on specific subscriptions.

## Delivery semantics

- At-most-once: messages are delivered zero or one time. There is a possibility of message loss but no duplicates.
- At-least-once: messages are delivered one or more times. There is a possibility of duplicate messages, but no message loss.
- Exactly-once: messages are delivered exactly one time, ensuring no duplicates or message loss. This is the most challenging to implement and often requires additional complexity and overhead.

## Idempotent operations

An operation is idempotent if performing it multiple times has the same effect as performing it once.

In the at-least-once message delivery, messages might be delivered more than once due to network issues, retries, or failovers, so idempotency ensures that even if a subscriber processes the same message multiple times, the outcome remains consistent and correct.

## Difference between Pub/Sub and message queuing

- Delivery model: Pub/Sub is often used for broadcast-type communication where messages are sent to multiple subscribers. Message queueing is used for point-to-point communication where messages are delivered to a specific consumer.
- Subscriber knowledge: Pub/Sub does not require publishers to know about specific subscribers, whereas message queueing requires knowledge of both the producer and consumer.
- Message persistence: message queues often doesn't persist messages once they are consumed, whereas in Pub/Sub, message persistence varies by implementation and configuration.

## Technologies

- [Apache Kafka](https://kafka.apache.org/)
- [Google Cloud Pub/Sub](https://cloud.google.com/pubsub?hl=en)
- [AWS SNS](https://aws.amazon.com/sns/)
- [Redis Pub/Sub](https://redis.io/docs/latest/develop/interact/pubsub/)

## External references

- [Pub/Sub Defined](https://redis.io/glossary/pub-sub/)
- [Publish-Subscribe Pattern vs Message Queues vs Request Response](https://www.youtube.com/watch?v=DXTHb9TqJOs&ab_channel=HusseinNasser)
- [Message Ordering in Pub/Sub or Queues](https://codeopinion.com/message-ordering-in-pub-sub-or-queues/)
- [Competing Consumers](https://www.enterpriseintegrationpatterns.com/patterns/messaging/CompetingConsumers.html)
