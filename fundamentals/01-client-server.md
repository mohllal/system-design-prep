# Client-Server Model

The client-server model describes the communication between two computing entities over a network.

Clients are the ones requesting a resource or service and Servers are the ones providing that resource or service.

## IP addresses

**IPv4**: 32-bit represented in dotted-decimal format e.g. 192.0.2.146.

**IPv6**: 128-bit represented in hexadecimal notation e.g. 2001:0db8:85a3:0000:0000:8a2e:0370:7334.

## Ports

An integer between 0 and $2^{16}$! Typically ports between 0-1023 are resvered for system ports (a.k.a *well-known* ports).

 Some other pre-defined popular ports:

- HTTP: 80

- HTTPS: 8843 / 443

- DNS Lookup: 53

## DNS

Domain Name System is the phonebook for the internet; it desribes the entities and protcols involved in the translation of domain names to IP address.

### Main types of DNS servers

- Recursive DNS (a.k.a Recursive Resolver DNS): acts as an intermediary who can get the DNS information on behaf of the client.
  
  - If a recursive DNS has the DNS reference cached, or stored for a period of time, then it answers the DNS query by providing the source or IP information.
  
  - If not, it passes the query to one or more authoritative DNS servers to find the information.

- Authoritative DNS: provides answers to Recursive DNS servers queries translating domain names into IP addresses.

### DNS record request sequence for www.example.com

1. Client request www.example.com.

2. The request for www.example.com is routed to a *DNS Resolver*, which is typically managed by the user's ISP.

3. The DNS Resolver sends a request to the *DNS Root nameserver* which responds with the address for the .com *TLD DNS nameserver*.

4. The DNS Resolver sends another request to the .com TLD DNS nameserver which responds with the address for the domain's *Authoritative DNS nameserver*.

5. Lastly, the DNS Resolver sends a request to the domain's *Authoritative DNS nameserver* which responds with the IP address of the domain.

6. The DNS Resolver returns the IP address to the client.

## Communication techniques

### Polling

Polling is a server/client communication technique where the client repeatedly requests data from the server at regular intervals.

Use cases: applications where real-time data is not critical, such as checking for updates or notifications at regular intervals.

### Streaming

Streaming is a server/client communication technique where the server continuously sends data to the client as it becomes available.

Use cases: applications requiring real-time updates, such as live video streaming, online gaming, or real-time analytics.

Popular algorithms/technologies:

- WebSockets
- Server-Sent Events (SSE)
- gRPC Streaming

### REST (Representational State Transfer)

REST is an architectural style for designing networked applications. It relies on stateless, client-server communication, primarily using HTTP.

Use cases: web services, APIs.

### RPC (Remote Procedure Call)

RPC allows a program to cause a procedure to execute on another address space (commonly on another physical machine) as if it were a local procedure call.

Use cases: microservices, distributed systems.

### GraphQL

GraphQL is a query language for APIs and a runtime for executing those queries.

- Use cases: APIs with flexible and complex data requirements.

### Message Queues

Message queues provide asynchronous communication between services, allowing for decoupled and scalable systems.

Use cases: event-driven architecture, background processing.

Popular algorithms/technologies:

- [RabbitMQ](https://www.rabbitmq.com/)
- [Apache Kafka](https://kafka.apache.org/)
- [AWS SQS](https://aws.amazon.com/sqs/)

### Difference between RPC (Remote Procedure Call) and REST (Representational State Transfer)

#### RPC

- Philosophy: action/method-centric, focusing on executing procedures or methods on a remote server.
- Protocol: can use various protocols like HTTP, TCP, or custom protocols.
- Message format: often uses formats like JSON-RPC, XML-RPC, or gRPC (Protocol Buffers).
- Statefulness: can be stateful.
- Pros: simple for developers, potentially higher performance (e.g., gRPC), strong typing.
- Cons: tightly coupled, potentially less scalable, interoperability issues with different implementations.

#### REST

- Philosophy: resource-centric, focusing on manipulating resources using standard HTTP methods (GET, POST, PUT, DELETE).
- Protocol: uses HTTP/HTTPS.
- Message format: commonly uses JSON or XML, but can use any format.
- Statefulness: stateless.
- Pros: highly scalable, widely interoperable, leverages existing web infrastructure.
- Cons: can be complex to design properly, potentially higher overhead, more verbose due to stateless nature.

## External references

- [What is DNS?](https://aws.amazon.com/route53/what-is-dns)
- [WebSockets vs Server-Sent-Events vs Long-Polling vs WebRTC vs WebTransport](https://rxdb.info/articles/websockets-sse-polling-webrtc-webtransport.html)
- [Distributed Systems: RPC (Remote Procedure Call)](https://www.youtube.com/watch?v=S2osKiqQG9s&ab_channel=MartinKleppmann)
- [What's the difference between RPC and REST?](https://aws.amazon.com/compare/the-difference-between-rpc-and-rest/)
