# System Design Reference Guide

> A complete reference for designing scalable, reliable, and maintainable systems — covering core concepts, patterns, and architecture-specific deep dives.

---

## Table of Contents

1. [Fundamentals](#1-fundamentals)
2. [Key Properties of Distributed Systems](#2-key-properties-of-distributed-systems)
3. [Networking & Communication](#3-networking--communication)
4. [Data Storage & Databases](#4-data-storage--databases)
5. [Caching](#5-caching)
6. [Load Balancing](#6-load-balancing)
7. [Scalability Patterns](#7-scalability-patterns)
8. [Reliability & Fault Tolerance](#8-reliability--fault-tolerance)
9. [Consistency & Consensus](#9-consistency--consensus)
10. [Messaging & Event Streaming](#10-messaging--event-streaming)
11. [API Design](#11-api-design)
12. [Security](#12-security)
13. [Observability](#13-observability)
14. [Architecture Patterns](#14-architecture-patterns)
    - [Monolith](#141-monolith)
    - [Microservices](#142-microservices)
    - [Event-Driven Architecture](#143-event-driven-architecture)
    - [Serverless](#144-serverless)
    - [CQRS & Event Sourcing](#145-cqrs--event-sourcing)
    - [Lambda Architecture](#146-lambda-architecture)
    - [Kappa Architecture](#147-kappa-architecture)
    - [Hexagonal / Clean Architecture](#148-hexagonal--clean-architecture)
    - [Service Mesh](#149-service-mesh)
    - [Cell-Based Architecture](#1410-cell-based-architecture)
15. [System Design Interview Framework](#15-system-design-interview-framework)
16. [Common System Design Problems](#16-common-system-design-problems)
17. [Back-of-the-Envelope Estimation](#17-back-of-the-envelope-estimation)
18. [Glossary](#18-glossary)

---

## 1. Fundamentals

### What Is System Design?

System design is the process of defining the architecture, components, data flows, and interfaces of a system to satisfy specified requirements. It bridges the gap between business requirements and technical implementation.

### Goals of a Good System Design

- **Scalability** — handle growth in load/data
- **Reliability** — continue working despite failures
- **Availability** — uptime and accessibility
- **Maintainability** — easy to modify and extend
- **Performance** — low latency, high throughput
- **Security** — protection against threats
- **Cost efficiency** — reasonable infrastructure cost

### Design Process

```
Requirements → Estimation → High-Level Design → Deep Dive → Trade-offs
```

---

## 2. Key Properties of Distributed Systems

### CAP Theorem

A distributed system can guarantee only **two** of the three:

| Property | Meaning |
|----------|---------|
| **C**onsistency | Every read receives the most recent write |
| **A**vailability | Every request receives a response (not necessarily latest) |
| **P**artition Tolerance | System continues despite network partition |

Network partitions are unavoidable → real choice is **CP vs AP**.

- **CP systems**: HBase, Zookeeper, MongoDB (w/ strong consistency)
- **AP systems**: Cassandra, CouchDB, DynamoDB (w/ eventual consistency)

### PACELC Theorem (Extension of CAP)

Even without partitions, trade-offs exist:

```
If Partition: trade Availability vs Consistency
Else:        trade Latency vs Consistency
```

### BASE vs ACID

| ACID (Traditional DBs) | BASE (NoSQL) |
|------------------------|--------------|
| Atomicity | Basically Available |
| Consistency | Soft state |
| Isolation | Eventually consistent |
| Durability | |

---

## 3. Networking & Communication

### Protocols

| Protocol | Layer | Use Case |
|----------|-------|----------|
| TCP | Transport | Reliable, ordered delivery |
| UDP | Transport | Low latency, loss-tolerant (gaming, video) |
| HTTP/1.1 | Application | Web, REST APIs |
| HTTP/2 | Application | Multiplexing, header compression |
| HTTP/3 | Application | QUIC-based, reduced handshake |
| WebSockets | Application | Bidirectional, real-time |
| gRPC | Application | High-performance RPC (HTTP/2 + Protobuf) |
| MQTT | Application | IoT, lightweight pub/sub |

### Communication Patterns

**Synchronous**
- Client waits for response
- Simple, but tight coupling
- Examples: REST, gRPC

**Asynchronous**
- Client doesn't wait; uses queues or callbacks
- Loose coupling, better resilience
- Examples: Message queues, event streaming

**Long Polling**
- Client holds connection open until server has data
- Simpler than WebSockets; higher overhead

**Server-Sent Events (SSE)**
- One-way stream from server to client
- Good for live feeds, notifications

**WebSockets**
- Full-duplex, persistent connection
- Use for: chat, live dashboards, collaborative tools

### DNS (Domain Name System)

```
Browser → Recursive Resolver → Root NS → TLD NS → Authoritative NS → IP
```

- **TTL** controls caching duration
- **GeoDNS** routes users to nearest region
- **Anycast** routes to nearest data center for same IP

### CDN (Content Delivery Network)

- Distributes static assets globally
- Reduces latency via edge servers close to users
- **Push CDN**: manually upload content
- **Pull CDN**: fetches from origin on cache miss

---

## 4. Data Storage & Databases

### SQL (Relational Databases)

**When to use**: Structured data, complex queries, ACID transactions, relationships

| DB | Best For |
|----|----------|
| PostgreSQL | General purpose, JSON support, extensions |
| MySQL | Web apps, high read throughput |
| SQLite | Embedded, local storage |
| Amazon Aurora | Cloud-native, auto-scaling |

**Key concepts**:
- Normalization (1NF, 2NF, 3NF, BCNF)
- Indexes (B-Tree, Hash, GiST, Full-text)
- Joins (INNER, LEFT, RIGHT, FULL, CROSS)
- Transactions & isolation levels (READ UNCOMMITTED → SERIALIZABLE)

**Partitioning** (within a single database):
- **Horizontal partitioning (sharding)**: split rows across tables/nodes
- **Vertical partitioning**: split columns across tables

**Replication**:
- **Master-slave**: writes to master, reads from slaves
- **Multi-master**: writes to multiple nodes (conflict resolution needed)

### NoSQL Databases

**Key-Value Stores**
- Redis, DynamoDB, Memcached
- O(1) reads/writes, simple data model
- Use for: caching, sessions, leaderboards

**Document Stores**
- MongoDB, CouchDB, Firestore
- JSON-like documents, flexible schema
- Use for: user profiles, catalogs, CMS

**Wide-Column Stores**
- Apache Cassandra, HBase, Bigtable
- Column families, optimized for writes
- Use for: time-series, IoT, analytics

**Graph Databases**
- Neo4j, Amazon Neptune, ArangoDB
- Nodes and edges, relationship-first
- Use for: social networks, recommendation engines, fraud detection

**Time-Series Databases**
- InfluxDB, TimescaleDB, Prometheus
- Optimized for timestamp-indexed data
- Use for: metrics, monitoring, IoT sensor data

**Search Engines**
- Elasticsearch, OpenSearch, Solr
- Full-text search, inverted indexes
- Use for: e-commerce search, log analysis

### Sharding

Distributing data across multiple nodes (shards).

**Sharding strategies**:
- **Hash sharding**: `shard = hash(key) % N` — even distribution, hard to rebalance
- **Range sharding**: by key range — good for range queries, risk of hotspots
- **Directory sharding**: lookup table maps keys to shards — flexible, lookup overhead
- **Geo sharding**: by geography — data locality, regulatory compliance

**Challenges**:
- Cross-shard queries (scatter-gather)
- Rebalancing (consistent hashing helps)
- Hotspots (popular keys overload one shard)

### Consistent Hashing

Places nodes and keys on a virtual ring (0 to 2^32).
- Keys map to nearest clockwise node
- Adding/removing a node only remaps ~K/N keys
- **Virtual nodes**: each physical node owns multiple points → better balance

```
ring: [Node A]---[key1]---[Node B]---[key2,key3]---[Node C]---[Node A]
```

### Indexes

| Type | Structure | Good For |
|------|-----------|----------|
| B-Tree | Balanced tree | Range queries, equality |
| Hash | Hash table | Equality only |
| Bitmap | Bit vectors | Low-cardinality columns |
| GiST / GIN | Generalized | Full-text, geometric |
| Composite | Multi-column | Multi-column filters |
| Covering | Includes all query cols | Index-only scans |

**Index trade-offs**: faster reads, slower writes, more storage.

### Data Warehouses & OLAP

| Feature | OLTP | OLAP |
|---------|------|------|
| Purpose | Transactions | Analytics |
| Query type | Simple, many | Complex, few |
| Schema | Normalized | Star/Snowflake |
| Examples | MySQL, Postgres | Redshift, BigQuery, Snowflake |

---

## 5. Caching

### Cache Levels

```
L1 (CPU cache) → L2/L3 → RAM → Local disk → Distributed cache → Database
```

### Caching Strategies

**Cache-aside (Lazy Loading)**
```
1. Check cache → hit? return data
2. Miss → query DB → store in cache → return data
```
- Application manages cache
- Simple, but cold start on first miss

**Write-through**
```
Write → cache AND DB simultaneously
```
- Always consistent, but write latency doubles

**Write-behind (Write-back)**
```
Write → cache only → async flush to DB
```
- Low latency writes, risk of data loss on crash

**Read-through**
```
Cache sits in front of DB, handles reads automatically
```

**Refresh-ahead**
- Proactively refresh cache before expiry
- Reduces miss rate, wastes resources if data isn't needed

### Cache Eviction Policies

| Policy | Description | Use When |
|--------|-------------|----------|
| LRU | Evict least recently used | General purpose |
| LFU | Evict least frequently used | Popularity-based access |
| MRU | Evict most recently used | Recency-inverse access |
| FIFO | Evict oldest inserted | Simple, predictable |
| TTL | Evict after time-to-live expires | Time-sensitive data |
| Random | Evict random entry | Low overhead |

### Cache Problems

**Cache Stampede (Thundering Herd)**
- Many requests miss simultaneously → DB overloaded
- Solutions: mutex locks, probabilistic early expiry, request coalescing

**Cache Penetration**
- Requests for non-existent keys always miss cache and hit DB
- Solutions: cache null values, Bloom filters

**Cache Avalanche**
- Many keys expire at same time → DB overloaded
- Solutions: random TTL jitter, circuit breakers

**Hotspot / Hot Key**
- One key receives disproportionate traffic
- Solutions: local caching, key splitting, replication

### Distributed Caches

| Tool | Type | Notes |
|------|------|-------|
| Redis | In-memory KV | Persistence, pub/sub, data structures |
| Memcached | In-memory KV | Simple, multi-threaded |
| Hazelcast | In-memory data grid | Distributed computations |
| Apache Ignite | In-memory DB | SQL + compute |

---

## 6. Load Balancing

### Algorithms

| Algorithm | Description | Best For |
|-----------|-------------|----------|
| Round Robin | Rotate through servers | Homogeneous servers |
| Weighted Round Robin | More weight = more traffic | Heterogeneous capacity |
| Least Connections | Send to server with fewest active conns | Long-lived connections |
| IP Hash | Hash client IP to server | Session stickiness |
| Least Response Time | Send to fastest server | Latency-sensitive |
| Random | Pick server randomly | Simple, low overhead |
| Resource Based | Based on CPU/memory | Cloud-native |

### Layer 4 vs Layer 7 Load Balancers

| | Layer 4 (Transport) | Layer 7 (Application) |
|-|---------------------|----------------------|
| Operates on | TCP/UDP | HTTP, HTTPS, WebSocket |
| Can inspect | IP, port | Headers, URL, cookies |
| Speed | Faster | Slightly slower |
| Features | Basic routing | Path-based, host-based routing |
| Examples | HAProxy (TCP mode), AWS NLB | Nginx, HAProxy (HTTP), AWS ALB |

### Health Checks

- **Passive**: detect failure from failed requests
- **Active**: periodic ping to `/health` endpoint
- Remove unhealthy nodes from rotation; re-add after recovery

### Global Load Balancing

- **GeoDNS**: route to nearest region
- **Anycast**: same IP, multiple data centers, BGP routes to nearest
- **Global Server Load Balancer (GSLB)**: health-aware global routing

---

## 7. Scalability Patterns

### Vertical Scaling (Scale Up)

Add more CPU, RAM, disk to one machine.
- Simple, no code changes
- Limited by hardware ceiling
- Single point of failure

### Horizontal Scaling (Scale Out)

Add more machines.
- Theoretically unlimited
- Requires stateless services or distributed state management
- More complex infrastructure

### Stateless vs Stateful Services

| Stateless | Stateful |
|-----------|----------|
| No session data on server | Session data lives on server |
| Easy to scale horizontally | Requires sticky sessions or external state |
| Any node can handle any request | Same node must serve same client |

**Solution**: Move state to external store (Redis, DB) → services become stateless.

### Rate Limiting

Prevent abuse, ensure fair usage.

**Algorithms**:
- **Token Bucket**: tokens accumulate at fixed rate; request consumes a token
- **Leaky Bucket**: requests drain from queue at fixed rate — smooth output
- **Fixed Window Counter**: count requests per time window — burst at boundary
- **Sliding Window Log**: log timestamps of requests — accurate but memory-heavy
- **Sliding Window Counter**: hybrid — efficient and accurate

**Implementation locations**: API Gateway, Nginx, Application layer, Redis-based

### Auto Scaling

- **Horizontal Pod Autoscaler (HPA)**: Kubernetes scales pods on CPU/memory
- **Vertical Pod Autoscaler (VPA)**: adjusts resource requests/limits
- **AWS Auto Scaling Groups**: EC2 instances scale based on CloudWatch metrics
- **Predictive scaling**: ML-based, scales ahead of load

---

## 8. Reliability & Fault Tolerance

### Failure Modes

- **Crash failure**: node stops working
- **Omission failure**: node doesn't respond to some requests
- **Byzantine failure**: node behaves incorrectly or maliciously
- **Network partition**: two groups of nodes can't communicate

### Redundancy Patterns

**Active-Active**: multiple nodes serve traffic simultaneously
**Active-Passive**: one primary, one standby; failover on primary failure

**N+1 redundancy**: one extra unit beyond minimum required
**N+2 redundancy**: two extra units (higher cost, higher safety)

### Replication

**Synchronous replication**:
- Write confirmed only after all replicas acknowledge
- Strong consistency, higher latency

**Asynchronous replication**:
- Write confirmed after primary writes; replicas update eventually
- Lower latency, risk of data loss on primary failure

**Semi-synchronous**:
- At least one replica must acknowledge (compromise)

### Circuit Breaker Pattern

Prevents cascading failures by short-circuiting calls to a failing service.

```
States: CLOSED → OPEN → HALF-OPEN → CLOSED
         (normal)  (fails fast) (probe)
```

- **CLOSED**: requests pass through; failures counted
- **OPEN**: requests fail immediately (no downstream call)
- **HALF-OPEN**: allow limited requests through to test recovery

**Libraries**: Hystrix (legacy), Resilience4j, Polly (.NET)

### Bulkhead Pattern

Isolate components so failure in one doesn't affect others.
- Thread pool bulkheads: separate pools per downstream
- Semaphore bulkheads: limit concurrent calls
- Named after ship compartments that prevent total sinking

### Retry Pattern

Retry failed requests with:
- **Exponential backoff**: `delay = base * 2^attempt`
- **Jitter**: add randomness to prevent synchronized retries
- **Max attempts**: avoid infinite loops

```python
delay = min(cap, base * 2**attempt) + random.uniform(0, jitter)
```

### Timeout Pattern

Always set timeouts on all external calls:
- **Connection timeout**: time to establish connection
- **Read timeout**: time waiting for response data
- **Deadline propagation**: pass remaining time budget through the call chain

### Fallback Pattern

Return a degraded but useful response when a service fails:
- Default value
- Cached response
- Response from alternative service

### Saga Pattern (Distributed Transactions)

Manage long-running transactions across microservices without distributed locks.

**Choreography Saga**: services react to events (decentralized)
**Orchestration Saga**: central coordinator tells each service what to do

Each step has a compensating transaction to undo changes on failure.

---

## 9. Consistency & Consensus

### Consistency Models (Weakest to Strongest)

| Model | Guarantee |
|-------|-----------|
| Eventual | Reads will eventually return latest write |
| Monotonic reads | Never see older data after seeing newer |
| Read-your-writes | Your writes are always visible to you |
| Session consistency | Consistent within a session |
| Causal | Causally related ops maintain order |
| Sequential | All nodes see same operation order |
| Linearizability | Operations appear instantaneous; strongest |

### Consensus Algorithms

**Paxos**:
- Classic consensus algorithm
- Complex to implement correctly
- Used in: Google Chubby, Apache Zookeeper (ZAB variant)

**Raft**:
- Designed for understandability (simpler than Paxos)
- Leader election + log replication
- Used in: etcd, CockroachDB, TiKV, Consul

**Raft lifecycle**:
```
Leader Election → Log Replication → Safety (committed entries never lost)
```

**Byzantine Fault Tolerant (BFT)**:
- Tolerates malicious nodes
- Needs 3f+1 nodes to tolerate f Byzantine failures
- Used in: blockchains (PBFT, Tendermint)

### Quorum

- **N** = total replicas
- **W** = write quorum (nodes that must acknowledge write)
- **R** = read quorum (nodes to read from)
- Strong consistency: `W + R > N`
- Common: N=3, W=2, R=2

---

## 10. Messaging & Event Streaming

### Message Queues vs Event Streams

| Feature | Message Queue | Event Stream |
|---------|--------------|--------------|
| Consumption | Each message consumed once | Consumers read at own offset |
| Retention | Deleted after consumption | Retained for configurable time |
| Use case | Task processing | Event sourcing, analytics |
| Examples | RabbitMQ, SQS, ActiveMQ | Kafka, Kinesis, Pulsar |

### Message Queue Patterns

**Point-to-Point**: one producer → one consumer queue → one consumer
**Pub/Sub**: one producer → topic → multiple consumers (fan-out)
**Dead Letter Queue (DLQ)**: messages that fail processing go here for inspection
**Priority Queue**: higher priority messages processed first

### Apache Kafka

**Core Concepts**:
- **Topic**: named log of events
- **Partition**: unit of parallelism; ordered within partition
- **Offset**: position of a message in a partition
- **Consumer Group**: group of consumers sharing partition assignment
- **Broker**: Kafka server; cluster has multiple brokers
- **Replication factor**: how many copies of each partition

**Guarantees**:
- At-least-once delivery (default with consumer offset commits)
- Exactly-once semantics (with transactions + idempotent producers)
- Messages within a partition are ordered

**Retention**: time-based or size-based; consumers can re-read history

**When to use Kafka**:
- High-throughput event streaming (millions/sec)
- Log aggregation
- Event sourcing
- Stream processing pipelines

### Message Delivery Semantics

| Semantic | Description | Risk |
|----------|-------------|------|
| At-most-once | Send and forget | Data loss |
| At-least-once | Retry until ACK | Duplicates |
| Exactly-once | Idempotent + transactions | Complex, slowest |

### Stream Processing

**Apache Flink**: stateful, event-time processing, low latency
**Apache Spark Streaming**: micro-batching, easier to use
**Kafka Streams**: embedded library, no separate cluster
**Apache Storm**: real-time, low latency, older

---

## 11. API Design

### REST (Representational State Transfer)

**Principles**:
- Stateless
- Uniform interface (resources, HTTP verbs)
- Client-server separation
- Layered system
- Cacheable responses

**HTTP Verbs**:
| Verb | Action | Idempotent |
|------|--------|------------|
| GET | Read | Yes |
| POST | Create | No |
| PUT | Replace | Yes |
| PATCH | Partial update | No |
| DELETE | Delete | Yes |

**Status Codes**:
- 2xx: Success (200 OK, 201 Created, 204 No Content)
- 3xx: Redirect (301 Moved, 304 Not Modified)
- 4xx: Client error (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 429 Too Many Requests)
- 5xx: Server error (500 Internal, 502 Bad Gateway, 503 Service Unavailable)

**Versioning strategies**:
- URL path: `/api/v1/users`
- Query param: `/api/users?version=1`
- Header: `Accept: application/vnd.myapp.v1+json`

### GraphQL

- Client specifies exact data shape needed
- Single endpoint (`/graphql`)
- Reduces over-fetching and under-fetching
- **Queries**: read data
- **Mutations**: write data
- **Subscriptions**: real-time updates

**Trade-offs**:
- N+1 query problem (solved with DataLoader)
- Complex caching
- Higher client complexity

### gRPC

- Protocol Buffers (binary serialization) — smaller, faster than JSON
- HTTP/2 (multiplexing, streaming)
- Strongly typed contracts (`.proto` files)
- Supports: unary, server streaming, client streaming, bidirectional streaming

**When to use**: internal microservice communication, low latency, polyglot systems

### API Gateway

Central entry point for all client requests.

**Responsibilities**:
- Authentication & authorization
- Rate limiting
- Request routing
- SSL termination
- Request/response transformation
- Logging & monitoring
- Circuit breaking

**Examples**: Kong, AWS API Gateway, Nginx, Envoy, Traefik

### Pagination

| Pattern | How | Use When |
|---------|-----|----------|
| Offset | `?offset=20&limit=10` | Simple, supports random access |
| Cursor/Keyset | `?after=cursor123` | Large datasets, stable order |
| Page number | `?page=3&size=10` | UI with page numbers |
| Time-based | `?since=timestamp` | Time-ordered data |

---

## 12. Security

### Authentication vs Authorization

- **Authentication**: verifying identity (who are you?)
- **Authorization**: verifying permissions (what can you do?)

### Authentication Methods

**API Keys**: simple, stateless; hard to rotate, no user context
**OAuth 2.0**: delegated authorization; access tokens; scopes
**JWT (JSON Web Token)**:
```
header.payload.signature
```
- Stateless, self-contained
- Verify without DB lookup (use signature)
- Cannot revoke without blocklist

**Session-based**: server stores session; client sends session ID cookie

**SAML**: XML-based SSO; enterprise federation
**OIDC (OpenID Connect)**: OAuth 2.0 + identity layer; provides ID token

### HTTPS & TLS

- TLS 1.2/1.3 — always use TLS in production
- Certificate Authority (CA) signs certificate
- **mTLS (Mutual TLS)**: both client and server authenticate — used in service meshes

### Common Vulnerabilities (OWASP Top 10)

| Vulnerability | Mitigation |
|--------------|------------|
| Injection (SQL, NoSQL, OS) | Parameterized queries, input validation |
| Broken Authentication | MFA, strong passwords, secure session management |
| XSS | Output encoding, CSP headers |
| CSRF | CSRF tokens, SameSite cookies |
| IDOR | Authorization checks on every request |
| Security Misconfiguration | Principle of least privilege, secure defaults |
| Sensitive Data Exposure | Encryption at rest and in transit |
| XXE | Disable XML external entities |
| SSRF | Allowlist outbound URLs |
| Insecure Deserialization | Validate and sanitize all input |

### Encryption

**At rest**: AES-256 for stored data; database encryption; disk encryption
**In transit**: TLS/HTTPS; gRPC with TLS
**Key management**: AWS KMS, HashiCorp Vault, GCP KMS

### Zero Trust Architecture

- Never trust, always verify
- Least privilege access
- Assume breach; segment the network
- Continuous verification (not just at login)

---

## 13. Observability

### The Three Pillars

**Metrics** — numerical time-series data
- CPU, memory, request rate, error rate, latency
- Tools: Prometheus, Grafana, Datadog, CloudWatch

**Logs** — discrete events with context
- Structured logging (JSON) preferred
- Tools: ELK Stack (Elasticsearch, Logstash, Kibana), Loki, Splunk

**Traces** — end-to-end request lifecycle across services
- Span = single operation; Trace = collection of spans
- Tools: Jaeger, Zipkin, AWS X-Ray, OpenTelemetry

### Key Metrics

**The Four Golden Signals (Google SRE)**:
1. **Latency** — time to serve a request (p50, p95, p99)
2. **Traffic** — requests per second
3. **Errors** — error rate (5xx, 4xx)
4. **Saturation** — how full resources are (CPU, memory, queue depth)

**RED Method (for microservices)**:
- **R**ate — requests/sec
- **E**rrors — errors/sec
- **D**uration — latency distribution

**USE Method (for resources)**:
- **U**tilization — % time resource is busy
- **S**aturation — work queued
- **E**rrors — error count

### SLA / SLO / SLI

| Term | Meaning | Example |
|------|---------|---------|
| **SLI** | Actual measured metric | 99.94% uptime measured |
| **SLO** | Internal target | 99.9% uptime |
| **SLA** | External commitment with penalties | 99.5% uptime or refund |

**Error budget**: allowed downtime = 1 - SLO
- 99.9% SLO = 8.7 hours downtime/year
- 99.99% SLO = 52 minutes downtime/year

### Alerting Best Practices

- Alert on **symptoms**, not causes
- Use alert severity levels (P1 critical, P2 high, P3 medium)
- Avoid alert fatigue; high signal-to-noise ratio
- Runbook links in alerts
- Dead man's switch alerts for silent failures

---

## 14. Architecture Patterns

### 14.1 Monolith

All application functionality in a single deployable unit.

```
[Client] → [Monolith: UI + Business Logic + Data Access] → [Database]
```

**Advantages**:
- Simple to develop and debug
- No network overhead between components
- Easier transactions
- Straightforward deployment

**Disadvantages**:
- Difficult to scale individual components
- Long build/deploy cycles
- Technology lock-in
- Single point of failure (partially)
- Team coordination challenges at scale

**Modular Monolith**: well-structured monolith with enforced module boundaries — good intermediate step before microservices.

**When to use**: early-stage startups, small teams, well-understood domains, rapid iteration needed

---

### 14.2 Microservices

System decomposed into small, independently deployable services, each owning its domain and data.

```
[API Gateway] → [Auth Service]
              → [Order Service] → [Order DB]
              → [User Service]  → [User DB]
              → [Payment Service] → [Payment DB]
```

**Design Principles**:
- **Single Responsibility**: each service owns one bounded context
- **Database per service**: no shared databases
- **API contracts**: well-defined interfaces between services
- **Failure isolation**: bulkheads, circuit breakers
- **Independent deployment**: CI/CD per service

**Service Discovery**:
- **Client-side**: client queries service registry (Eureka, Consul)
- **Server-side**: load balancer queries registry (AWS ALB + ECS)

**Decomposition Strategies**:
- By **business capability** (Order, Payment, Inventory)
- By **domain-driven design** (bounded contexts)
- By **subdomain** (core, supporting, generic)
- **Strangler Fig Pattern**: incrementally replace monolith by routing traffic to new services

**Trade-offs**:
- Operational complexity (distributed systems, observability)
- Network latency between services
- Eventual consistency challenges
- Service coordination (sagas, choreography)

**When to use**: large teams, independent scaling needs, polyglot requirements, complex domain

---

### 14.3 Event-Driven Architecture

Services communicate by producing and consuming events. No direct calls.

```
[Producer] → [Event Broker (Kafka/SQS)] → [Consumer A]
                                         → [Consumer B]
                                         → [Consumer C]
```

**Event Types**:
- **Domain Events**: something happened in the domain (OrderPlaced, PaymentProcessed)
- **Commands**: request to do something (ProcessPayment)
- **Queries**: request for information (GetOrderStatus)

**Topologies**:
- **Mediator**: central orchestrator routes events (more control)
- **Broker**: events broadcast; consumers decide what to handle (looser coupling)

**Advantages**:
- Loose coupling; producers don't know consumers
- High scalability (consumers scale independently)
- Resilience (events persisted; consumers can recover)
- Real-time processing

**Disadvantages**:
- Eventual consistency
- Harder to debug (event chains are non-obvious)
- Event schema evolution challenges
- Ordering guarantees limited to per-partition

**Patterns**:
- **Outbox Pattern**: write event to DB in same transaction, then publish to broker → atomicity
- **Event Replay**: re-process events from beginning to rebuild state
- **Idempotent consumers**: handle duplicate events safely

---

### 14.4 Serverless

Functions execute on demand; infrastructure managed by cloud provider.

```
[Event Trigger] → [Function (Lambda/Cloud Function)] → [Output/Service]
```

**Trigger types**: HTTP (API Gateway), Queue (SQS), Stream (Kinesis), Schedule (EventBridge), Storage (S3), Database (DynamoDB Streams)

**Characteristics**:
- No server management
- Auto-scaling (including to zero)
- Pay-per-execution
- Stateless (state in external stores)
- Cold start latency on first invocation

**Cold Start Mitigation**:
- Provisioned concurrency (keep instances warm)
- Minimize dependencies / bundle size
- Use faster runtimes (Go, Rust over Java)

**Vendor Lock-in Mitigation**:
- Abstract cloud APIs behind interfaces
- Use frameworks: Serverless Framework, AWS SAM, Pulumi

**When to use**: event-driven workloads, unpredictable traffic, microservices with low traffic, scheduled jobs, glue code

**When NOT to use**: long-running processes, high-frequency low-latency requirements, complex stateful workflows

---

### 14.5 CQRS & Event Sourcing

**CQRS (Command Query Responsibility Segregation)**

Separate read and write models.

```
[Client] → [Command Handler] → [Write Model (normalized DB)]
         → [Query Handler]  → [Read Model (denormalized, optimized)]
```

- Commands change state; queries read state
- Read model updated asynchronously via events
- Read model can be a different database type (e.g., Elasticsearch for search)
- Enables independent scaling of reads and writes

**Event Sourcing**

Store state as a sequence of events, not current state.

```
Events: [AccountOpened] → [MoneyDeposited] → [MoneyWithdrawn]
Current state = replay of all events
```

- Complete audit trail
- Time travel (rebuild state at any point in time)
- Events are immutable
- **Projection**: materialized view built from events

**Combined CQRS + Event Sourcing**:
- Commands produce events
- Events stored in event store
- Projections build read models from events

**Trade-offs**:
- Complexity (eventual consistency, projections, schema evolution)
- Event store must handle high write volume
- Query side may lag behind write side

---

### 14.6 Lambda Architecture

Batch and streaming pipelines combined to handle latency vs completeness trade-off.

```
[Data Source] → [Batch Layer (Hadoop/Spark)] → [Batch Views]
              → [Speed Layer (Flink/Storm)]   → [Real-time Views]
                                                          ↓
                                               [Serving Layer (Merges both)]
```

**Batch Layer**: high latency, fully accurate, reprocesses all historical data
**Speed Layer**: low latency, approximate, handles only recent data
**Serving Layer**: queries both, merges results

**Drawback**: two separate codebases for same logic; complex to maintain

---

### 14.7 Kappa Architecture

Simplification of Lambda: everything through a single streaming pipeline.

```
[Data Source] → [Stream Processor (Kafka + Flink)] → [Serving Layer]
```

- Historical reprocessing: replay from Kafka's offset 0
- Single codebase for batch and streaming
- Requires durable, replayable event log (Kafka)

**When to prefer Kappa**: streaming is the primary use case, team wants simplicity
**When to prefer Lambda**: batch accuracy is critical and streaming is supplemental

---

### 14.8 Hexagonal / Clean Architecture

Also known as Ports and Adapters. Decouples business logic from external systems.

```
[External World]
     ↕ (Adapters: REST, gRPC, CLI, MQ)
[Ports (Interfaces)]
     ↕
[Application Core (Business Logic)]
     ↕
[Ports (Interfaces)]
     ↕ (Adapters: DB, Cache, Email, External APIs)
[Infrastructure]
```

**Layers**:
- **Domain**: entities, value objects, domain events, business rules
- **Application**: use cases / application services; orchestrates domain
- **Infrastructure**: database implementations, HTTP clients, message publishers
- **Interfaces**: controllers, CLI handlers, event consumers

**Dependency Rule**: dependencies point inward; domain knows nothing about infrastructure

**Benefits**:
- Business logic is testable without external systems
- Easy to swap adapters (e.g., switch from MySQL to Postgres)
- Clear separation of concerns

**Variants**: Clean Architecture (Uncle Bob), Onion Architecture (same concept, different layering)

---

### 14.9 Service Mesh

Infrastructure layer for service-to-service communication, implemented via sidecar proxies.

```
[Service A] ← [Sidecar Proxy] ←→ [Control Plane]
                    ↕
[Service B] ← [Sidecar Proxy]
```

**Control plane**: manages configuration, certificates, policies (Istio Pilot, Consul)
**Data plane**: sidecar proxies handle traffic (Envoy, Linkerd-proxy)

**Features**:
- mTLS between services (automatic)
- Traffic management (canary, A/B, retries, timeouts)
- Observability (distributed tracing, metrics from proxy)
- Circuit breaking & rate limiting
- Service discovery

**Popular implementations**: Istio, Linkerd, Consul Connect, AWS App Mesh

**When to use**: large microservices deployments needing consistent security and observability

**Trade-offs**: added latency per hop, operational complexity, resource overhead (sidecars)

---

### 14.10 Cell-Based Architecture

System divided into independent cells, each a fully functional replica of the whole system.

```
[Global Router]
      ↓
[Cell 1: User A-M] [Cell 2: User N-Z] [Cell 3: Region EU]
Each cell: [API] [Services] [DB] [Cache] [Queue]
```

**Properties**:
- Blast radius containment: failure in one cell doesn't affect others
- Independent scaling per cell
- Data sovereignty (e.g., GDPR — EU data in EU cells)
- Rolling deployments: deploy to one cell first

**Cell assignment strategies**: user ID range, geography, organization, random hash

**Used by**: Amazon (availability zones as cells), Slack, Shopify

---

## 15. System Design Interview Framework

### Step-by-Step Approach

**1. Clarify Requirements (5–7 min)**
- Functional requirements: what must the system do?
- Non-functional requirements: scale, latency, consistency, availability
- Constraints: budget, team size, existing infrastructure

Key questions:
```
- How many users? (DAU, MAU)
- Read-heavy or write-heavy?
- Latency requirements? (real-time vs eventual)
- Consistency requirements?
- Global or single region?
- Any existing systems to integrate with?
```

**2. Back-of-the-Envelope Estimation (3–5 min)**
- QPS (queries per second)
- Storage requirements
- Bandwidth
- Number of servers needed

**3. High-Level Design (10–15 min)**
- Draw major components: clients, LB, services, databases, caches
- Define API contracts
- Data flow for core use cases
- Don't over-engineer at this stage

**4. Deep Dive (10–15 min)**
- Drill into one or two bottleneck areas
- Schema design
- Sharding strategy
- Caching strategy
- Consistency model
- Failure scenarios

**5. Trade-offs & Bottlenecks (5 min)**
- What are the weaknesses?
- What would you do with more time?
- How would it scale 10x?

### Common Follow-up Questions

- How do you handle hot partitions?
- What happens if the cache is down?
- How do you ensure exactly-once processing?
- How do you do a zero-downtime migration?
- How would you handle a celebrity/viral post?

---

## 16. Common System Design Problems

### URL Shortener (e.g., bit.ly)

**Core requirements**: shorten URL, redirect on access, analytics

**Key decisions**:
- ID generation: base62 encoding of hash or auto-increment
- Storage: KV store (Redis + Cassandra) or relational
- Redirect: 301 (permanent, cached by browser) vs 302 (always hits server)
- Custom aliases: check uniqueness

**Architecture**:
```
[Client] → [LB] → [Shortener Service] → [ID Generator (Snowflake)]
                                      → [KV Store (Redis + Cassandra)]
         → [Analytics Consumer] ← [Event Queue]
```

---

### Rate Limiter

**Key decisions**:
- Algorithm: token bucket (allows bursts) vs sliding window
- Storage: Redis for distributed counting
- Scope: per user, per IP, per API key
- Response: 429 with Retry-After header

**Redis implementation**:
```
INCR key:user:endpoint:minute_window
EXPIRE key TTL
```

---

### Distributed Cache

**Key decisions**:
- Consistent hashing for node assignment
- Replication factor for fault tolerance
- Eviction policy (LRU)
- Cache write strategy

---

### News Feed / Timeline

**Core**: show recent posts from people you follow

**Fan-out on write**: pre-compute feed on post; write to each follower's feed
- Pro: fast reads
- Con: expensive writes (celebrities with millions of followers)

**Fan-out on read**: compute feed at read time
- Pro: simple writes
- Con: slow reads for users following many people

**Hybrid**: fan-out on write for regular users; fan-out on read for celebrities

---

### Chat System (e.g., WhatsApp)

**Key components**:
- WebSocket connections (persistent, bidirectional)
- Message store (Cassandra: user_id + timestamp as key)
- Presence service (who's online)
- Push notifications (APNs, FCM) when user is offline
- Message ordering: Lamport timestamps or per-user sequence numbers
- End-to-end encryption: Signal Protocol

---

### Search Autocomplete

**Key components**:
- Trie (prefix tree) for prefix matching
- Top-K results per node stored in trie
- Offline job to update trie from query logs
- Distributed trie: partition by first character
- Cache top queries

---

### Distributed Message Queue

**Partitioned log** (Kafka-style):
- Topics divided into partitions
- Producers append to end of partition
- Consumers read from offset
- Offsets committed to separate store

**Key design decisions**:
- Message ordering: guaranteed within partition
- Consumer group rebalancing: partition reassignment on member change
- Durability: replication factor, ISR (in-sync replicas)

---

### Ride-Sharing (e.g., Uber)

**Core features**: real-time location tracking, driver matching, pricing

**Key components**:
- Location service: drivers send GPS updates (WebSocket/HTTP polling)
- Geospatial index: QuadTree or S2 cells for nearby driver lookup
- Matching service: algorithm to match rider ↔ driver
- Pricing engine: surge based on supply/demand
- Trip service: manages trip state machine

---

### Video Streaming (e.g., YouTube/Netflix)

**Upload flow**:
```
[Client] → [Upload Service] → [Raw Storage] → [Transcoder] → [CDN]
```

**Playback flow**:
```
[Client] → [CDN edge] (cache hit) or [Origin Server]
```

**Key decisions**:
- Adaptive bitrate streaming (HLS, DASH): switch quality based on bandwidth
- Content encoding: multiple resolutions (240p → 4K)
- CDN for static asset delivery
- Metadata: separate from video data; stored in relational DB

---

## 17. Back-of-the-Envelope Estimation

### Key Numbers to Memorize

**Latency reference (approximate)**:

| Operation | Latency |
|-----------|---------|
| L1 cache | 1 ns |
| L2 cache | 10 ns |
| RAM access | 100 ns |
| SSD read | 100 µs |
| HDD seek | 10 ms |
| Cross-AZ network | 1–3 ms |
| Cross-region network | 30–200 ms |

**Storage**:
- 1 ASCII char = 1 byte
- 1 UUID = 16 bytes
- 1 timestamp = 8 bytes
- 1 average tweet ≈ 300 bytes
- 1 HD photo ≈ 3 MB

**Scale conversions**:
```
1K   = 10^3
1M   = 10^6
1B   = 10^9
1T   = 10^12

1 KB = 10^3 bytes
1 MB = 10^6 bytes
1 GB = 10^9 bytes
1 TB = 10^12 bytes
```

### Estimation Formula

```
QPS = Daily Active Users × Actions per user per day / 86,400 seconds
Peak QPS ≈ QPS × 2–3

Storage per year = QPS × Data per request × 86,400 × 365
Bandwidth = QPS × Average response size
```

### Example: Twitter-scale Estimation

```
DAU = 300M
Tweets per day = 300M × 5 = 1.5B tweets/day
QPS = 1.5B / 86,400 ≈ 17,000 tweets/sec (writes)
Read QPS ≈ 10× write = 170,000 reads/sec

Tweet size = 300 bytes
Storage = 1.5B × 300 bytes = 450 GB/day = ~160 TB/year

Media storage: assume 20% of tweets have media (avg 1 MB)
= 0.2 × 1.5B × 1 MB = 300 TB/day (CDN handles this)
```

---

## 18. Glossary

| Term | Definition |
|------|------------|
| **Availability** | Percentage of time a system is operational |
| **Bandwidth** | Maximum data transfer rate |
| **Blob storage** | Object storage for unstructured data (S3, GCS) |
| **Bounded context** | DDD term for a domain with clear boundaries and its own model |
| **Circuit breaker** | Stops calls to a failing service to allow recovery |
| **Cold start** | Latency on first serverless function invocation |
| **Consistent hashing** | Distributes keys across nodes with minimal remapping on change |
| **CQRS** | Pattern separating read and write models |
| **Data locality** | Keeping data close to compute to reduce latency |
| **Denormalization** | Adding redundancy to improve read performance |
| **Edge computing** | Processing data close to the source/user |
| **Eventual consistency** | Data will converge to same value, but not immediately |
| **Fanout** | Distributing one message/event to multiple targets |
| **Federation** | Splitting database by function |
| **Heartbeat** | Periodic signal to indicate a service is alive |
| **Hot spot** | Node/partition receiving disproportionate traffic |
| **Idempotency** | Operation that produces same result regardless of how many times applied |
| **Immutable infrastructure** | Replace servers rather than modify them |
| **Latency** | Time for a single request/response |
| **Leader election** | Process for choosing one node as coordinator |
| **Linearizability** | Strongest consistency model; ops appear instantaneous |
| **Microservices** | Small, independently deployable services per domain |
| **Partition** | Division of data or failure domain |
| **Polling** | Repeatedly checking for new data |
| **Quorum** | Minimum nodes that must agree for an operation to succeed |
| **Race condition** | Outcome depends on unpredictable ordering of events |
| **Replication** | Copying data to multiple nodes for redundancy |
| **Saga** | Pattern for distributed long-running transactions with compensating actions |
| **Sharding** | Horizontal partitioning across multiple nodes |
| **SLA** | Service Level Agreement — external commitment |
| **SLI** | Service Level Indicator — measured metric |
| **SLO** | Service Level Objective — internal target |
| **Stateless** | Service holds no session state between requests |
| **Throughput** | Number of operations per second |
| **TTL** | Time to live — expiry for cache/DNS entries |
| **Two-phase commit (2PC)** | Distributed transaction protocol requiring coordinator |
| **Vector clock** | Tracks causality between events across distributed nodes |
| **WAL (Write-Ahead Log)** | Durability mechanism; log changes before applying them |

---

*Reference compiled for use as an IDE knowledge base. Covers core distributed systems concepts, architecture patterns, and design trade-offs.*
