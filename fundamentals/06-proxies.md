# Proxies

Proxies act as intermediaries between clients and servers, forwarding requests from clients to servers and returning responses from servers to clients.

While there are several types of proxies, the two most common ones are forward proxies and reverse proxies.

## Forward proxy

Forward proxies, also known as web proxies, sit between clients and the internet. Clients send requests to the forward proxy, which forwards them to the target server. The responses are then returned to the clients through the proxy.

Forward proxies can be used in many use cases, including:

- Anonymity: forward proxies can mask the IP addresses of clients, providing anonymity while browsing the internet.
- Content filtering: organizations use forward proxies to filter and control access to web content, enforcing security policies and blocking malicious or inappropriate websites.
- Caching: forward proxies can cache frequently accessed web pages to reduce bandwidth usage.

## Reverse Proxy

Reverse proxies sit in front of servers and act as gateways for client requests. They receive requests from clients, forward them to the appropriate server, and then return the responses to the clients.

Reverse proxies can be used in many use cases, including:

- Load balancing: reverse proxies distribute incoming traffic across multiple backend servers, ensuring optimal resource utilization and improving scalability and reliability.
- SSL termination: Reverse proxies can offload SSL/TLS encryption and decryption tasks from backend servers, reducing their computational overhead.
- Security: reverse proxies can inspect incoming requests, filtering out malicious traffic and protecting backend servers from attacks such as DDoS and SQL injection.

## Technologies

[Nginx](https://www.nginx.com/) - a widely-used web server known for its versatility. It's often employed as a reverse proxy and a load balancer.

## External references

- [Proxy server](https://en.wikipedia.org/wiki/Proxy_server)
