# Networking for Systems Design: Beginner to Advanced

> A comprehensive reference for understanding computer networking concepts as they apply to building and designing scalable, reliable distributed systems. Suitable for use as an AI instruction document, study guide, or engineering reference.

---

## Table of Contents

1. [Foundational Concepts](#1-foundational-concepts)
2. [The OSI Model](#2-the-osi-model)
3. [TCP/IP Stack](#3-tcpip-stack)
4. [IP Addressing and Subnetting](#4-ip-addressing-and-subnetting)
5. [DNS — Domain Name System](#5-dns--domain-name-system)
6. [HTTP and HTTPS](#6-http-and-https)
7. [Transport Layer Protocols](#7-transport-layer-protocols)
8. [Sockets and Connections](#8-sockets-and-connections)
9. [Network Topologies](#9-network-topologies)
10. [Load Balancing](#10-load-balancing)
11. [Proxies and Reverse Proxies](#11-proxies-and-reverse-proxies)
12. [Content Delivery Networks (CDN)](#12-content-delivery-networks-cdn)
13. [Caching in Distributed Systems](#13-caching-in-distributed-systems)
14. [API Design and Communication Protocols](#14-api-design-and-communication-protocols)
15. [WebSockets and Real-Time Communication](#15-websockets-and-real-time-communication)
16. [Microservices Networking](#16-microservices-networking)
17. [Service Discovery and Service Mesh](#17-service-discovery-and-service-mesh)
18. [Message Queues and Event-Driven Architecture](#18-message-queues-and-event-driven-architecture)
19. [Databases and Network Considerations](#19-databases-and-network-considerations)
20. [Replication and Consistency](#20-replication-and-consistency)
21. [Partitioning and Sharding](#21-partitioning-and-sharding)
22. [CAP Theorem and Distributed Systems Theory](#22-cap-theorem-and-distributed-systems-theory)
23. [Security in Networked Systems](#23-security-in-networked-systems)
24. [Network Reliability and Fault Tolerance](#24-network-reliability-and-fault-tolerance)
25. [Observability: Monitoring, Logging, Tracing](#25-observability-monitoring-logging-tracing)
26. [Cloud Networking](#26-cloud-networking)
27. [Rate Limiting and Throttling](#27-rate-limiting-and-throttling)
28. [System Design Patterns](#28-system-design-patterns)
29. [Scalability Principles](#29-scalability-principles)
30. [Advanced Topics](#30-advanced-topics)

---

## 1. Foundational Concepts

### What is a Network?
A network is a collection of computing devices connected together to share resources and communicate. Networks can be as small as two devices connected via a cable (LAN) or as large as the global internet.

### Key Terminology

- **Node**: Any device connected to a network (computer, router, phone, server).
- **Host**: A device that sends or receives data on a network.
- **Packet**: The fundamental unit of data transmitted over a network. Data is broken into packets, routed independently, and reassembled at the destination.
- **Bandwidth**: The maximum data transfer rate of a network connection, measured in bits per second (bps, Mbps, Gbps).
- **Latency**: The time it takes for a packet to travel from source to destination. Often measured as Round-Trip Time (RTT).
- **Throughput**: The actual amount of data successfully transferred per unit of time. Usually lower than bandwidth due to overhead.
- **Jitter**: Variation in packet delay. High jitter causes problems for real-time applications like video calls.
- **Packet Loss**: Percentage of packets that fail to reach the destination. Causes retransmissions and degrades performance.
- **Protocol**: A set of rules governing how data is formatted, transmitted, received, and acknowledged.
- **Port**: A logical endpoint on a host for a specific service. Ranges from 0–65535. Well-known ports: 80 (HTTP), 443 (HTTPS), 22 (SSH), 5432 (PostgreSQL).
- **Socket**: A combination of IP address + port that uniquely identifies a network endpoint.
- **Handshake**: An exchange of signals between two devices to establish a communication session.

### Units of Measurement

| Unit | Value |
|------|-------|
| 1 Kbps | 1,000 bits/sec |
| 1 Mbps | 1,000,000 bits/sec |
| 1 Gbps | 1,000,000,000 bits/sec |
| 1 ms (millisecond) | 1/1,000 second |
| 1 µs (microsecond) | 1/1,000,000 second |

> **Note**: Data storage uses binary prefixes (1 KiB = 1,024 bytes). Networking uses decimal (1 Kbps = 1,000 bits). Do not confuse them.

---

## 2. The OSI Model

The Open Systems Interconnection (OSI) model is a conceptual framework that standardizes how different network systems communicate in seven layers. Each layer has a specific responsibility and communicates only with the layers directly above and below it.

```
Layer 7 — Application     (HTTP, DNS, SMTP, FTP)
Layer 6 — Presentation    (TLS/SSL, encoding, compression)
Layer 5 — Session         (session management, NetBIOS)
Layer 4 — Transport       (TCP, UDP — ports, reliability)
Layer 3 — Network         (IP, ICMP, routing)
Layer 2 — Data Link       (Ethernet, MAC addresses, switches)
Layer 1 — Physical        (cables, signals, Wi-Fi radio waves)
```

### Layer Details

**Layer 1 — Physical**
Deals with the raw transmission of bits over a physical medium. Includes electrical signals, fiber optics, radio waves. Components: cables (Cat6, fiber), hubs, network interface cards (NICs).

**Layer 2 — Data Link**
Responsible for node-to-node data transfer and error detection within the same network segment. Uses MAC addresses (48-bit hardware addresses). Key device: switch. Protocols: Ethernet (IEEE 802.3), Wi-Fi (IEEE 802.11).

**Layer 3 — Network**
Handles logical addressing and routing across different networks. Uses IP addresses. Key devices: routers. Protocols: IP (IPv4, IPv6), ICMP (ping), OSPF, BGP.

**Layer 4 — Transport**
Provides end-to-end communication, reliability, and flow control. Protocols: TCP (reliable, ordered), UDP (fast, unreliable). Introduces the concept of ports.

**Layer 5 — Session**
Manages sessions between applications (opening, maintaining, closing). Less prominent in modern systems; largely handled by application-layer protocols.

**Layer 6 — Presentation**
Handles data translation, encryption, and compression. TLS/SSL operates here. Converts data between application format and network format (e.g., encoding, serialization).

**Layer 7 — Application**
The layer closest to the end user. Protocols: HTTP, HTTPS, DNS, SMTP, FTP, WebSocket. Applications interact with the network at this layer.

### Why OSI Matters for System Design
- Helps diagnose where failures occur (is it a routing issue? a TLS issue? an application bug?)
- Helps design appropriate solutions at each layer (e.g., load balancers operate at L4 or L7)
- Provides a common language between developers, network engineers, and architects

---

## 3. TCP/IP Stack

The TCP/IP model is the practical implementation used on the internet. It condenses OSI's 7 layers into 4:

```
Application Layer    (OSI layers 5, 6, 7) — HTTP, DNS, TLS
Transport Layer      (OSI layer 4)        — TCP, UDP
Internet Layer       (OSI layer 3)        — IP, ICMP
Network Access Layer (OSI layers 1, 2)    — Ethernet, Wi-Fi
```

### How a Packet Travels (Encapsulation)

When data is sent, each layer wraps the data with its own header (encapsulation):

```
[Application Data]
→ [TCP Header | Application Data]         ← Segment
→ [IP Header | TCP Header | Data]         ← Packet
→ [Ethernet Header | IP Header | TCP | Data | Ethernet Trailer]  ← Frame
→ Transmitted as bits
```

At the destination, each layer strips its header and passes the payload up (decapsulation).

### TCP Three-Way Handshake

TCP establishes connections using a three-way handshake before data transfer:

```
Client                        Server
  |                              |
  |-------- SYN (seq=x) -------->|   Client wants to connect
  |                              |
  |<---- SYN-ACK (seq=y,ack=x+1)-|   Server acknowledges
  |                              |
  |-------- ACK (ack=y+1) ------>|   Client confirms
  |                              |
  |<====== Data Transfer ========>|
```

- **SYN**: Synchronize sequence numbers
- **ACK**: Acknowledge received data
- This handshake adds ~1.5 RTT of latency before data can flow

### TCP Four-Way Termination

```
Client                        Server
  |                              |
  |-------- FIN ----------------->|
  |<-------- ACK -----------------|
  |<-------- FIN -----------------|
  |--------- ACK ---------------->|
  |  (waits in TIME_WAIT state)   |
```

### Key TCP Mechanisms

- **Sequence Numbers**: Every byte of data is numbered so the receiver can reassemble packets in order.
- **Acknowledgments (ACK)**: Receiver confirms which bytes it has received.
- **Retransmission**: If ACK is not received within a timeout, the sender retransmits.
- **Flow Control**: Receiver advertises a window size to prevent the sender from overwhelming it.
- **Congestion Control**: TCP dynamically adjusts sending rate based on perceived network congestion. Algorithms: Reno, CUBIC, BBR.
- **Nagle's Algorithm**: Buffers small packets to combine them. Can add latency in interactive applications (disable with `TCP_NODELAY`).

### TCP vs UDP Comparison

| Feature | TCP | UDP |
|---------|-----|-----|
| Connection | Connection-oriented | Connectionless |
| Reliability | Guaranteed delivery | No guarantees |
| Ordering | In-order delivery | No ordering |
| Error checking | Yes, with retransmission | Checksum only |
| Speed | Slower (overhead) | Faster (minimal overhead) |
| Use cases | Web, email, file transfer | Video streaming, gaming, DNS, VoIP |
| Header size | 20–60 bytes | 8 bytes |

---

## 4. IP Addressing and Subnetting

### IPv4

IPv4 addresses are 32-bit numbers written in dotted decimal notation: `192.168.1.100`

Each octet is 0–255. Total address space: ~4.3 billion addresses (nearly exhausted).

**Address Classes (historical, largely replaced by CIDR):**

| Class | Range | Default Subnet Mask | Use |
|-------|-------|---------------------|-----|
| A | 1.0.0.0 – 126.255.255.255 | /8 | Large organizations |
| B | 128.0.0.0 – 191.255.255.255 | /16 | Medium organizations |
| C | 192.0.0.0 – 223.255.255.255 | /24 | Small networks |

**Private IP Ranges (RFC 1918 — not routable on the internet):**

| Range | CIDR | Hosts |
|-------|------|-------|
| 10.0.0.0 – 10.255.255.255 | 10.0.0.0/8 | ~16 million |
| 172.16.0.0 – 172.31.255.255 | 172.16.0.0/12 | ~1 million |
| 192.168.0.0 – 192.168.255.255 | 192.168.0.0/16 | ~65,000 |

**Special Addresses:**
- `127.0.0.1` — Loopback (localhost), only accessible to the local machine
- `0.0.0.0` — "All interfaces" or "unspecified"
- `255.255.255.255` — Limited broadcast

### CIDR Notation (Classless Inter-Domain Routing)

CIDR replaces class-based addressing. Format: `IP/prefix_length`

- `/24` means 24 bits are the network portion → 256 addresses (254 usable)
- `/16` → 65,536 addresses
- `/8` → 16,777,216 addresses

**Subnet Mask**: A 32-bit number where `1` bits = network, `0` bits = host.
- `/24` = `255.255.255.0`
- `/16` = `255.255.0.0`

**Subnetting Example:**

Network: `192.168.1.0/24`
- Network address: `192.168.1.0`
- Broadcast: `192.168.1.255`
- Usable hosts: `192.168.1.1` – `192.168.1.254` (254 hosts)

Splitting into two `/25` subnets:
- `192.168.1.0/25` → hosts `.1`–`.126`
- `192.168.1.128/25` → hosts `.129`–`.254`

### IPv6

128-bit addresses, written in hexadecimal: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`

- Virtually unlimited address space (~340 undecillion addresses)
- Simplified header, built-in security (IPsec), auto-configuration
- Loopback: `::1`
- Can abbreviate leading zeros and consecutive zero groups: `2001:db8::8a2e:370:7334`

### NAT (Network Address Translation)

NAT allows multiple devices on a private network to share a single public IP address. A NAT device (typically a router) rewrites packet headers:

- **SNAT (Source NAT)**: Rewrites source IP. Used by home routers to let internal devices reach the internet.
- **DNAT (Destination NAT)**: Rewrites destination IP. Used for port forwarding / exposing services.

In system design, NAT complicates peer-to-peer communication and can hide the real client IP (important for logging and security).

---

## 5. DNS — Domain Name System

DNS translates human-readable domain names into IP addresses. It is a hierarchical, distributed database.

### DNS Hierarchy

```
Root (.) 
  └── TLD (.com, .org, .net, .in)
        └── Second-Level Domain (google.com, amazon.com)
              └── Subdomain (www, api, mail)
```

### DNS Resolution Process

1. User types `www.example.com` in browser
2. OS checks local DNS cache
3. If not cached, queries **Recursive Resolver** (usually your ISP or 8.8.8.8)
4. Resolver checks its cache
5. If not cached, resolver queries a **Root Name Server** → "I don't know, ask `.com` TLD"
6. Resolver queries **.com TLD Server** → "I don't know, ask `example.com` authoritative server"
7. Resolver queries **Authoritative Name Server** for `example.com` → Returns IP address
8. Resolver caches result and returns to client
9. Client caches result for TTL duration

### DNS Record Types

| Record | Purpose | Example |
|--------|---------|---------|
| A | Maps hostname to IPv4 address | `example.com → 93.184.216.34` |
| AAAA | Maps hostname to IPv6 address | `example.com → 2606:2800::1` |
| CNAME | Alias to another hostname | `www.example.com → example.com` |
| MX | Mail exchange server | `example.com → mail.example.com` |
| NS | Authoritative name servers | `example.com → ns1.example.com` |
| TXT | Arbitrary text (SPF, DKIM, verification) | `"v=spf1 include:..."` |
| SOA | Start of Authority — zone metadata | Serial, refresh interval, etc. |
| PTR | Reverse DNS (IP → hostname) | `93.184.216.34 → example.com` |
| SRV | Service location records | Port and hostname for a service |
| CAA | Certificate authority authorization | Restricts which CAs can issue SSL certs |

### TTL (Time to Live)

DNS records have a TTL in seconds. Once cached, a resolver will serve the cached answer until TTL expires. Implications:
- Lower TTL = faster propagation when you change records (but more DNS queries)
- Higher TTL = better performance, less load on authoritative servers
- During migrations, lower TTL ahead of time (e.g., to 60s before changing an A record)

### DNS in System Design

- **GeoDNS**: Return different IPs based on the requester's geographic location. Used to route users to the nearest data center.
- **DNS Round Robin**: Multiple A records for the same hostname. Clients are returned addresses in rotation — a simple form of load balancing with no health checking.
- **DNS Failover**: Authoritative servers monitor health; switch A records if a server goes down. Limited by TTL propagation delay.
- **Split-horizon DNS**: Different answers for internal vs. external clients.
- **DNS-based Service Discovery**: Used in microservices (e.g., Kubernetes uses internal DNS for service discovery).

---

## 6. HTTP and HTTPS

### HTTP (HyperText Transfer Protocol)

HTTP is the application-layer protocol for the web. It is stateless — each request is independent; the server retains no memory of previous requests by default.

### HTTP Request Structure

```
GET /api/users/42 HTTP/1.1
Host: example.com
Accept: application/json
Authorization: Bearer eyJhbGc...
User-Agent: Mozilla/5.0
```

### HTTP Response Structure

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 234
Cache-Control: max-age=3600

{"id": 42, "name": "Alice", ...}
```

### HTTP Methods

| Method | Purpose | Idempotent | Safe | Body |
|--------|---------|------------|------|------|
| GET | Retrieve resource | Yes | Yes | No |
| POST | Create resource | No | No | Yes |
| PUT | Replace resource | Yes | No | Yes |
| PATCH | Partial update | No | No | Yes |
| DELETE | Remove resource | Yes | No | Optional |
| HEAD | GET without body | Yes | Yes | No |
| OPTIONS | Describe capabilities | Yes | Yes | No |

**Idempotent**: Multiple identical requests have the same effect as one.
**Safe**: Request does not modify server state.

### HTTP Status Codes

| Range | Category | Examples |
|-------|----------|---------|
| 1xx | Informational | 100 Continue, 101 Switching Protocols |
| 2xx | Success | 200 OK, 201 Created, 204 No Content |
| 3xx | Redirection | 301 Moved Permanently, 302 Found, 304 Not Modified |
| 4xx | Client Error | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 429 Too Many Requests |
| 5xx | Server Error | 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable, 504 Gateway Timeout |

### HTTP Versions

**HTTP/1.0**: One request per TCP connection. Very inefficient.

**HTTP/1.1** (1997, still widely used):
- Persistent connections (keep-alive) — reuse TCP connection for multiple requests
- Pipelining (send multiple requests without waiting, rarely used)
- Chunked transfer encoding
- Host header (required, enabling virtual hosting)
- Problem: **Head-of-Line (HOL) blocking** — later requests must wait for earlier ones

**HTTP/2** (2015):
- **Multiplexing**: Multiple concurrent requests over a single TCP connection, no HOL blocking at HTTP level
- **Header compression** (HPACK): Reduces overhead significantly
- **Server Push**: Server can proactively send resources before client asks
- **Binary framing**: More efficient than text-based HTTP/1.1
- Still suffers from TCP-level HOL blocking

**HTTP/3** (2022):
- Runs over **QUIC** (UDP-based) instead of TCP
- Eliminates TCP-level HOL blocking
- Faster connection establishment (0-RTT or 1-RTT)
- Built-in TLS 1.3
- Better performance on lossy networks (mobile)

### HTTPS (HTTP Secure)

HTTPS = HTTP over TLS (Transport Layer Security). Provides:
- **Encryption**: Data is encrypted in transit (confidentiality)
- **Authentication**: Server identity verified via certificate
- **Integrity**: Data cannot be modified in transit (tamper detection)

### TLS Handshake (TLS 1.3 simplified)

```
Client                          Server
  |                               |
  |--- ClientHello (cipher suites, key shares) -->|
  |<-- ServerHello + Certificate + Finished ------|
  |--- Finished (client verify) ----------------->|
  |<=============== Application Data =============>|
```

TLS 1.3 achieves full handshake in 1-RTT (and 0-RTT for resumptions).

### HTTP Headers for Caching

- `Cache-Control: max-age=3600` — Cache for 1 hour
- `Cache-Control: no-cache` — Must revalidate before using cached copy
- `Cache-Control: no-store` — Never cache (sensitive data)
- `Cache-Control: public` — Shared caches (CDN) may cache
- `Cache-Control: private` — Only browser may cache
- `ETag` — A hash/version identifier for a resource
- `Last-Modified` — Timestamp of last modification
- `If-None-Match: <etag>` — Conditional request; server returns 304 if unchanged
- `Vary` — Specify which headers affect the cached response

---

## 7. Transport Layer Protocols

### TCP Deep Dive

**Congestion Control Algorithms:**

- **Slow Start**: TCP begins with a small congestion window and doubles it each RTT until a threshold.
- **Congestion Avoidance**: After the threshold, window grows linearly (+1 MSS per RTT).
- **Fast Retransmit**: On 3 duplicate ACKs, retransmit without waiting for timeout.
- **Fast Recovery**: After fast retransmit, halve window instead of resetting to 1.
- **CUBIC** (Linux default): More aggressive growth, better for high-bandwidth-delay-product links.
- **BBR** (Bottleneck Bandwidth and RTT): Models the network to maximize throughput while minimizing queue delay. Used by Google.

**Connection States:**
`LISTEN → SYN_SENT → SYN_RECEIVED → ESTABLISHED → FIN_WAIT_1 → FIN_WAIT_2 → TIME_WAIT → CLOSED`

**TIME_WAIT State**: A closed TCP connection stays in TIME_WAIT for 2×MSL (Maximum Segment Lifetime, ~60s) to ensure delayed packets don't corrupt new connections. At high connection rates, this can exhaust port resources.

**TCP Keep-Alive**: Sends periodic probes to detect if the remote end is still alive. Important for long-lived connections (database connections, WebSockets).

### UDP

UDP provides minimal service: it sends datagrams with no guarantees. Applications that use UDP typically implement their own reliability if needed.

**Use cases for UDP:**
- DNS (single request/response, retransmit at app level)
- Video/audio streaming (better to drop a frame than wait for retransmit)
- Online games (low latency more important than reliability)
- DHCP (broadcast-based)
- QUIC (implements reliability at the QUIC layer, not TCP)

### QUIC

QUIC (Quick UDP Internet Connections) is the transport protocol underlying HTTP/3. Key properties:
- Runs over UDP, implements its own reliable delivery
- Connection IDs not tied to IP:port → survives network changes (e.g., switching from Wi-Fi to cellular)
- Independent streams: HOL blocking only affects one stream, not all
- Built-in TLS 1.3
- 0-RTT connection resumption

---

## 8. Sockets and Connections

### What is a Socket?

A socket is an endpoint for communication. It is identified by:
`(Protocol, Local IP, Local Port, Remote IP, Remote Port)`

This 5-tuple uniquely identifies a connection. A server socket listening on port 80 can handle thousands of simultaneous connections — each is a different 5-tuple.

### Ephemeral Ports

When a client connects to a server, the OS assigns the client a random high-numbered **ephemeral port** (typically 49152–65535 or 32768–60999 on Linux). This becomes the source port for that connection.

### Socket States in Web Servers

High-traffic servers maintain pools of established connections. Key kernel parameters:
- `net.core.somaxconn` — Maximum length of the accept queue
- `net.ipv4.tcp_tw_reuse` — Allow reuse of TIME_WAIT sockets
- `ulimit -n` — Maximum open file descriptors (each socket is a file descriptor)

### Connection Pooling

Opening a new TCP connection (and TLS handshake) costs ~1-2 RTTs. Connection pooling reuses established connections for multiple requests:
- Database connection pools (HikariCP, PgBouncer)
- HTTP keep-alive / HTTP/2 multiplexing
- gRPC connection multiplexing

**Pool sizing**: Too small → bottleneck. Too large → resource exhaustion. Formula: pool_size = (available_connections) / (number_of_instances).

---

## 9. Network Topologies

### Physical Topologies

- **Bus**: All devices share a single cable. Simple, single point of failure.
- **Star**: All devices connect to a central switch/hub. Most common LAN topology.
- **Ring**: Each device connects to two others forming a ring. Token Ring (legacy).
- **Mesh**: Every device connects to every other. Maximum redundancy, expensive.
- **Tree (Hierarchical)**: Star networks connected together. Used in large enterprise/campus networks.
- **Hybrid**: Combination of multiple topologies.

### Data Center Network Topologies

**Three-Tier (Traditional):**
```
Core Layer (high-speed routers)
    ↓
Aggregation/Distribution Layer (aggregates access switches)
    ↓
Access Layer (connects servers)
```

**Leaf-Spine (Modern, preferred for scale):**
```
Spine Switches (high-bandwidth interconnects)
    ↕ (each leaf connects to every spine)
Leaf Switches (connect servers/racks)
    ↓
Servers
```
- Any server can reach any other server in exactly 2 hops
- Predictable, consistent latency
- Scale by adding more leaves; add spines for more bandwidth
- Eliminates Spanning Tree Protocol issues

**Fat-Tree (used in large-scale cloud):**
- A specific Clos network topology
- Full bisection bandwidth (any half of servers can communicate with the other half at full bandwidth)
- Used by Facebook, Google, AWS for massive-scale clusters

---

## 10. Load Balancing

Load balancers distribute incoming traffic across multiple backend servers to improve availability, scalability, and performance.

### Load Balancer Types

**Layer 4 (Transport Layer) Load Balancer:**
- Operates on TCP/UDP without inspecting application content
- Routes based on IP addresses and ports
- Very fast, minimal processing
- Cannot make routing decisions based on URL, cookies, or headers
- Examples: AWS NLB, HAProxy in TCP mode

**Layer 7 (Application Layer) Load Balancer:**
- Inspects HTTP headers, URL, cookies, request body
- Can route based on content (path-based, host-based routing)
- Can perform SSL termination, compression, caching
- Supports advanced features: health checks, session affinity, A/B testing
- Slightly more overhead than L4
- Examples: NGINX, AWS ALB, HAProxy in HTTP mode, Envoy

### Load Balancing Algorithms

**Round Robin**: Distribute requests in sequence: Server1 → Server2 → Server3 → Server1...
- Simple, works when servers have equal capacity and equal request costs.

**Weighted Round Robin**: Like round robin but servers receive traffic proportional to their weight.
- Use when servers have different capacities.

**Least Connections**: Route new request to server with fewest active connections.
- Better than round robin when requests vary significantly in processing time.

**Weighted Least Connections**: Combines server weight with current connection count.

**IP Hash**: Hash the client's IP to always route to the same server.
- Provides session affinity (sticky sessions) without cookie-based approaches.
- Problem: uneven distribution if clients are behind NAT.

**Consistent Hashing**: A smarter hashing approach where adding/removing servers only remaps a small fraction of requests. Widely used in distributed caches and databases.

**Least Response Time**: Route to server with lowest average response time and fewest connections. Requires health-check monitoring.

**Random with Two Choices (Power of Two Choices)**: Pick two random servers, send to the one with fewer connections. Near-optimal performance with minimal coordination.

### Session Affinity (Sticky Sessions)

Some applications require that a user's requests always go to the same server (e.g., in-memory session state). Methods:
- **Cookie-based**: LB inserts a cookie indicating which server to use.
- **IP Hash**: As above.

Problems: Defeats the purpose of load balancing if one user does heavy work. Better solution: externalize session state to a shared store (Redis).

### Health Checks

Load balancers continuously probe backend servers:
- **Active health checks**: LB sends periodic pings (HTTP, TCP, ICMP) to backends.
- **Passive health checks**: LB monitors live traffic; marks server unhealthy after N consecutive failures.

When a server fails, its connections are redistributed. When it recovers, it's gradually reintroduced.

### Global Load Balancing (GSLB)

Distributes traffic across multiple data centers or regions:
- **GeoDNS**: Route users to nearest region via DNS
- **Anycast**: Same IP advertised from multiple locations; BGP routing sends user to nearest
- **HTTP redirect**: LB redirects user to nearest regional endpoint

---

## 11. Proxies and Reverse Proxies

### Forward Proxy

A forward proxy sits between clients and the internet. Clients send requests to the proxy, which forwards them on behalf of the client.

**Uses:**
- Anonymity (hides client's real IP from the destination)
- Content filtering (corporate internet access control)
- Caching web content
- Bypassing geo-restrictions (VPNs often work similarly)

### Reverse Proxy

A reverse proxy sits in front of backend servers. Clients think they're talking to the server directly, but the proxy handles the request.

**Uses:**
- **Load balancing**: Distribute requests across multiple backends
- **SSL termination**: Decrypt HTTPS at the proxy, pass HTTP to backends (reduces backend CPU load)
- **Caching**: Cache static responses, reducing backend load
- **Compression**: Compress responses before sending to clients
- **Security**: Hide backend topology, protect against DDoS, WAF
- **Rate limiting**: Enforce request rate limits
- **Authentication**: Centralize auth before forwarding to backends

**Popular reverse proxies**: NGINX, HAProxy, Envoy, Caddy, Traefik, AWS ALB/CloudFront

### NGINX as Reverse Proxy (Example Configuration)

```nginx
upstream backend {
    server 10.0.0.1:8080 weight=3;
    server 10.0.0.2:8080 weight=1;
    keepalive 32;
}

server {
    listen 443 ssl;
    ssl_certificate /etc/ssl/cert.pem;
    ssl_certificate_key /etc/ssl/key.pem;

    location /api/ {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    location /static/ {
        root /var/www;
        expires 1d;
    }
}
```

### API Gateway

An API Gateway is a specialized reverse proxy for APIs that adds:
- Authentication and authorization
- Request/response transformation
- Rate limiting and quotas
- Analytics and logging
- Protocol translation (REST ↔ gRPC)
- Request routing (route `/v1/users` to user-service)

Examples: Kong, AWS API Gateway, Apigee, Nginx Plus, Envoy

---

## 12. Content Delivery Networks (CDN)

A CDN is a globally distributed network of servers (Points of Presence — PoPs) that cache and deliver content from locations geographically close to users, reducing latency.

### How a CDN Works

1. Origin server holds the authoritative content.
2. CDN provider has PoPs in many locations (edge nodes).
3. When a user requests content, DNS routes them to the nearest edge node.
4. If the edge has the content cached (**cache hit**), it serves it directly.
5. If not (**cache miss**), edge fetches from origin, caches it, and serves it.

### Cache Hit Ratio

The percentage of requests served from cache. A higher ratio means less load on your origin and lower latency for users. Good CDNs achieve 90%+ hit ratios for static content.

### What to Cache on a CDN

**Good for CDN caching:**
- Static assets: images, CSS, JavaScript, fonts, PDFs
- Pre-rendered HTML
- Video and audio files
- Software downloads

**Not suitable for CDN caching:**
- Authenticated / personalized responses
- Real-time data (stock prices, live scores)
- POST/PUT/DELETE requests (state-modifying)

### CDN Cache Invalidation

When content changes, you need to purge stale cache:
- **TTL-based expiry**: Set `Cache-Control: max-age` appropriately
- **Cache purge API**: Explicitly delete cached objects by URL
- **Cache versioning**: Include version in URL (`style.v4.css`) — changing the URL bypasses stale cache

### CDN Features for System Design

- **Edge compute/Lambda@Edge**: Run code at CDN edge nodes (personalization, A/B testing, auth)
- **DDoS protection**: CDN absorbs attack traffic before it reaches your origin
- **TLS termination at edge**: Improves handshake latency for distant users
- **Image optimization**: Auto-resize, compress, convert format (WebP) based on device
- **HTTP/2 and HTTP/3**: CDNs often support new protocols even if your origin doesn't

**Popular CDNs**: Cloudflare, AWS CloudFront, Fastly, Akamai, Google Cloud CDN

---

## 13. Caching in Distributed Systems

Caching stores copies of data in faster storage to reduce latency and backend load.

### Cache Levels

**CPU Caches (L1/L2/L3)**: Hardware-level, microsecond access. Managed by OS/CPU.

**In-Process Memory Cache**: Data stored in application heap (e.g., a HashMap). Zero network cost. Lost on restart. Not shared between instances. Suitable for: small, static lookup tables.

**Distributed Cache**: Shared cache accessible by all application instances. Examples: Redis, Memcached. Millisecond access. Persists across restarts (optional). Essential for horizontally scaled applications.

**Database Query Cache**: Many databases cache query results internally. Can be invalidated unpredictably.

**CDN Cache**: Edge caching of HTTP responses. See Section 12.

**Client-Side (Browser) Cache**: HTTP caching in the browser.

### Caching Strategies

**Cache-Aside (Lazy Loading):**
Application first checks cache; on miss, reads from DB, writes to cache, returns data.
```
Read: App → Cache → (miss) → DB → write to Cache → return
Write: App → DB (invalidate or update cache)
```
Pros: Only cached data that is actually requested; resilient to cache failure.
Cons: First request always hits DB (cold start); potential stale data.

**Write-Through:**
Write goes to cache AND DB simultaneously.
```
Write: App → Cache + DB (together)
Read: App → Cache → (miss) → DB
```
Pros: Cache always consistent with DB.
Cons: Write latency is higher; cache fills up with data that may never be read.

**Write-Behind (Write-Back):**
Writes go to cache immediately; DB is updated asynchronously.
```
Write: App → Cache → (async) → DB
```
Pros: Very low write latency.
Cons: Risk of data loss if cache fails before DB write; complex consistency.

**Read-Through:**
Cache sits in front of DB; on a miss, cache itself fetches from DB.
```
Read: App → Cache → (miss, cache fetches) → DB
```
Pros: Simpler application logic.
Cons: Cache miss is still slow.

**Refresh-Ahead:**
Cache proactively refreshes data before it expires, based on predicted access patterns.
Pros: Low latency for frequently accessed data.
Cons: May cache data that won't be accessed.

### Cache Eviction Policies

When the cache is full, it must evict entries to make space:

- **LRU (Least Recently Used)**: Evict the item not accessed for the longest time. Most common.
- **LFU (Least Frequently Used)**: Evict the item accessed least often. Better for skewed access patterns.
- **FIFO (First In, First Out)**: Evict oldest inserted item, regardless of access.
- **TTL (Time to Live)**: Evict items after a set expiry time.
- **Random Replacement**: Evict a random item. Simple, surprisingly effective.
- **MRU (Most Recently Used)**: Evict most recently used (for specific access patterns like full scans).

### Cache Invalidation

"There are only two hard things in Computer Science: cache invalidation and naming things." — Phil Karlton

Strategies:
- **TTL expiry**: Simple but may serve stale data up to TTL.
- **Event-driven invalidation**: When data changes in DB, publish an event → cache listens and invalidates.
- **Write-through**: Always update cache with DB write.
- **Versioned keys**: Change the cache key when data changes (`user:42:v2` → `user:42:v3`).

### Cache Problems

**Cache Stampede (Thundering Herd):**
When a popular cache entry expires, many concurrent requests all miss and hit the DB simultaneously.
Solutions:
- **Mutex/Lock**: First request acquires lock, fetches from DB; others wait for cache.
- **Probabilistic Early Expiration**: Randomly expire items slightly before TTL to spread refreshes.
- **Background refresh**: Serve stale while refreshing asynchronously.

**Cache Penetration:**
Queries for data that doesn't exist in DB (e.g., negative lookups). Each miss bypasses cache and hits DB.
Solutions:
- Cache null results with a short TTL.
- **Bloom filter**: Probabilistic data structure that quickly tells if a key definitely doesn't exist.

**Cache Avalanche:**
Many cache entries expire at the same time, causing a surge of DB requests.
Solutions:
- Add random jitter to TTLs.
- Stagger cache warm-up.
- Circuit breaker to protect DB.

**Hot Keys:**
A single cache key is accessed by millions of requests per second, overwhelming one cache node.
Solutions:
- Replicate hot keys across multiple cache nodes.
- Local in-process cache as a second layer.
- Client-side routing to spread load.

---

## 14. API Design and Communication Protocols

### REST (Representational State Transfer)

REST is an architectural style for distributed hypermedia systems, not a protocol. RESTful APIs use HTTP methods and URLs to represent resources.

**REST Principles:**
1. **Stateless**: Each request contains all information needed; server stores no session state.
2. **Uniform Interface**: Consistent URLs and methods.
3. **Client-Server Separation**: Client and server evolve independently.
4. **Cacheable**: Responses must indicate cacheability.
5. **Layered System**: Client doesn't know if it's talking to the real server or a proxy.
6. **Code on Demand** (optional): Server can send executable code.

**RESTful URL Design:**

```
GET    /users              → List all users
POST   /users              → Create a new user
GET    /users/{id}         → Get user by ID
PUT    /users/{id}         → Replace user
PATCH  /users/{id}         → Partial update
DELETE /users/{id}         → Delete user

GET    /users/{id}/orders  → User's orders
POST   /users/{id}/orders  → Create order for user
```

**Versioning strategies:**
- URL path: `/api/v1/users`
- Query param: `/api/users?version=1`
- Header: `Accept: application/vnd.api+json;version=1`

### gRPC (Google Remote Procedure Call)

gRPC is a high-performance RPC framework using Protocol Buffers (protobuf) for serialization and HTTP/2 for transport.

**Advantages over REST:**
- Binary serialization (protobuf) is much smaller and faster than JSON
- Strongly typed contracts (`.proto` files)
- Built-in code generation for clients in many languages
- Supports streaming (client, server, bidirectional)
- HTTP/2 multiplexing

**Use cases:** Internal microservice communication, high-throughput APIs, mobile (smaller payloads).

**Proto example:**
```protobuf
syntax = "proto3";

service UserService {
  rpc GetUser (GetUserRequest) returns (User);
  rpc ListUsers (ListUsersRequest) returns (stream User);
}

message User {
  int64 id = 1;
  string name = 2;
  string email = 3;
}
```

### GraphQL

GraphQL is a query language for APIs that lets clients request exactly the data they need.

**Key concepts:**
- Single endpoint (`/graphql`)
- Client specifies exact fields needed (no over/under-fetching)
- Strongly typed schema
- Subscriptions for real-time data

**Tradeoffs:**
- More complex server implementation (N+1 query problem, needs DataLoader)
- Harder to cache (POST requests; unique queries)
- Great for frontend-driven development, complex data graphs

### tRPC

Type-safe RPC for TypeScript. No schema definition — TypeScript types are the contract. Best for full-stack TypeScript applications.

### GraphQL vs REST vs gRPC

| Criteria | REST | gRPC | GraphQL |
|---------|------|------|---------|
| Protocol | HTTP | HTTP/2 | HTTP |
| Payload | JSON/XML | Protobuf (binary) | JSON |
| Type safety | No | Strong | Strong |
| Streaming | Limited | Native | Subscriptions |
| Browser support | Full | Limited (gRPC-Web) | Full |
| Caching | Easy (HTTP cache) | Manual | Complex |
| Best for | Public APIs | Internal microservices | Complex data, mobile |

### API Pagination

**Offset Pagination:**
```
GET /users?offset=0&limit=20
GET /users?offset=20&limit=20
```
Simple but inconsistent if items are inserted/deleted between pages. Slow for deep pages (DB must scan all rows).

**Cursor-based Pagination:**
```
GET /users?limit=20
→ returns: { data: [...], next_cursor: "eyJpZCI6MjB9" }
GET /users?cursor=eyJpZCI6MjB9&limit=20
```
Stable even with data changes. Efficient (index-based). Cannot jump to arbitrary page. Preferred for feeds and real-time data.

**Keyset Pagination:**
```
GET /users?after_id=1000&limit=20
```
Similar to cursor, uses a natural key. Fast DB queries using index.

---

## 15. WebSockets and Real-Time Communication

### WebSockets

WebSockets provide a persistent, full-duplex communication channel over a single TCP connection. Unlike HTTP (request-response), either side can send data at any time.

**WebSocket Handshake (HTTP Upgrade):**
```
Client → Server:
GET /ws HTTP/1.1
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13

Server → Client:
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```

After the handshake, the connection is a raw WebSocket — no more HTTP semantics.

**Use cases:** Live chat, collaborative editing, real-time dashboards, multiplayer games, stock tickers, notifications.

**Challenges at scale:**
- Each open WebSocket is a stateful connection tied to a server; horizontal scaling is complicated.
- Need a shared pub/sub system (Redis Pub/Sub, Kafka) to broadcast messages across server instances.
- Connection state must be handled carefully (reconnection, heartbeats).

### Server-Sent Events (SSE)

SSE is a simpler alternative for server-to-client unidirectional streaming over HTTP.

```
HTTP/1.1 200 OK
Content-Type: text/event-stream
Cache-Control: no-cache

data: {"price": 150.25}

data: {"price": 150.50}
```

- Works over regular HTTP (no upgrade needed)
- Automatic reconnection built into browser EventSource API
- Only server → client (client cannot send after initial request)
- Use cases: live feeds, notifications, progress updates
- Simpler than WebSockets when bidirectionality is not needed

### Long Polling

A workaround for when WebSockets are not available. The client sends an HTTP request; the server holds it open until data is available, then responds. Client immediately sends another request.

```
Client → Server: GET /events (server waits)
Server → Client: HTTP 200 with data (when available)
Client → Server: GET /events (immediately again)
```

Simple but inefficient. Largely replaced by WebSockets and SSE in modern systems.

### Polling (Short Polling)

Client sends periodic HTTP requests regardless of whether new data exists. Simple but wastes resources.

### Choosing a Real-Time Protocol

| Protocol | Direction | Use Case |
|---------|-----------|----------|
| WebSocket | Bidirectional | Chat, gaming, collaboration |
| SSE | Server → Client | Notifications, live feeds |
| Long Polling | Server → Client | Legacy systems, simple notifications |
| WebRTC | Peer-to-Peer | Video/voice calls, peer file sharing |

---

## 16. Microservices Networking

### Monolith vs Microservices

**Monolith**: All functionality in a single deployable unit. In-process communication (function calls). Simple to develop and debug initially.

**Microservices**: Application split into small, independently deployable services. Communicate over a network. Each service owns its data and has a specific bounded context.

### Microservice Communication Patterns

**Synchronous (Request-Response):**
- REST/HTTP
- gRPC
- Direct service-to-service calls
- Caller blocks waiting for response
- Creates temporal coupling

**Asynchronous (Event-Driven):**
- Message queues (RabbitMQ, SQS)
- Event streaming (Kafka)
- Caller doesn't wait; fire and forget
- Better decoupling and resilience

### Inter-Service Communication Challenges

**Network Failures**: Unlike in-process calls, network calls can fail at any point. Services must handle timeouts, retries, and partial failures.

**Service Discovery**: Services need to find each other. IP addresses change as services scale. Solutions:
- Client-side discovery (service queries registry directly)
- Server-side discovery (load balancer queries registry)

**Data Consistency**: Each service has its own database. Cross-service transactions are complex. Use eventual consistency and Sagas.

**Distributed Tracing**: A single user request may touch 10+ services. Need correlation IDs to trace the full path.

### Patterns for Resilience

**Circuit Breaker:**
If a downstream service is failing, stop sending requests to it (open the circuit) to prevent cascading failures. After a timeout, send a test request (half-open). If it succeeds, close the circuit.

```
States: CLOSED (normal) → OPEN (failing) → HALF-OPEN (testing) → CLOSED
```

**Retry with Exponential Backoff:**
On failure, retry after a delay. Double the delay with each retry. Add jitter (random variance) to avoid synchronized retries from multiple clients.

```
Retry 1: wait 1s
Retry 2: wait 2s
Retry 3: wait 4s (+jitter)
Retry 4: wait 8s (+jitter)
```

**Bulkhead:**
Isolate critical resources. Separate thread pools or connection pools for different dependencies so that a slow service doesn't exhaust resources for others.

**Timeout:**
Always set timeouts on network calls. A hanging call that never returns is worse than a fast failure. Use the 99th percentile latency as a guide for timeout values.

**Fallback:**
Define a fallback response when a service is unavailable. Serve cached data, a degraded response, or a user-friendly error.

---

## 17. Service Discovery and Service Mesh

### Service Discovery

When services scale horizontally, their IPs and ports change dynamically. Service discovery allows services to find each other automatically.

**Client-Side Discovery:**
1. Service registers itself with a registry (e.g., Consul, Eureka) on startup.
2. Client queries the registry to get available instances.
3. Client performs load balancing and calls the instance directly.

**Server-Side Discovery:**
1. Client sends request to load balancer.
2. Load balancer queries registry.
3. Load balancer routes to appropriate instance.

**DNS-Based Discovery:**
Service addresses registered as DNS records. Kubernetes uses this: `service-name.namespace.svc.cluster.local`

**Popular tools**: Consul, etcd (via Kubernetes), Eureka (Netflix), ZooKeeper, AWS Cloud Map

### Service Mesh

A service mesh is a dedicated infrastructure layer for handling service-to-service communication. It offloads cross-cutting concerns (retries, circuit breaking, mTLS, observability) from application code to a sidecar proxy.

**Architecture:**
- **Data Plane**: Sidecar proxies (Envoy) injected alongside each service. Intercept all inbound and outbound traffic.
- **Control Plane**: Central management that configures the proxies (Istio, Linkerd control plane).

```
[Service A] → [Sidecar Proxy (Envoy)] ← network → [Sidecar Proxy (Envoy)] → [Service B]
                         ↑                                      ↑
                    [Control Plane — Istio]
```

**Service mesh features:**
- Automatic mTLS between services (no app code changes)
- Traffic management (canary deployments, A/B testing, traffic splitting)
- Observability (metrics, traces, logs for all traffic)
- Retry/circuit breaker policies
- Rate limiting

**Popular service meshes**: Istio, Linkerd, Consul Connect, AWS App Mesh

**Tradeoffs**: Complexity, latency overhead from sidecar hops, operational burden.

---

## 18. Message Queues and Event-Driven Architecture

### Why Message Queues?

In synchronous architectures, services are tightly coupled — if Service B is slow or down, Service A waits or fails. Message queues decouple producers and consumers:

- **Temporal decoupling**: Producer and consumer don't need to be online simultaneously.
- **Rate decoupling**: Producer can produce faster than consumer can consume (queue absorbs bursts).
- **Geographic decoupling**: Producer and consumer can be in different regions.

### Key Concepts

- **Producer**: Publishes messages to the queue.
- **Consumer**: Reads and processes messages from the queue.
- **Queue**: Holds messages; typically FIFO.
- **Topic**: A named channel in pub/sub systems. Multiple consumers can subscribe.
- **Message Broker**: The server managing queues/topics (RabbitMQ, Kafka, SQS).
- **Acknowledgment (ACK)**: Consumer confirms successful processing; broker removes message.
- **Dead Letter Queue (DLQ)**: Messages that fail processing repeatedly are moved here for inspection.
- **At-Least-Once Delivery**: Message delivered at least once; consumer must handle duplicates (idempotency).
- **Exactly-Once Delivery**: Hard to guarantee; achieved with transactions in Kafka.
- **At-Most-Once Delivery**: Message delivered at most once; possible to lose a message.

### Message Queue Types

**Point-to-Point (Queue):**
Each message consumed by exactly one consumer. Work distribution pattern.

**Publish-Subscribe (Topic):**
Each message delivered to ALL subscribers. Broadcast pattern.

### Apache Kafka

Kafka is a distributed event streaming platform designed for high throughput and durability.

**Core concepts:**
- **Topic**: Logical category for messages.
- **Partition**: Topics are split into partitions for parallelism. Messages within a partition are ordered.
- **Offset**: Each message in a partition has a sequential offset. Consumers track their offset.
- **Consumer Group**: Multiple consumers in a group split the partitions among themselves (load balancing). Each partition assigned to one consumer in a group.
- **Replication**: Partitions replicated across brokers for durability. Leader handles reads/writes; followers replicate.
- **Retention**: Messages retained for a configurable duration (default 7 days), not deleted after consumption.

**Kafka vs Traditional Queue:**

| Feature | Kafka | RabbitMQ/SQS |
|---------|-------|-------------|
| Retention | Configurable duration | Deleted after ACK |
| Replay | Yes (seek to any offset) | No |
| Throughput | Millions/sec | Thousands/sec |
| Ordering | Per-partition | Per-queue (with some caveats) |
| Consumer model | Pull | Push or Pull |
| Use cases | Event sourcing, stream processing, audit logs | Task queues, RPC, work distribution |

### Event-Driven Architecture Patterns

**Event Sourcing:**
Instead of storing current state, store a sequence of all events that led to that state. Current state is derived by replaying events. Enables full audit trail, time travel, event replay.

**CQRS (Command Query Responsibility Segregation):**
Separate read (query) and write (command) models. Writes update the event log; reads use a denormalized read model optimized for queries. Improves read performance and scalability.

**Saga Pattern:**
Manages long-running distributed transactions across multiple services without 2PC. Each step publishes an event. If a step fails, compensating transactions are triggered.

**Choreography-based Saga**: Services react to events; no central coordinator.
**Orchestration-based Saga**: A central orchestrator sends commands to each service.

---

## 19. Databases and Network Considerations

### Client-Server Communication

Database drivers maintain a connection pool to the database server. Each connection is a TCP socket. Key considerations:
- **Connection limit**: Databases like PostgreSQL have a max_connections limit (default ~100).
- **Connection pooling**: Use PgBouncer (PostgreSQL), ProxySQL (MySQL) to multiplex many app connections to few DB connections.
- **Connection latency**: Establishing a connection costs time (~5-20ms). Always pool.
- **Network placement**: Database should be in the same region/availability zone as the application to minimize latency.

### Read Replicas

For read-heavy workloads, add read replicas. Writes go to the primary; reads are distributed across replicas. Replication is typically asynchronous (slight lag — replication lag).

```
Application
    ↓ writes          ↓ reads
[Primary DB] → async replication → [Replica 1]
                                 → [Replica 2]
```

### Database Proxies

Proxies like PgBouncer, RDS Proxy, ProxySQL sit between the application and database:
- Multiplex many app connections into fewer DB connections
- Transparent failover during database restarts
- Connection management and monitoring

---

## 20. Replication and Consistency

### Replication

Replication copies data to multiple nodes. Goals: durability, availability, read scalability.

**Single-Leader (Primary-Replica) Replication:**
- One primary accepts all writes.
- Replicas receive changes asynchronously.
- Reads can go to any replica.
- Failover: promote a replica to primary.

**Multi-Leader Replication:**
- Multiple primaries accept writes.
- Leaders sync with each other.
- Use case: multi-region, offline-first apps.
- Conflict resolution required (last-write-wins, application-level merge).

**Leaderless (Dynamo-style) Replication:**
- Any node can accept writes.
- Write to W nodes; read from R nodes; if W + R > N (total nodes), reads see latest write.
- Used by Cassandra, DynamoDB, Riak.

### Consistency Models

**Strong Consistency:**
All reads see the most recent write, as if there were only one copy. Highest guarantee. Requires coordination across nodes. Examples: single-node DB, Google Spanner.

**Eventual Consistency:**
Given no new updates, all replicas will converge to the same value eventually. No guarantee on when. High availability, low latency. Examples: Cassandra, DynamoDB (by default), DNS.

**Causal Consistency:**
Reads respect causal order — if you write X then read it, you always see X (even on different nodes).

**Read-Your-Writes Consistency:**
After writing, the same user's subsequent reads see that write. Achieved by routing the user's reads to the primary or to the replica that has caught up.

**Monotonic Reads:**
A client never sees an older version of data after seeing a newer version. Prevents time-travel reads.

**ACID vs BASE:**

| ACID (Relational DBs) | BASE (NoSQL/Distributed) |
|----------------------|--------------------------|
| Atomicity | Basically Available |
| Consistency | Soft state |
| Isolation | Eventual consistency |
| Durability | |

---

## 21. Partitioning and Sharding

Partitioning distributes data across multiple nodes to scale horizontally beyond what a single node can handle.

### Horizontal vs Vertical Partitioning

**Horizontal Partitioning (Sharding):**
Rows are distributed across multiple tables/nodes (each shard holds a subset of rows).

**Vertical Partitioning:**
Columns are split across tables. Infrequently accessed or large columns moved to a separate table.

### Sharding Strategies

**Range-Based Sharding:**
Assign shards based on a range of the partition key.
- Shard 1: user_id 1–1,000,000
- Shard 2: user_id 1,000,001–2,000,000
Pros: Supports range queries. Cons: Hotspots if traffic concentrates in a range (new users all go to last shard).

**Hash-Based Sharding:**
Hash the partition key, take modulo of shard count.
`shard = hash(user_id) % num_shards`
Pros: Even distribution. Cons: Cannot do range queries; resharding is expensive (all data remaps).

**Consistent Hashing:**
Nodes and keys are mapped to a ring. Each key assigned to the nearest node clockwise on the ring. Adding/removing nodes only remaps data from/to adjacent nodes (minimal reshuffling).
Used by: Cassandra, DynamoDB, Memcached (with consistent hashing clients), CDNs.

**Directory-Based Sharding:**
A lookup table maps keys to shards. Flexible — any key can be on any shard. But the lookup table is a single point of failure and bottleneck.

### Challenges of Sharding

- **Cross-shard queries**: JOINs across shards require querying multiple shards and merging results.
- **Distributed transactions**: ACID transactions across shards require 2PC, which is slow and complex.
- **Rebalancing**: Adding/removing shards requires moving data. Consistent hashing minimizes this.
- **Hotspots**: Some shards receive disproportionately more traffic. Need to monitor and re-shard.

---

## 22. CAP Theorem and Distributed Systems Theory

### CAP Theorem

**In a distributed system, you can guarantee at most 2 of these 3 properties:**

- **Consistency (C)**: Every read receives the most recent write or an error.
- **Availability (A)**: Every request receives a non-error response (but not necessarily the latest data).
- **Partition Tolerance (P)**: The system continues operating despite network partitions (message loss/delays between nodes).

**Network partitions are unavoidable** in any distributed system. Therefore, the real trade-off is:
- **CP systems**: Choose consistency over availability when a partition occurs. Refuse to serve stale data. Examples: HBase, Zookeeper, etcd.
- **AP systems**: Choose availability over consistency when a partition occurs. May serve stale data. Examples: Cassandra, DynamoDB, CouchDB.

**CA systems** (no partition tolerance): Only possible for single-node systems; not practical for distributed deployments.

### PACELC Theorem (Extension of CAP)

PACELC adds that even without partitions, there's a trade-off between latency and consistency:

"If there is a **P**artition, the system must trade off between **A**vailability and **C**onsistency. **E**lse (no partition), the system must trade off between **L**atency and **C**onsistency."

Example: Cassandra is PA/EL (available during partition, lower latency over consistency when healthy).

### Fallacies of Distributed Computing

Classic assumptions that cause design mistakes:

1. The network is reliable.
2. Latency is zero.
3. Bandwidth is infinite.
4. The network is secure.
5. Topology doesn't change.
6. There is one administrator.
7. Transport cost is zero.
8. The network is homogeneous.

Design your system to handle all of these being false.

### Two Generals Problem

A thought experiment showing that 100% reliable consensus is impossible over an unreliable communication channel. No matter how many ACKs are exchanged, certainty can never be guaranteed. Real systems accept probabilistic guarantees.

### Byzantine Fault Tolerance

A system is Byzantine fault tolerant if it continues to function even when some nodes behave arbitrarily (send false data, crash, lie). Relevant for blockchain and adversarial environments. Not usually required for internal distributed systems.

### Consensus Algorithms

Used to get agreement among distributed nodes:

**Raft**: Understandable leader-based consensus. A leader is elected; all writes go through the leader; followers replicate. Used by: etcd, CockroachDB, TiKV.

**Paxos**: The classic consensus algorithm. Correct but famously difficult to understand and implement. Used by: Google Chubby, Google Spanner.

**Zab (Zookeeper Atomic Broadcast)**: Used by Apache ZooKeeper. Similar to Raft in principles.

---

## 23. Security in Networked Systems

### TLS/SSL

Covered in Section 6. Key points:
- Always use TLS 1.2 minimum; prefer TLS 1.3.
- Use HSTS (HTTP Strict Transport Security) to prevent downgrade attacks.
- Certificate rotation: automate with Let's Encrypt / ACME protocol.
- Certificate pinning: pin the expected cert in clients (mobile apps) to prevent MITM even with valid certs.

### Authentication and Authorization

**Authentication (AuthN)**: Who are you? Verify identity.
**Authorization (AuthZ)**: What can you do? Verify permissions.

**Methods:**
- **API Keys**: Simple, long-lived secrets. Easy but no expiry, hard to rotate.
- **Basic Auth**: Base64-encoded username:password in header. Only acceptable over HTTPS.
- **JWT (JSON Web Token)**: Self-contained token. Header.Payload.Signature. Server validates signature without DB lookup. Stateless. Revocation is hard.
- **OAuth 2.0**: Authorization framework. Allows third-party apps to access resources without sharing credentials. Roles: Resource Owner, Client, Authorization Server, Resource Server.
- **OIDC (OpenID Connect)**: Authentication layer on top of OAuth 2.0. Provides ID tokens.
- **mTLS (Mutual TLS)**: Both client and server verify each other's certificates. Used in microservices (zero-trust networks).

### Common Attacks and Mitigations

**DDoS (Distributed Denial of Service):**
Flood a server with traffic to overwhelm it.
Mitigations: CDN/WAF (absorbs traffic), rate limiting, Anycast routing, scrubbing centers.

**SQL Injection:**
Attacker inserts malicious SQL into inputs.
Mitigations: Parameterized queries/prepared statements. NEVER concatenate user input into SQL.

**XSS (Cross-Site Scripting):**
Attacker injects scripts into web pages viewed by others.
Mitigations: Output encoding, Content Security Policy (CSP) headers.

**CSRF (Cross-Site Request Forgery):**
Forces authenticated user to make unintended requests.
Mitigations: CSRF tokens, SameSite cookie attribute, checking Origin/Referer headers.

**MITM (Man in the Middle):**
Attacker intercepts communication.
Mitigations: TLS, certificate pinning, HSTS.

**Replay Attack:**
Attacker captures a valid request and replays it.
Mitigations: Nonces (one-time use tokens), timestamps, HMAC with short expiry.

### Network Security Layers

**Firewall:**
Filters traffic based on IP, port, protocol. Stateful firewalls track TCP connections. Network ACLs (AWS) are stateless.

**WAF (Web Application Firewall):**
L7 firewall that inspects HTTP. Blocks known attack patterns (OWASP Top 10). Examples: AWS WAF, Cloudflare WAF, ModSecurity.

**VPC (Virtual Private Cloud):**
Logically isolated network in the cloud. Resources inside a VPC are not directly accessible from the internet unless explicitly exposed. Use private subnets for databases and internal services.

**Zero Trust Network:**
No implicit trust based on network location. Every request authenticated and authorized regardless of source (internal or external). Implemented with: identity-aware proxies, mTLS, continuous verification.

**Secrets Management:**
Never hardcode secrets in code or config files. Use: HashiCorp Vault, AWS Secrets Manager, GCP Secret Manager. Rotate secrets regularly.

---

## 24. Network Reliability and Fault Tolerance

### High Availability (HA)

Availability = uptime / total time. Expressed as nines:

| Availability | Downtime/Year | Downtime/Month |
|-------------|--------------|----------------|
| 99% (2 nines) | 87.6 hours | 7.2 hours |
| 99.9% (3 nines) | 8.76 hours | 43.8 minutes |
| 99.99% (4 nines) | 52.6 minutes | 4.38 minutes |
| 99.999% (5 nines) | 5.26 minutes | 26.3 seconds |

Achieving high availability requires eliminating single points of failure (SPOF), redundancy at every layer, and automated failover.

### Redundancy Patterns

**Active-Active**: Multiple instances running simultaneously, all serving traffic. Failure of one reduces capacity but doesn't cause outage.

**Active-Passive**: One active instance serves traffic; passive instance is on standby. On failure, passive is promoted. Requires failover time.

**N+1 Redundancy**: Always have one more server than needed. Tolerates one failure with full capacity.

**Geographic Redundancy**: Resources in multiple regions. Protects against regional outages, natural disasters.

### Failure Modes

**Hardware failure**: Server crash, disk failure, NIC failure. Mitigated by redundant hardware, RAID, cloud providers' managed hardware.

**Network partition**: Two parts of a distributed system cannot communicate. Each side may continue operating independently.

**Data corruption**: Silent data corruption (bit rot) or bug-induced corruption. Mitigated by checksums, redundancy, regular backups.

**Cascading failures**: A failure in one component overloads adjacent components, causing them to fail too. Mitigated by circuit breakers, bulkheads, rate limiting.

**Gray failure**: Component partially working — slow but not down. Harder to detect than complete failures. Health checks must test functionality, not just liveness.

### SLA, SLO, SLI

**SLI (Service Level Indicator)**: A specific measurable metric. E.g., 99th percentile request latency = 200ms.

**SLO (Service Level Objective)**: A target for an SLI. E.g., 99th percentile latency ≤ 300ms for 99.9% of requests in a month.

**SLA (Service Level Agreement)**: A contract with customers. If SLO is missed, consequences apply (refunds, credits).

**Error Budget**: 100% - SLO target. If your SLO is 99.9% availability, you have 0.1% = 43.8 min/month error budget. Use it intentionally; don't exceed it accidentally.

---

## 25. Observability: Monitoring, Logging, Tracing

### The Three Pillars of Observability

**Metrics**: Numerical time-series data. Aggregated, low cardinality. Examples: requests per second, error rate, CPU usage, p99 latency.

**Logs**: Timestamped records of discrete events. High cardinality. Examples: request/response logs, application events, errors.

**Traces**: Records of a single request's journey across services. Shows latency breakdown, errors, causality.

### Metrics

**Counter**: Monotonically increasing number. Always incrementing. Track: total requests, total errors.

**Gauge**: Value that can go up and down. Track: active connections, memory usage, queue depth.

**Histogram**: Distribution of values across configurable buckets. Compute percentiles (p50, p95, p99). Track: request latency distribution.

**Summary**: Pre-computed quantiles. Less flexible than histograms for aggregation.

**RED Method** (for services):
- **R**ate: requests per second
- **E**rrors: error rate
- **D**uration: response time distribution

**USE Method** (for resources):
- **U**tilization: % time busy
- **S**aturation: amount of work queued
- **E**rrors: error events

### Distributed Tracing

A trace represents the execution of a request across multiple services. Key concepts:
- **Trace ID**: Unique ID for the entire request journey.
- **Span**: A single operation within a trace (e.g., one service call). Has start time, duration, name, metadata.
- **Span Context**: Propagated across service calls via headers (W3C Trace Context: `traceparent`, `tracestate`).
- **Parent Span / Child Span**: Spans form a tree structure.

**Trace propagation headers (W3C):**
```
traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01
```

**Tools**: Jaeger, Zipkin, AWS X-Ray, Datadog APM, Honeycomb, OpenTelemetry (standardized instrumentation).

### Logging Best Practices

- Use structured logging (JSON). Machine-parseable, indexable.
- Include trace IDs in every log line for correlation.
- Log at appropriate levels: DEBUG, INFO, WARN, ERROR, FATAL.
- Don't log sensitive data (PII, credentials).
- Centralize logs (ELK Stack: Elasticsearch, Logstash, Kibana; or Loki + Grafana; or Datadog, Splunk).
- Implement log sampling for high-volume, low-value logs.

### Alerting

Alert on symptoms (user-visible problems), not causes. Follow the SLO/error-budget approach:
- Alert when error budget burn rate exceeds threshold.
- Avoid alert fatigue by ensuring alerts are actionable.

---

## 26. Cloud Networking

### Virtual Private Cloud (VPC)

A VPC is an isolated network within a cloud provider. You define:
- **CIDR range**: The IP address space (e.g., `10.0.0.0/16`)
- **Subnets**: Subdivisions (e.g., `10.0.1.0/24`). Can be public or private.
- **Route Tables**: Control where traffic is directed.
- **Internet Gateway**: Allows public internet access for public subnets.
- **NAT Gateway**: Allows private subnet instances to initiate internet connections (no inbound).
- **Security Groups**: Stateful, instance-level firewall rules.
- **Network ACLs**: Stateless, subnet-level firewall rules.

### VPC Peering and Transit Gateway

**VPC Peering**: Connect two VPCs privately. Traffic stays on the cloud backbone. Non-transitive (A-B and B-C doesn't give A-C).

**Transit Gateway**: Hub for connecting many VPCs and on-premises networks. Transitive routing. Scales better than many peering connections.

### PrivateLink / Private Endpoints

Access cloud services (S3, DynamoDB, SQS) from within your VPC without traversing the internet. Improved security and latency.

### Multi-Region and Multi-AZ Architecture

**Availability Zone (AZ)**: Isolated data centers within a region. Physically separated but low-latency interconnect.

**Region**: Geographic area with multiple AZs (e.g., us-east-1 has 6 AZs).

**Best practices:**
- Deploy to 2+ AZs for high availability within a region.
- Deploy to 2+ regions for disaster recovery and global latency.
- Use regional load balancers (ALB) that span AZs.
- Use global load balancing (Route 53, Cloudflare) to route between regions.
- Replicate databases across AZs/regions.

### Cloud Load Balancers (AWS Example)

- **ALB (Application Load Balancer)**: L7, HTTP/HTTPS, WebSocket. Best for web applications.
- **NLB (Network Load Balancer)**: L4, TCP/UDP. Ultra-low latency, preserves client IP, handles millions of requests/sec.
- **CLB (Classic Load Balancer)**: Legacy, L4/L7. Use ALB or NLB instead.
- **GWLB (Gateway Load Balancer)**: For deploying network appliances (firewalls, intrusion detection).

---

## 27. Rate Limiting and Throttling

Rate limiting controls how many requests a client can make in a given time window. Protects against abuse, DDoS, and overload.

### Rate Limiting Algorithms

**Fixed Window Counter:**
Count requests in a fixed time window (e.g., 100 requests per minute). Reset counter at the start of each window.
Problem: burst at window boundary (99 requests at end of window + 99 at start of next = 198 in 2 seconds).

**Sliding Window Log:**
Store timestamp of every request. On each request, remove timestamps older than the window, count remaining.
Accurate but memory-intensive (stores every request timestamp).

**Sliding Window Counter:**
Combine fixed window and sliding window using weighted counts. More memory-efficient than log.

**Token Bucket:**
A bucket holds tokens. Tokens are added at a constant rate (e.g., 10/sec) up to a capacity. Each request consumes one token. If empty, request is rejected.
Allows bursting up to bucket capacity while enforcing average rate.

**Leaky Bucket:**
Requests enter a queue (bucket). Requests leave the bucket at a constant rate (like water leaking). Queue overflow = rejected.
Smooths traffic to a constant rate. Good for protecting backends from bursts.

### Rate Limiting in Distributed Systems

Each server maintaining its own counter leads to inconsistency (a client can exceed the limit by hitting different servers).

Solutions:
- **Centralized counter**: All servers update a shared counter in Redis using atomic operations (`INCR`, `EXPIRE`). Consistent but adds Redis latency.
- **Sticky routing**: Always route the same client to the same server (IP hash). Local counter works but limits scalability.
- **Approximate counting**: Each server maintains a local counter and periodically syncs with Redis. Small window of inconsistency; much higher performance.

### Rate Limit Response

```
HTTP/1.1 429 Too Many Requests
Retry-After: 30
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1687000000
```

---

## 28. System Design Patterns

### Data Ingestion Patterns

**Fan-Out on Write**: When a user posts, immediately fan out to all followers' feeds. Reads are fast (pre-computed feed). Writes are expensive for users with millions of followers.

**Fan-Out on Read**: Fetch from all followed users and aggregate at read time. Reads are slower but writes are cheap. Common for users with few followers.

**Hybrid**: Fan-out on write for non-celebrity users; fan-out on read for celebrities (too expensive to push to millions of followers).

### Storage Patterns

**CQRS + Event Sourcing**: Write events to the event log; derive read models from events. Full audit trail, easy reprocessing, temporal queries.

**Lambda Architecture**: Process data in two paths: batch (reprocesses all historical data, high accuracy) and speed (processes recent data in real time, low latency). Merge results for complete view.

**Kappa Architecture**: Simplification of Lambda. Use only stream processing (Kafka + Flink/Spark Streaming). Replay historical data by reprocessing the stream from beginning. Simpler but requires retaining all events.

### URL Shortener (Classic System Design)

**Components**: URL generator (Base62 encoding of auto-increment ID), distributed ID generator (Snowflake), cache (Redis) for hot URLs, DB for all mappings, CDN/reverse proxy for redirects.

**Redirect types**: 301 (permanent, client caches — reduces server load but loses analytics) vs 302 (temporary, always hits server — better for analytics).

### Notification System

Push to users across channels: SMS, email, in-app push notifications. Key components: event triggers (user actions), notification service, per-channel delivery workers, retry with exponential backoff, user preference store (which channels enabled, quiet hours), rate limiting per user per channel.

### Search Autocomplete

Trie data structure for prefix matching. Cache popular queries. Aggregate query logs for suggestions. CDN cache autocomplete responses. Rank by frequency + freshness.

---

## 29. Scalability Principles

### Scaling Dimensions

**Vertical Scaling (Scale Up)**: Increase resources on a single node (more CPU, RAM, faster disk). Simple, no architectural changes. Hard limit exists. Single point of failure.

**Horizontal Scaling (Scale Out)**: Add more nodes. Requires stateless application design, load balancing, distributed data management. No hard limit. Higher complexity.

**Stateless Services**: Services that don't store state between requests (no session state in memory). Any instance can handle any request. Prerequisites for horizontal scaling.

### Performance vs Scalability

- **Performance**: Single request latency. How fast is one request?
- **Scalability**: How does performance change as load increases? Ideally linear.

A slow system has a performance problem. A system that is fast for one user but slow for many has a scalability problem.

### Amdahl's Law

The maximum speedup from parallelization is limited by the sequential fraction of the program.

`Speedup = 1 / (S + (1 - S) / N)`

Where S = sequential fraction, N = processors. Even with infinite processors, speedup is limited to 1/S.

Implication: Eliminate sequential bottlenecks (global locks, single-threaded queues) to achieve meaningful scalability.

### Bottleneck Analysis

Every system has a bottleneck — the single constraint limiting throughput. Use the Theory of Constraints:
1. Identify the bottleneck (profiling, metrics).
2. Exploit it (maximize its throughput).
3. Subordinate everything else to it.
4. Elevate it (scale the bottleneck specifically).
5. Find the new bottleneck.

### Capacity Planning

Estimate future load and ensure you have sufficient capacity:
- Back-of-envelope calculations: QPS, storage growth rate, bandwidth.
- Load testing: simulate expected and peak traffic.
- Auto-scaling: automatically add/remove instances based on metrics.

**Example calculation:**
- 10 million daily active users
- Average 10 requests per day per user
- = 100 million requests/day = ~1,157 req/sec average
- Peak = 3x average = ~3,500 req/sec
- Each server handles 1,000 req/sec → need ~4 servers at peak

---

## 30. Advanced Topics

### Long-Tail Latency (Tail Latency)

The 99th or 99.9th percentile latency is often much higher than median latency. Small percentages of slow requests degrade user experience, especially in microservices where a single user request fans out to many services (tail amplification).

**Mitigations:**
- **Hedged requests**: Send the same request to two servers, use whichever responds first.
- **Tied requests**: Send to one server, if no response in 95th percentile time, send to second as well.
- Reduce GC pauses, eliminate lock contention, use consistent hashing.

### Backpressure

When a producer sends data faster than a consumer can process, backpressure is the mechanism that signals the producer to slow down:
- TCP receive window (flow control)
- Message queue depth signaling
- gRPC flow control
- Reactive Streams standard

Without backpressure, queues grow unbounded, causing memory exhaustion and latency spikes.

### Connection Draining

When taking a server out of rotation (for deployment or scaling down), send no new requests but allow existing connections to complete. Cloud load balancers support configurable draining periods (30-300 seconds).

### Graceful Degradation

Provide reduced functionality rather than complete failure when a dependency is unavailable. Examples:
- Show cached results when the search service is down.
- Show a static home page if the recommendation engine is unavailable.
- Disable non-critical features (A/B tests, analytics) under high load.

### Dark Launching / Traffic Shadowing

Mirror production traffic to a new version without affecting users. Compare results. Useful for testing new systems under real load before cutting over.

### Chaos Engineering

Deliberately inject failures into production to test system resilience. Netflix pioneered this with Chaos Monkey (randomly terminates EC2 instances). Principles:
1. Define a "steady state" (normal behavior metrics).
2. Hypothesize that steady state will continue.
3. Introduce variables (server failure, network delay, DNS outage).
4. Disprove the hypothesis → fix the weakness.

Tools: Chaos Monkey, Gremlin, AWS Fault Injection Simulator.

### Network Protocols in Depth

**BGP (Border Gateway Protocol):**
The routing protocol of the internet. Autonomous Systems (AS) — large networks operated by ISPs, cloud providers — exchange routing information via BGP. BGP determines which path traffic takes across the internet. BGP hijacking is a real security concern.

**OSPF (Open Shortest Path First):**
Link-state routing protocol used within a network (interior gateway protocol). Routers share topology and compute shortest paths using Dijkstra's algorithm.

**ECMP (Equal-Cost Multi-Path):**
When multiple equal-cost paths exist to a destination, traffic is load-balanced across them. Used in data center networks for bandwidth aggregation.

**VXLAN (Virtual Extensible LAN):**
Encapsulates Layer 2 Ethernet frames within UDP packets. Enables virtual networks to span physical network boundaries. Used in cloud and data center overlay networks (Kubernetes networking, OpenStack).

**DPDK (Data Plane Development Kit):**
Bypasses the kernel network stack; processes packets in user space at very high speed. Used in network functions (firewalls, routers) requiring millions of packets per second.

**RDMA (Remote Direct Memory Access):**
Allows one computer to directly read/write another computer's memory without involving the CPU or OS. Ultra-low latency, zero-copy. Used in HPC and high-performance distributed databases.

### eBPF (Extended Berkeley Packet Filter)

eBPF allows running sandboxed programs in the Linux kernel without changing kernel source. Used for:
- High-performance networking (Cilium — eBPF-based Kubernetes CNI)
- Observability (zero-overhead profiling, tracing without instrumentation)
- Security (runtime security enforcement)
- Load balancing (Cloudflare, Meta use eBPF for XDP-based routing)

### P2P Networking

Peers both provide and consume resources (no dedicated client-server roles). Examples: BitTorrent, Blockchain.

**DHT (Distributed Hash Table)**: Decentralized key-value store used in P2P systems. Each node responsible for a portion of the keyspace. Used by BitTorrent (Kademlia DHT), IPFS.

### IPFS (InterPlanetary File System)

Content-addressable, peer-to-peer distributed file system. Files referenced by their hash (CID). Location-independent addressing. Basis for decentralized web (Web3).

### Time Synchronization

Distributed systems require synchronized clocks for:
- Ordering events
- TTL / lease expiration
- Distributed tracing

**NTP (Network Time Protocol)**: Synchronizes clocks over a network. Typical accuracy: 1-100ms. GPS-disciplined NTP servers achieve microsecond accuracy.

**Logical Clocks (Lamport Timestamps)**: If A happens before B, clock(A) < clock(B). Tracks causal ordering without wall-clock time.

**Vector Clocks**: Track causal history across multiple nodes. Can detect concurrent events.

**Hybrid Logical Clocks (HLC)**: Combine physical and logical time. Used by CockroachDB, YugabyteDB.

**Google TrueTime**: GPS + atomic clocks in every data center. Bounded uncertainty (~7ms). Enables Spanner's globally consistent transactions.

### CORS (Cross-Origin Resource Sharing)

A browser security mechanism that restricts web pages from making requests to a different domain. The server must explicitly allow cross-origin requests via headers:

```
Access-Control-Allow-Origin: https://example.com
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Max-Age: 86400
```

Preflight requests (OPTIONS) are sent by browsers for non-simple requests before the actual request.

### WebRTC (Web Real-Time Communication)

Browser API for peer-to-peer audio, video, and data communication. Components:
- **ICE (Interactive Connectivity Establishment)**: Finds the best path between peers (direct, via STUN, via TURN).
- **STUN (Session Traversal Utilities for NAT)**: Helps peers discover their public IP and port.
- **TURN (Traversal Using Relays around NAT)**: Relay server when direct P2P is impossible (symmetric NAT).
- **SDP (Session Description Protocol)**: Describes the media capabilities of each peer.
- **DTLS**: TLS for UDP (encrypts the data channel).
- **SRTP**: Secure RTP for audio/video.

### IPVS (IP Virtual Server) and XDP

**IPVS**: Kernel-level L4 load balancing. Much faster than iptables for large rule sets. Used by Kubernetes (kube-proxy in IPVS mode).

**XDP (eXpress Data Path)**: eBPF programs that run at the earliest possible point in the network stack (on the NIC driver). Can forward or drop packets before they reach the kernel network stack. Used for DDoS mitigation and high-speed routing.

---

## Quick Reference Cheat Sheet

### Common Port Numbers

| Port | Service |
|------|---------|
| 22 | SSH |
| 25 | SMTP |
| 53 | DNS (UDP/TCP) |
| 80 | HTTP |
| 443 | HTTPS |
| 3306 | MySQL |
| 5432 | PostgreSQL |
| 6379 | Redis |
| 9092 | Kafka |
| 2181 | ZooKeeper |
| 8080 | HTTP alternate |
| 27017 | MongoDB |

### Back-of-Envelope Constants

| Item | Value |
|------|-------|
| Read 1 MB from RAM | 250 µs |
| Read 1 MB from SSD | 1 ms |
| Read 1 MB from HDD | 20 ms |
| RTT same datacenter | 0.5 ms |
| RTT cross-continent | 100–150 ms |
| Disk seek (HDD) | 10 ms |
| Mutex lock/unlock | 25 ns |
| DNS lookup | 1–100 ms |
| TCP handshake | 1 RTT |
| TLS 1.3 handshake | 1 RTT |
| L1 cache reference | 1 ns |
| L2 cache reference | 4 ns |
| Branch misprediction | 5 ns |
| Main memory access | 100 ns |

### HTTP Status Code Quick Reference

| Code | Meaning |
|------|---------|
| 200 | OK |
| 201 | Created |
| 204 | No Content |
| 301 | Moved Permanently |
| 302 | Found (Redirect) |
| 304 | Not Modified |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 409 | Conflict |
| 422 | Unprocessable Entity |
| 429 | Too Many Requests |
| 500 | Internal Server Error |
| 502 | Bad Gateway |
| 503 | Service Unavailable |
| 504 | Gateway Timeout |

---

*This document is intended as a comprehensive reference for instructing AI systems, engineering study, and system design preparation. All concepts range from foundational networking principles to advanced distributed systems topics.*
