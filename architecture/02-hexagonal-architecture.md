# Hexagonal Architecture (Ports and Adapters)

Hexagonal Architecture, also known as the **Ports and Adapters** architecture, is an architectural style that aims to make the application core independent of external systems (databases, APIs, user interfaces, etc.) by defining clear boundaries between internal logic and outside dependencies.

The main components are:

- **Core/Domain**: The business logic of the application, which is agnostic of how it's being used or accessed.
- **Ports**: Interfaces that define how the external world interacts with the core (input/driving/primary ports) and how the core interacts with external systems (output/driven/secondary ports).
- **Adapters**: Implementations of the ports. These can be user interfaces (e.g., web controllers) or external system integrations (e.g., database repositories, external API clients).

## Driving Side vs Driven Side

### Driving Side

1. Driving (or primary) actors are responsible for initiating interactions with the system. For example, a driving adapter could be a controller that receives input (e.g., from a user) and forwards it to the Application Core through a Port.

2. On the Driving side, the adapter depends on the port, which is implemented by the Application Service. The adapter doesn't know the specifics of who will handle its requests; it only knows the methods guaranteed by the port's interface. In this way, the adapter relies on an abstraction.

### Driven Side

1. Driven (or secondary) actors, on the other hand, are triggered into action by the Application Core. For instance, a database adapter acts as a driven actor when the Application invokes it to retrieve or persist data.

2. On the Driven side, the Application Service depends on the port, while the adapter implements the port's interface. This setup inverts the dependency, as the low-level adapter (e.g., a database repository) must conform to the abstraction defined in the application's core, which is considered the higher-level component.

## Pros and Cons

### Pros

1. Decoupling the Core from Infrastructure
   - The business logic is isolated from external concerns like databases, messaging systems, and UI frameworks.
   - The application can easily switch external systems (like changing databases) without modifying the core business logic.

2. Testability
   - By isolating the core from the external dependencies, it becomes easier to unit test the core business logic independently.
   - Mock implementations of the ports can be used to simulate interactions with external systems during tests.

3. Flexibility
   - Multiple interfaces (adapters) can be plugged into the core for various use cases (e.g., HTTP APIs, command-line interfaces, event-driven systems).
   - The architecture can evolve and scale by adding new adapters for different platforms without changing the core logic.

4. Maintenance
   - Ports and Adapters are interfaces, which encourages a contract-based approach to development, allowing for better extension and maintenance.

### Cons

1. Implementation Overhead:
   - Designing and implementing the necessary ports and adapters requires extra effort and time.

## Example

This example represents a simple bank account system. It demonstrates how the core business logic (Domain), ports (interfaces), and adapters (implementations) interact within the Hexagonal Architecture.

### Domain (Core Business Logic)

The domain layer represents the business logic, which is the core of the application. It is entirely independent of external systems like databases or user interfaces. In this case, the `BankAccount` class encapsulates the core logic for managing account balances.

```typescript
class BankAccount {
    private balance: number;

    constructor(initialBalance: number = 0) {
        this.balance = initialBalance;
    }

    // Method to deposit money
    deposit(amount: number): void {
        if (amount <= 0) {
            throw new Error("Invalid amount to deposit");
        }
        this.balance += amount;
    }

    // Method to withdraw money
    withdraw(amount: number): void {
        if (amount <= 0) {
            throw new Error("Invalid amount to withdraw");
        }
        if (this.balance < amount) {
            throw new Error("Insufficient funds");
        }
        this.balance -= amount;
    }

    // Method to check current balance
    getBalance(): number {
        return this.balance;
    }
}
```

### Ports

#### Input Port (Interface for Handling Commands)

The input port defines the operations that the external world can invoke. It is implemented by the application service layer, which uses the core business logic. This interface allows the application to be used by different adapters (e.g., a REST API, command-line interface).

```typescript
interface IBankAccountService {
    deposit(accountId: number, amount: number): void;
    withdraw(accountId: number, amount: number): void;
    checkBalance(accountId: number): number;
}
```

#### Output Port (Interface for Interacting with the Database)

The output port defines how the application interacts with external systems like a database or an API. The application service relies on this interface to retrieve and store data without being tightly coupled to any specific database technology.

```typescript
interface IBankAccountRepository {
    findAccountById(accountId: number): BankAccount;
    updateAccount(account: BankAccount): void;
}
```

### Application Service (Implements Input Port)

The application service acts as the bridge between the input port and the core business logic. It handles requests coming from external systems (via the input port) and uses the core logic to process them.

```typescript
class BankAccountService implements IBankAccountService {
    private repository: IBankAccountRepository;

    constructor(repository: IBankAccountRepository) {
        this.repository = repository;
    }

    deposit(accountId: number, amount: number): void {
        const account = this.repository.findAccountById(accountId);
        account.deposit(amount);
        this.repository.updateAccount(account);
    }

    withdraw(accountId: number, amount: number): void {
        const account = this.repository.findAccountById(accountId);
        account.withdraw(amount);
        this.repository.updateAccount(account);
    }

    checkBalance(accountId: number): number {
        const account = this.repository.findAccountById(accountId);
        return account.getBalance();
    }
}
```

### Adapters

#### Inbound Adapter (HTTP Controller)

An inbound adapter handles external input (such as an HTTP request) and delegates it to the application service. It conforms to the input port's interface.

```typescript
class HTTPBankAccountController {
    private service: IBankAccountService;

    constructor(service: IBankAccountService) {
        this.service = service;
    }

    // Simulating an HTTP POST request to deposit money
    deposit(req: { accountId: number, amount: number }): void {
        this.service.deposit(req.accountId, req.amount);
        console.log(`Deposited ${req.amount} into account ${req.accountId}`);
    }

    // Simulating an HTTP POST request to withdraw money
    withdraw(req: { accountId: number, amount: number }): void {
        this.service.withdraw(req.accountId, req.amount);
        console.log(`Withdrew ${req.amount} from account ${req.accountId}`);
    }

    // Simulating an HTTP GET request to check balance
    checkBalance(req: { accountId: number }): number {
        return this.service.checkBalance(req.accountId);
    }
}
```

#### Outbound Adapter (Database Repository)

An outbound adapter implements the output port, interacting with the actual persistence mechanism (e.g., a database). It conforms to the output port's interface.

```typescript
class BankAccountDatabaseRepository implements IBankAccountRepository {
    private db: any; // Simulating a database connection

    constructor(databaseConnection: any) {
        this.db = databaseConnection;
    }

    findAccountById(accountId: number): BankAccount {
        // Simulating a database query
        const accountData = this.db.query(`SELECT * FROM accounts WHERE id = ${accountId}`);
        return new BankAccount(accountData.balance);
    }

    updateAccount(account: BankAccount): void {
        // Simulating a database update
        this.db.execute(`UPDATE accounts SET balance = ${account.getBalance()} WHERE id = ${accountId}`);
    }
```

## References

- [Hexagonal architecture](https://alistair.cockburn.us/hexagonal-architecture/)
- [Ready for changes with Hexagonal Architecture](https://netflixtechblog.com/ready-for-changes-with-hexagonal-architecture-b315ec967749)
- [Hexagonal Architecture, there are always two sides to every story](https://medium.com/ssense-tech/hexagonal-architecture-there-are-always-two-sides-to-every-story-bc0780ed7d9c)
- [Hexagonal architecture: the what, why and when?](https://www.youtube.com/watch?v=qGp66Oc3zTg&ab_channel=YanCui)
- [Hexagonal Architecture (All You Need to Know)](https://www.youtube.com/watch?v=k_GkYMd8Ouc&ab_channel=GuiFerreira)
