# Network Protocols

## TCP/IP network model

1. Link Layer
   Handles the physical connection to the network to allow data frames to be placed on the network medium.
   Protocols: Ethernet, Wi-Fi.

2. Internet Layer
   Manages the addressing, routing, and fragmentation of data packets for transmission across interconnected networks.
   Protocols: IP (Internet Protocol), ICMP.

3. Transport Layer
   Provides end-to-end communication, ensuring that data is reliably and accurately delivered between applications.
   Protocols: TCP (Transmission Control Protocol), UDP (User Datagram Protocol).

4. Application Layer
   Provides network services directly to end-users or applications, allowing them to communicate over the network.
   Protocols: HTTP, FTP, SMTP.

### Data construction through the TCP/IP model layers

1. Application Layer
   Data at this layer is in the form of messages or streams, depending on the specific application protocol.

2. Transport Layer (TCP/UDP)
   The Application Layer data is encapsulated into segments (in case of TCP) or datagrams (in case of UDP).

   Each segment or datagram includes a header with control information like source and destination port numbers, sequence and acknowledgment numbers, etc...

3. Internet Layer (IP)
   The Transport Layer segment or datagram is then encapsulated into an IP packet.

   Each packet includes a header with information like source and destination IP addresses, time-to-live (TTL), etc...

4. Link Layer
   The Internet Layer packet is then encapsulated into a frame which is then transmitted over the physical medium.

   Each packet includes a header with information like source and destination MAC addresses, frame length, etc... and a footer with error-checking information for the integrity of the data in the frame.

## What happens when you type example.com in browser?

1. Resolve IP address via DNS.

2. TLS handshake (if it is https).

3. Generate an HTTP request with headers.

4. Open an HTTP connection to the resolved IP address.

5. Send the request to the server.

6. Receive the response from the server.

7. Parse the response headers.

8. Depending on the response headers, perform additional operations:

   - Redirect to a different resource on `301/302/303/307` status code.
   - Respect resouce caching policies if provided e.g. `cache-control`.

9. Decompress the response body if it's compressed (e.g. gzipped).

10. Parse the response body (render the HTML in case of `Content-Type: text/html`).

11. Close the HTTP connection.

## External references

- [TCP/IP](https://en.wikipedia.org/wiki/Internet_protocol_suite)
- [How the TCP/IP Protocols Handle Data Communications](https://docs.oracle.com/cd/E26505_01/html/E27061/ipov-29.html)
- [What Happens When You Type google.com Into Your browser's Address Box and Press Enter?](https://github.com/alex/what-happens-when)
- [What is a TLS Handshake?](https://www.cloudflare.com/learning/ssl/what-happens-in-a-tls-handshake)
