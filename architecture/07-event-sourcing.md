# Event Sourcing

Event Sourcing is an architectural pattern that is commonly used in distributed systems and event-driven architectures which involves persisting the state of a system as a sequence of immutable events rather than storing the current state directly.

Every change to the state is captured as an event, and these events are stored in an append-only event store. Then the current state of the system can be reconstructed by replaying all the events from the beginning.

## Components

- Event store: a specialized database that holds all the events. It is typically append-only and it will have a unique event stream for each unique business object (similar to SQL/NoSQL records/documents).
- Aggregate: The fundamental unit of consistency in event sourcing, responsible for generating events and applying events, retrieved from the event stream, to build its up-to-date state.

## Trade-offs

Event sourcing provides benefits

- 100% accurate audit logging: with event sourcing, each state change corresponds to one or more events, providing 100% accurate audit logging.
- Easy temporal queries: because event sourcing maintains the complete history of each business object, implementing temporal queries and reconstructing the historical state of an entity is straightforward.

But comes with challenges:

- Event versioning: as the system evolves, the structure of events may change, necessitating careful handling of event versioning and backward compatibility.
- Performance: reconstructing the state from events can become slow if there are many events, requiring the use further optimizations.

## Optimizations

### Snapshotting

Periodically take snapshots of the state to avoid replaying all events from the start in the aggregate.

A generalized flow of using snapshots looks like follows:

1. A new command is received
2. Latest snapshot of an aggregate is read from a snapshot store
3. If the snapshot was found: the aggregate state is set from the snapshot, the aggregate version is set to the snapshot version 
4. All remaining events are read from the event store starting from the current aggregate version
5. The state is updated with remaining events (if any)
6. Command is handled as usual

### Pre-computed projections

One common optimization in Event Sourcing is the use of pre-computed projections. Instead of reconstructing the current state by replaying all past events every time it is needed, a separate data store maintains the current state, which is updated as new events are processed.

This approach eliminates the need to pull all events from the event stream to build the current state on-the-fly. Instead, the current state is pre-computed and stored as events occur. This is usually done asynchronously by a separate process, which listens to the event stream, applies the necessary changes to the state, and updates the projection in the data store.

## Example

Here is an example of implementing Event Sourcing for a warehouse product aggregate using an in-memory event store.

First, defining the events

```python
from abc import ABC
from dataclasses import dataclass
from datetime import datetime

# Define the IEvent interface
class IEvent(ABC):
  pass

# Define specific events using Python's dataclass
@dataclass
class ProductShipped(IEvent):
  sku: str
  quantity: int
  date_time: datetime

@dataclass
class ProductReceived(IEvent):
  sku: str
  quantity: int
  date_time: datetime

@dataclass
class InventoryAdjusted(IEvent):
  sku: str
  quantity: int
  reason: str
  date_time: datetime
```

Second, defining the aggregate

```python
from datetime import datetime
from typing import List


# Define aggregate base class
class Aggregate:
  def __init__(self, id: str):
    self.id = id
    self._all_events: List[IEvent] = []
    self._uncommitted_events: List[IEvent] = []

  def apply_event(self, event: IEvent):
    self._apply(event)
    self._all_events.append(event)

  def add_event(self, event: IEvent):
    self.apply_event(event)
    self._uncommitted_events.append(event)

  def get_uncommitted_events(self) -> List[IEvent]:
    return self._uncommitted_events.copy()

  def get_all_events(self) -> List[IEvent]:
    return self._all_events.copy()

  def events_committed(self):
    self._uncommitted_events.clear()

  def _apply(self, event: IEvent):
    raise NotImplementedError("Each aggregate must implement event application logic.")


class InvalidDomainException(Exception):
  pass

class CurrentState:
  def __init__(self):
    self.quantity_on_hand: int = 0

# Define the WarehouseProduct aggregate class
class WarehouseProduct(Aggregate):
  def __init__(self, id: str):
    self.id = id
    self._current_state = CurrentState()

  def ship_product(self, quantity: int):
    if quantity > self._current_state.quantity_on_hand:
      raise InvalidDomainException("Ah... we don't have enough product to ship?")
    
    self.add_event(ProductShipped(self.id, quantity, datetime.utcnow()))

  def receive_product(self, quantity: int):
    self.add_event(ProductReceived(self.id, quantity, datetime.utcnow()))

  def adjust_inventory(self, quantity: int, reason: str):
    if quantity < 0:
      raise InvalidDomainException("Cannot adjust to a negative quantity.")
    
    self.add_event(InventoryAdjusted(self.id, quantity, reason, datetime.utcnow()))

  def _apply(self, event: IEvent):
    if isinstance(event, ProductShipped):
      self._current_state.quantity_on_hand -= event.quantity
    elif isinstance(event, ProductReceived):
      self._current_state.quantity_on_hand += event.quantity
    elif isinstance(event, InventoryAdjusted):
      self._current_state.quantity_on_hand = event.quantity
    else:
      raise ValueError("Unsupported event type.")

  def get_quantity_on_hand(self) -> int:
    return self._current_state.quantity_on_hand
```

Third, implementing the repo that interacts with the event store.

```python
from typing import List, Dict

class WarehouseProductRepository:
  def __init__(self):
    self._in_memory_streams: Dict[str, List[IEvent]] = {}

  def get(self, id: str) -> WarehouseProduct:
    warehouse_product = WarehouseProduct(id)
    
    if id in self._in_memory_streams:
      for event in self._in_memory_streams[id]:
        warehouse_product.apply_event(event)
    
    return warehouse_product

  def save(self, warehouse_product: WarehouseProduct):
    # create new event stream if it doesn't exist
    if warehouse_product.id not in self._in_memory_streams:
      self._in_memory_streams[warehouse_product.id] = []
    
    new_events = warehouse_product.get_uncommitted_events()
    self._in_memory_streams[warehouse_product.sku].extend(new_events)
    warehouse_product.events_committed()
```

```python
warehouse_product = warehouse_product_repository.get(id);
warehouse_product.receive_product(10);
warehouse_product.ship_product(2);

warehouse_product_repository.save(warehouse_product);
```

## References

- [Pattern: Event Sourcing](https://microservices.io/patterns/data/event-sourcing.html)
- [Event sourcing pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/event-sourcing.html)
- [Event Sourcing by Martin Fowler](https://martinfowler.com/eaaDev/EventSourcing.html)
- [Focusing on Events by Martin Fowler](https://martinfowler.com/eaaDev/EventNarrative.html)
- [Domain Event by Martin Fowler](https://martinfowler.com/eaaDev/DomainEvent.html)
- [Domain Events vs. Event-Sourcing](https://www.innoq.com/en/blog/2019/01/domain-events-versus-event-sourcing/)
- [Event Sourcing Example & Explained in plain English](https://codeopinion.com/event-sourcing-example-explained-in-plain-english/)
- [Event Sourcing do's and don'ts](https://codeopinion.com/event-sourcing-tips-dos-and-donts/)
- [Event Sourcing by Greg Young](https://www.youtube.com/watch?v=8JKjvY4etTY&ab_channel=GOTOConferences)
- [Event Sourcing: Snapshotting](https://domaincentric.net/blog/event-sourcing-snapshotting)
