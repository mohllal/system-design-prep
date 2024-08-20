# Command Query Responsibility Segregation (CQRS)

Command Query Responsibility Segregation (CQRS) is an architectural pattern that separates the operations of reading and writing data into two different models.

- Command model: handles operations that modify data (create, update, delete). Commands are tasks that change the state of the system.
- Query model: handles operations that retrieve data. Queries do not change the system's state; they only read it.

## Command and Query Separation (CQS)

Command Query Separation (CQS) is a object-oriented principle which states that every method in a class should either be a command that performs an action or a query that returns data to the caller, but not both. In other words, a method should either modify the state (command) or return a result (query), but never do both.

```python
class BankAccount:
  def __init__(self, balance):
    self._balance = balance

  # Command: modify the account balance state
  def deposit(self, amount):
    if amount > 0:
      self._balance += amount

  # Command: modify the account balance state
  def withdraw(self, amount):
    if 0 < amount <= self._balance:
      self._balance -= amount

  # Query: return the account balance state
  def get_balance(self):
    return self._balance
```

## Trade-offs

CQRS provides benefits:

- Scalability: allows independent scaling of read and write sides. For instance, read-heavy applications can optimize query performance without affecting the command side.
- Performance: queries can be optimized for read performance (e.g., using de-normalized or read-optimized data structures), while commands can focus on transactional consistency.
- Flexibility in data storage: enables the use of different databases or storage models optimized for reading or writing, depending on the needs of each side.

But comes with challenges:

- Complexity: introducing CQRS increases system complexity, requiring careful design of consistency and data synchronization mechanisms between the command and query models.
- Eventual consistency: if multiple databases are used, the query side might not be immediately consistent with the command side, leading to eventual consistency.

## Use cases

- Read-heavy or write-heavy systems: Where read and write operations need to be optimized separately for performance.
- Combined with event sourcing: often used alongside event sourcing, where changes to the system are stored as a sequence of events, making it easier to propagate changes from the command side to the query side to have a read-view projection.

## Example

CQRS extends the Command Query Separation (CQS) principle from object-oriented programming by splitting a single object into two distinct ones: one for commands and one for queries. This separation is based on whether a method modifies the state (command) or retrieves data (query).

```python
from abc import ABC, abstractmethod
from typing import List
from dataclasses import dataclass


# Define the Customer and CustomerDetails classes
@dataclass
class Customer:
  id: str
  name: str
  locale: str
  is_preferred: bool = False

@dataclass
class CustomerDetails:
  name: str
  locale: str

# Interface for write operations
class ICustomerWriteService(ABC):
  @abstractmethod
  def make_customer_preferred(self, customer_id: str):
    pass

  @abstractmethod
  def change_customer_locale(self, customer_id: str, new_locale: str):
    pass

  @abstractmethod
  def create_customer(self, customer: Customer):
    pass

  @abstractmethod
  def edit_customer_details(self, customer_details: CustomerDetails):
    pass

# Interface for read operations
class ICustomerReadService(ABC):
  @abstractmethod
  def get_customer(self, customer_id: str) -> Customer:
    pass

  @abstractmethod
  def get_customers_with_name(self, name: str) -> List[Customer]:
    pass

  @abstractmethod
  def get_preferred_customers(self) -> List[Customer]:
    pass
```

## External references

- [Pattern: Command Query Responsibility Segregation (CQRS)](https://microservices.io/patterns/data/cqrs.html)
- [CQRS by Martin Fowler](https://martinfowler.com/bliki/CQRS.html)
- [Clarified CQRS](https://udidahan.com/2009/12/09/clarified-cqrs/)
- [CQRS Myths: 3 Most Common Misconceptions](https://codeopinion.com/cqrs-myths-3-most-common-misconceptions/)
- [CQRS & Event Sourcing Code Walk-Through](https://codeopinion.com/cqrs-event-sourcing-code-walk-through/)
- [When should you use CQRS?](https://blog.risingstack.com/when-to-use-cqrs/)
