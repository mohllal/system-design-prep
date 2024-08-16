# Transactional Outbox

The transactional outbox pattern resolves the dual write operations issue that occurs in distributed systems when a single operation involves both a database write operation and a message or event notification.

The Transactional outbox pattern ensures that both the database write and the message sending are coordinated by writing the outgoing message to a dedicated `outbox`, or any other name, table in the same database transaction as the other changes. The outbox table acts as a buffer to ensure that the message will eventually be sent, by another process, even if the initial attempt fails.

## Dual write problem

In distributed systems, there is often a need to update a database and send a message to an external system, like a message broker. If these operations are not atomic, there's a risk that one succeeds while the other fails, leading to inconsistent states.

- If the database update is successful but the event notification or message sending fails, the downstream service will not be aware of the change, and the system can enter an inconsistent state.
- If the database update fails but the event notification or message sending is sent, data could get corrupted, which might affect the reliability of the system.

## Steps

1. Write to outbox: during the business logic execution, when a change is made to the database (e.g., creating an order), a corresponding message (e.g., OrderCreated) is written to the `outbox` table in the same transaction to ensure atomicity.
2. Transaction commit: if the transaction commits successfully, both the database changes and the message in the `outbox` table are persisted.
3. Outbox polling: a background process or a separate service polls the `outbox` table for unsent messages.
4. Send messages: the polling process reads the messages from the `outbox` table, sends them to the message broker, and then marks the messages as sent (or more commonly deletes them).

Here is a pseudocode example of the transactional outbox pattern

```python
def place_order(order_details):
    try:
        # Start a new database transaction
        with db.transaction():
            # Step 1: Write to the Orders table
            order_id = db.insert("Orders", order_details)

            # Step 2: Prepare the message to be sent
            message = {
                "order_id": order_id,
                "event_type": "OrderCreated",
                "payload": order_details,
            }

            # Step 3: Write the message to the Outbox table
            db.insert("Outbox", {"message": json.dumps(message), "status": "pending"})

            # Step 4: Commit the transaction
            db.commit()
    except Exception as e:
        # If any error occurs, rollback the transaction
        db.rollback()
        raise e


# Background process to poll the Outbox table and send messages to the message broker
def poll_outbox():
    while True:
        # Step 1: Fetch pending messages from the Outbox
        messages = db.query("SELECT * FROM Outbox WHERE status = 'pending'")

        for message_record in messages:
            try:
                # Step 2: Send the message to the message broker
                send_to_message_broker(message_record["message"])

                # Step 3: Mark the message as sent (or maybe delete the record)
                db.update("Outbox", message_record["id"], {"status": "sent"})
            except Exception as e:
                # Log the error and continue with the next message
                log_error(f"Failed to send message: {message_record['id']}, error: {e}")

        # Sleep for a while before polling again
        time.sleep(10)


# Function to send the message to a message broker (e.g., Kafka, RabbitMQ)
def send_to_message_broker(message):
    # For example, using Kafka:
    # kafka_producer.send(topic='orders', value=message)
    print(f"Sending message: {message}")
```

## Trade-offs

Transactional outbox pattern provide benefits:

- Atomic operations: ensures that either both the database change and the message are processed, or neither is, avoiding inconsistencies.
- Resiliency: If message delivery fails, the message remains in the outbox table until it is successfully sent.
- Decoupling: decouples the transaction from the message sending process.

But comes with challenges:

- Idempotency: ensuring that message handling is idempotent since messages could be delivered more than once due to retries.
- Messages ordering: ensuring that sending messages or events in the same order in which the service updates the database (usually mitigated by having timestamp and sequence number in the `outbox` records).

## References

- [Pattern: Transactional outbox](https://microservices.io/patterns/data/transactional-outbox.html)
- [Pattern: Transaction log tailing](https://microservices.io/patterns/data/transaction-log-tailing.html)
- [Transactional outbox pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html)
- [Saga Orchestration for Microservices Using the Outbox Pattern](https://www.infoq.com/articles/saga-orchestration-outbox/)
- [Outbox Pattern: Reliably Save State & Publish Events](https://codeopinion.com/outbox-pattern-reliably-save-state-publish-events/)
- [Reliable Microservices Data Exchange With the Outbox Pattern](https://debezium.io/blog/2019/02/19/reliable-microservices-data-exchange-with-the-outbox-pattern/)
