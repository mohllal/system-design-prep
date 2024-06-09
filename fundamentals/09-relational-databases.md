# Relational Databases

A relational database is a type of database that stores and organizes data in tables with rows and columns. It uses a structure that allows relationships to be defined between different sets of data. The key components of a relational database include:

- Tables: data is organized into tables, where each table represents one entity or concept (e.g., customers, orders, products).
- Rows: each row (or record) in a table represents a unique instance of the entity and contains specific data related to that instance.
- Columns: each column in a table represents a specific attribute or field of the entity, defining the type of data that can be stored (e.g., name, age, address).
- Relationships: relationships between tables are established using foreign keys, which link rows in one table to rows in another, enabling complex queries and data integrity.

## SQL (Structured Query Language)

SQL is a domain-specific language used to manage and query relational databases. It provides a standardized way to interact with databases, allowing users to perform operations such as:

- Data querying: retrieving data from tables using `SELECT` statements.
- Data manipulation: adding, modifying, or deleting data using `INSERT`, `UPDATE`, and `DELETE` statements.
- Schema definition: dreating and altering database schema (tables, indexes, constraints) using `CREATE`, `ALTER`, and `DROP` statements.
- Data control: managing user permissions and access to database objects using `GRANT` and `REVOKE` statements.

## ACID properties

ACID (Atomicity, Consistency, Isolation, Durability) properties ensure that database transactions are processed reliably

### Atomicity

Atomicity ensures that all operations within a transaction are completed successfully, or if any operation fails, the transaction is rolled back to its previous state.

### Consistency

Consistency guarantees that the database remains in a consistent state before and after the transaction, adhering to all defined rules and constraints.

Below are some common types of database constraints:

- Entity integrity constraints: focuses on maintaining the uniqueness and validity of primary key values within a table.
- Referential integrity constraints: ensures that the values in the foreign key column match the values in the referenced primary key column of the parent table.
- Domain integrity constraints: restricts the values that can be inserted or updated in a column, ensuring that only valid data is stored.
- Unique constraints: ensures that all values in a specified column (or combination of columns) are unique across the table.

### Isolation

Isolation ensures that transactions operate independently of each other, preventing interference between concurrent transactions.

Below are some common implemented transaction isolation levels:

- Read uncommitted (max concurrency, low consistency): provides the lowest level of isolation where transactions read data that has been modified but not yet committed by other transactions, potentially leading to dirty reads.
- Read committed: ensures transactions only see data that has been committed by other transactions, avoiding dirty reads but still allowing non-repeatable reads and phantom reads.
- Repeatable read: ensures transactions consistently see the same snapshot of data throughout their execution, preventing non-repeatable reads but still allowing phantom reads.
- Serializable (low concurrency, max consistency): Provides the highest level of isolation where transactions are executed as if they were serialized (one at a time), preventing all types of anomalies (dirty reads, non-repeatable reads, and phantom reads) but potentially impacting concurrency and performance due to increased locking.

### Durability

Durability ensures that once a transaction is committed, changes made by the transaction are permanently saved and will not be lost, even in the event of a system failure.

## Locking

Database locking is a technique used to control concurrent access to data by preventing conflicting operations from executing simultaneously. It ensures data integrity and consistency in multi-user environments.

### Pessimistic locking

Pessimistic locking involves acquiring locks on data resources before performing operations, assuming that conflicts are likely to occur.

- Implementation: When a transaction accesses a data resource, it acquires an exclusive lock on the resource, preventing other transactions from accessing or modifying it until the lock is released.
- There are various levels of locks: they can be stored on a row level, page level (which we can consider a group of rows), table level, and whole database level.
- Usage: Commonly used in scenarios where conflicts are frequent or the risk of concurrent modifications is high.

### Optimistic locking

Optimistic locking assumes that conflicts are rare, so it allows transactions to proceed without acquiring locks initially.

- Implementation: When a transaction wants to modify a data resource, it first reads the resource's current state (utilizing version numbers). Before committing the changes, the transaction checks if the resource's state has changed since it was last read.

  If rows were modified by some other transaction, the update is rejected and the transaction has to start from scratch.
- Usage: Suitable for scenarios with low conflict probability, such as read-heavy applications or scenarios where maintaining high concurrency is crucial.

### Pessimistic vs Optimistic locking

- Concurrency: pessimistic locking reduces concurrency because transactions acquire exclusive locks upfront, blocking other transactions. Optimistic locking allows greater concurrency by delaying conflict resolution until commit time.
- Resource utilization: pessimistic locking may lead to resource contention and deadlock issues in high-concurrency environments. Optimistic locking minimizes resource contention but requires additional logic to handle conflicts at commit time.
- Conflict resolution: pessimistic locking resolves conflicts proactively by acquiring locks, while optimistic locking resolves conflicts reactively by checking for changes before committing transactions.

## Normalization

Database normalization is the process of organizing the fields and tables of a relational database to minimize redundancy and dependency.

### First Normal Form (1NF)

- Using row order to convey information is not permitted.
- Mixing data types within the same column is not permitted.
- Having a table without a primary key is not permitted.
- Repeating groups are not permitted.

### Second Normal Form (2NF)

All non-key attributes must be fully functionally dependent on the entire primary key.

Primary key is (`PlayerID`, `ItemType`)

| PlayerID   | ItemType   | ItemQuantity  | PlayerRating   |
| ---------- | ---------- | ------------- | -------------- |
| jodge1     | amultes    | 2             | Intermediate   |
| jodge1     | rings      | 4             | Intermediate   |
| gilal9     | coins      | 20            | Advanced       |

Here `PlayerRating` attribute isn't dependent on the primary key which will led to write/read/delete anamolies.

Normalized version is:

| PlayerID   | ItemType   | ItemQuantity      |
| ---------- | ---------- | ----------------- |
| jodge1     | amultes    | 2                 |
| jodge1     | rings      | 4                 |
| gilal9     | coins      | 20                |

and

| PlayerID   | PlayerRating   |
| ---------- | -------------- |
| jodge1     | Intermediate   |
| gilal9     | Advanced       |

### Third Normal Form (3NF)

All non-key attributes must be directly dependent on the primary key, not on other non-key attributes.

Primary key is `PlayerID` and there is an logical assoication between `PlayerRating` and `PlayerSkillLevel` as follows:

- `PlayerSkillLevel` from 0 to 4 is a `Beginner` rating.
- `PlayerSkillLevel` from 5 to 7 is a `Intermediate` rating.
- `PlayerSkillLevel` from 7 to 10 is a `Advanced` rating.

| PlayerID   | PlayerRating   | PlayerSkillLevel |
| ---------- | -------------- | ---------------- |
| jodge1     | Intermediate   | 4                |
| gilal9     | Advanced       | 9                |

Here `PlayerRating` attribute is dependent on the `PlayerSkillLevel` attribute which isn't a primary key attribute.

Normalized version is:

| PlayerID   | PlayerSkillLevel |
| ---------- | ---------------- |
| jodge1     | 4                |
| gilal9     | 9                |

and

| PlayerSkillLevel | PlayerRating |
| ---------------- | ------------ |
| 0                | Beginner     |
| 1                | Beginner     |
| ..               | ........     |

### Fourth Normal Form (4NF)

The only kinds of multivalued dependecy allowed in a table are multivalued dependecies on the key.

Primary key is `Movie` and there are two multivalued dependecy as the various locations can have the same movie, and multiple movies can have same listing.

| Movie      | Location  | Genre       |
| ---------- | --------- | ----------- |
| Pie        | USA       | Strategy    |
| Dune       | UK        | Thriller    |

Normalized version is:

| Movie      | Location  |
| ---------- | --------- |
| Pie        | USA       |
| Dune       | UK        |

and

| Movie      | Genre       |
| ---------- | ----------- |
| Pie        | Strategy    |
| Dune       | Thriller    |

## Technologies

- [MySQL](https://www.mysql.com/)
- [PostgreSQL](https://www.postgresql.org/)
- [Microsoft SQL Server](https://www.microsoft.com/en-us/sql-server/)

## External references

- [What is a relational database?](https://cloud.google.com/learn/what-is-a-relational-database)
- [ACID Properties in Databases With Examples - Video](https://www.youtube.com/watch?v=GAe5oB742dw&ab_channel=ByteByteGo)
- [What are Integrity Constraints in DBMS?](https://www.knowledgehut.com/blog/database/integrity-constraints-in-dbms)
- [Transaction Isolation Levels and why we should care](https://www.metisdata.io/blog/transaction-isolation-levels-and-why-we-should-care)
- [Optimistic vs. Pessimistic Locking by Daniel Soper](https://www.youtube.com/watch?v=x1wZPXKz40k&ab_channel=Dr.DanielSoper)
- [Learn Database Normalization](https://www.youtube.com/watch?v=GFQaEYEc8_8&ab_channel=Decomplexify)
- [Optimistic vs. Pessimistic Locking by Vlad Mihalcea](https://vladmihalcea.com/optimistic-vs-pessimistic-locking/)
- [A beginner’s guide to Serializability by Vlad Mihalcea](https://vladmihalcea.com/serializability/)
