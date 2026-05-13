# Distributed Systems — Complete Reference Guide

> A deep-reference document for distributed systems concepts, patterns, trade-offs, and architecture selection. Optimized for IDE use — searchable, structured, and self-contained.

---

## Table of Contents

1. [What Is a Distributed System?](#1-what-is-a-distributed-system)
2. [Fundamental Properties](#2-fundamental-properties)
3. [Failure Models](#3-failure-models)
4. [Time & Ordering](#4-time--ordering)
5. [Consistency Models](#5-consistency-models)
6. [Consensus Algorithms](#6-consensus-algorithms)
7. [Replication](#7-replication)
8. [Partitioning & Sharding](#8-partitioning--sharding)
9. [Distributed Storage Systems](#9-distributed-storage-systems)
10. [Distributed Transactions](#10-distributed-transactions)
11. [Coordination & Leader Election](#11-coordination--leader-election)
12. [Distributed Messaging & Streaming](#12-distributed-messaging--streaming)
13. [Distributed Caching](#13-distributed-caching)
14. [Networking in Distributed Systems](#14-networking-in-distributed-systems)
15. [Fault Tolerance Patterns](#15-fault-tolerance-patterns)
16. [Scalability Patterns](#16-scalability-patterns)
17. [Observability in Distributed Systems](#17-observability-in-distributed-systems)
18. [Security in Distributed Systems](#18-security-in-distributed-systems)
19. [Distributed System Architectures](#19-distributed-system-architectures)
    - [Master-Slave](#191-master-slave-architecture)
    - [Peer-to-Peer (P2P)](#192-peer-to-peer-p2p)
    - [Leader-Follower Cluster](#193-leader-follower-cluster)
    - [Sharded Cluster](#194-sharded-cluster)
    - [Multi-Region Active-Active](#195-multi-region-active-active)
    - [Multi-Region Active-Passive](#196-multi-region-active-passive)
    - [Gossip-Based (Epidemic)](#197-gossip-based-epidemic-architecture)
    - [Hierarchical / Tree-Based](#198-hierarchical--tree-based-architecture)
    - [Lambda Architecture](#199-lambda-architecture)
    - [Kappa Architecture](#1910-kappa-architecture)
    - [CQRS + Event Sourcing](#1911-cqrs--event-sourcing)
    - [Cell-Based Architecture](#1912-cell-based-architecture)
20. [Architecture Selection Guide](#20-architecture-selection-guide)
21. [Back-of-the-Envelope Estimation](#21-back-of-the-envelope-estimation)
22. [Glossary](#22-glossary)

---

## 1. What Is a Distributed System?

A distributed system is a collection of independent computers (nodes) that appear to users as a single coherent system. Nodes communicate via a network and coordinate to accomplish a common goal.

### Why Build Distributed Systems?

| Motivation | Description |
|------------|-------------|
| **Scalability** | One machine has physical limits; distribute load across many |
| **Fault tolerance** | No single point of failure; system survives node crashes |
| **Low latency** | Place computation/data close to users globally |
| **High availability** | Continuous operation despite partial failures |
| **Cost** | Commodity hardware is cheaper than mainframes |

### Core Challenges (The 8 Fallacies of Distributed Computing)

1. The network is reliable
2. Latency is zero
3. Bandwidth is infinite
4. The network is secure
5. Topology doesn't change
6. There is one administrator
7. Transport cost is zero
8. The network is homogeneous

**These are all false.** Every design decision in a distributed system fights against one or more of these fallacies.

---

## 2. Fundamental Properties

### CAP Theorem

In the presence of a **network partition**, a distributed system must choose between:

- **Consistency (C)**: every read receives the most recent write (or an error)
- **Availability (A)**: every request receives a non-error response (may not be latest data)
- **Partition Tolerance (P)**: system continues operating despite network partitions

> **Network partitions are unavoidable.** The real trade-off is **CP vs AP**.

```
CAP Triangle:

        Consistency
           /\
          /  \
         / CA \ ← (only possible without partitions; not realistic)
        /      \
       /--------\
  CP  /          \ AP
     /            \
    Availability--Partition Tolerance
```

| System | Type | Reason |
|--------|------|--------|
| HBase | CP | Stops accepting writes during partition |
| Zookeeper | CP | Leader-based; fails if no quorum |
| MongoDB (w/ strong) | CP | Read/write concern can sacrifice availability |
| Cassandra | AP | Always accepts writes; reconciles later |
| CouchDB | AP | Eventual consistency; multi-master |
| DynamoDB | AP (default) | Eventually consistent reads by default |
| Eureka | AP | Favors availability for service discovery |

### PACELC Theorem

Extends CAP — **even without partitions**, there are trade-offs:

```
If Partition:  trade Availability (A) vs Consistency (C)
Else (E):      trade Latency (L) vs Consistency (C)
```

| System | Partition Behavior | Normal Behavior |
|--------|--------------------|----------------|
| DynamoDB | PA | EL (low latency, eventual consistency) |
| Cassandra | PA | EL |
| MongoDB | PC | EC |
| MySQL (sync replica) | PC | EC |
| Zookeeper | PC | EC |

### ACID vs BASE

| Property | ACID (Relational) | BASE (Distributed NoSQL) |
|----------|-------------------|--------------------------|
| Consistency | Strong, immediate | Eventual |
| Availability | Lower (locks) | Higher |
| Transactions | Full ACID | Limited / compensating |
| Model | Pessimistic | Optimistic |
| Examples | PostgreSQL, MySQL | Cassandra, DynamoDB |

---

## 3. Failure Models

Understanding how systems fail is prerequisite to designing around failure.

### Types of Failures

**Crash Failure (Fail-Stop)**
- Node stops completely and does not restart
- Simplest to handle; others detect via heartbeat timeout
- Most common assumed failure mode

**Crash-Recovery Failure**
- Node crashes and may restart later
- Must handle state recovery (WAL, checkpointing)

**Omission Failure**
- Node is running but fails to respond to some messages
- Send-omission: fails to send
- Receive-omission: fails to receive
- Harder to detect than crash failures

**Performance Failure (Timing Failure)**
- Node responds, but too slowly (exceeds timeout)
- Indistinguishable from crash failure in practice
- Drives the need for timeouts

**Byzantine Failure (Arbitrary Failure)**
- Node behaves arbitrarily: sends wrong data, acts maliciously, or lies
- Hardest to handle
- Requires BFT algorithms (needs 3f+1 nodes to tolerate f Byzantine nodes)
- Relevant in: blockchain, safety-critical systems, untrusted environments

### Failure Detection

**Heartbeat mechanism**:
- Nodes send periodic pings
- If no heartbeat within threshold → suspected failed
- Risk: false positives during slow network (GC pauses, high load)

**Phi Accrual Failure Detector** (used in Akka, Cassandra):
- Returns a suspicion level (phi value), not a binary alive/dead
- phi increases the longer since last heartbeat
- Application sets its own threshold based on tolerance

**SWIM Protocol (Scalable Weakly-consistent Infection-style Membership)**:
- Used in: Consul, HashiCorp Serf
- Combines heartbeat + random probing + gossip
- Detects failures in O(log N) time without central coordinator

### The FLP Impossibility

Fischer-Lynch-Paterson (1985): In an **asynchronous** distributed system with even **one** crash failure, it is impossible to guarantee consensus in finite time.

**Practical implication**: All real consensus algorithms (Paxos, Raft) use timeouts and randomization to work around this — they're not guaranteed to terminate, but terminate with high probability in practice.

---

## 4. Time & Ordering

In distributed systems, there is no global clock. Time and event ordering must be constructed.

### Why Clocks Are Unreliable

- Clock drift: hardware clocks run at slightly different rates
- NTP synchronization is imperfect (±milliseconds to seconds of error)
- Leap seconds cause discontinuities
- Google TrueTime (used in Spanner): uses atomic clocks + GPS, provides uncertainty bounds

### Logical Clocks

**Lamport Timestamps (1978)**

Each node maintains a counter. Rules:
1. Increment counter before each event
2. When sending a message, attach current counter value
3. On receive: `clock = max(local, received) + 1`

- Establishes causal ordering: if A → B then timestamp(A) < timestamp(B)
- Converse is NOT true: timestamp(A) < timestamp(B) does not mean A caused B

```
Node 1:  1 --- 2 ----------- 5
                 \           /
Node 2:  1 ------ 3 --- 4 --
```

**Vector Clocks**

Each node maintains a vector of counters (one per node).

- Captures causality fully: can determine if A → B, B → A, or A ∥ B (concurrent)
- Used in: DynamoDB, Riak, Git (conceptually)
- Space: O(N) per message where N = number of nodes

```
Node A: [1,0,0] → [2,0,0]  → send to B → [3,1,1]
Node B: [0,0,0] → [2,1,0]  → send to C → [2,2,1]
Node C: [0,0,0] → [2,2,1]  (receives B's message)
```

**Version Vectors** (variant used in storage systems):
- Tracks which version of data each node has seen
- Detects conflicts when merging concurrent writes

**Hybrid Logical Clocks (HLC)**
- Combines physical time (NTP) + logical increments
- Provides causality tracking with a clock that closely tracks wall time
- Used in: CockroachDB, YugabyteDB

### Event Ordering Strategies

| Strategy | Mechanism | Use Case |
|----------|-----------|----------|
| Lamport timestamps | Logical counter | Total order, cannot detect concurrency |
| Vector clocks | Per-node counter vector | Causality tracking, conflict detection |
| TrueTime | Atomic clock + GPS + uncertainty bounds | Globally consistent transactions (Spanner) |
| Sequence numbers | Central ID generator | Simple ordering within a system |
| Snowflake IDs | Timestamp + machine ID + sequence | Distributed unique ID + approximate ordering |

### Snowflake ID (Twitter)

64-bit ID composed of:
```
[41 bits timestamp ms] [10 bits machine ID] [12 bits sequence]
```
- Sortable by time
- ~4096 IDs per millisecond per machine
- No coordination required between generators

---

## 5. Consistency Models

From weakest (most available) to strongest (most consistent):

### Eventual Consistency

- All replicas will converge to the same value **eventually** if no new updates occur
- No guarantee on when convergence happens
- Reads may return stale data
- **Used in**: Cassandra, DynamoDB (default), DNS

### Monotonic Read Consistency

- If a process reads a value, subsequent reads will never return an older value
- Prevents "going back in time"
- Implementation: always read from the same replica, or track read version

### Read-Your-Writes (Read-Your-Own-Writes)

- A process will always see its own writes in subsequent reads
- Other processes may not see the write yet
- Implementation: route reads from a user to the same replica they wrote to

### Monotonic Write Consistency

- Writes from a single process are applied in the order they were issued
- Prevents write reordering at the replica level

### Causal Consistency

- Operations that are causally related are seen in causal order by all nodes
- Concurrent operations may be seen in different orders by different nodes
- Stronger than eventual, weaker than sequential
- **Used in**: MongoDB causal sessions, some NewSQL databases

### Sequential Consistency

- Operations appear to execute in some sequential order
- All nodes see the same order
- Each node's operations appear in program order
- Weaker than linearizability (no real-time constraint)

### Linearizability (Strict Consistency)

- Strongest consistency model
- Every operation appears to take effect instantaneously at some point between invocation and completion
- All nodes see the same real-time ordering
- **Used in**: etcd, Zookeeper, Google Spanner (external consistency)
- Cost: higher latency (quorum reads required)

### Serializability (Database Context)

- Transactions appear to execute serially (one at a time)
- Does not require real-time ordering
- Weaker than linearizability in the time dimension

### Strict Serializability

- Serializability + real-time ordering (linearizability for transactions)
- Strongest model for databases
- **Used in**: Google Spanner, CockroachDB (with serializable isolation)

```
Consistency Spectrum (weakest → strongest):

Eventual → Monotonic Reads → Read-Your-Writes → Causal →
Sequential → Linearizable → Strict Serializable
```

---

## 6. Consensus Algorithms

Consensus = getting a group of distributed nodes to agree on a single value.

### Requirements for Consensus

1. **Agreement**: all non-faulty nodes decide the same value
2. **Validity**: decided value was proposed by some node
3. **Termination**: all non-faulty nodes eventually decide (liveness)
4. **Fault tolerance**: works despite up to f failures

### Paxos

The original consensus algorithm (Lamport, 1989). Foundation for many production systems.

**Roles**:
- **Proposers**: propose values
- **Acceptors**: vote on proposals (form a quorum)
- **Learners**: learn the decided value

**Two phases**:

```
Phase 1 — Prepare:
  Proposer → Acceptors: "Prepare(n)"     [promise to not accept proposals < n]
  Acceptors → Proposer: "Promise(n, v)"  [highest previously accepted value]

Phase 2 — Accept:
  Proposer → Acceptors: "Accept(n, v)"   [propose value v with number n]
  Acceptors → Proposer: "Accepted(n, v)" [if n is still highest seen]
```

**Multi-Paxos**: elects a stable leader to skip Phase 1 for subsequent rounds — used in production.

**Variants**:
- **Fast Paxos**: reduces message delays in fast path
- **Cheap Paxos**: f+1 permanent + f cheap (auxiliary) nodes
- **Byzantine Paxos**: tolerates Byzantine failures (needs 3f+1 nodes)

**Used in**: Google Chubby, Apache Zookeeper (ZAB is Paxos-inspired), Apache Cassandra (lightweight transactions), Megastore

**Drawbacks**: complex to understand and implement correctly; liveness not guaranteed under contention.

---

### Raft

Designed for understandability (Ongaro & Ousterhout, 2014). Equivalent to Multi-Paxos but easier to reason about.

**Roles**:
- **Leader**: handles all client requests; replicates log entries
- **Follower**: passive; responds to leader/candidate
- **Candidate**: running for election

**Three sub-problems**:

**1. Leader Election**:
```
Follower → (election timeout, no heartbeat) → Candidate
Candidate → RequestVote(term, lastLogIndex, lastLogTerm) → all nodes
Majority votes → Leader
Leader → AppendEntries(heartbeats) → Followers  [prevents new elections]
```

- Term number = logical clock; each election is a new term
- Each node votes for at most one candidate per term
- Candidate must have log at least as up-to-date as voter's log

**2. Log Replication**:
```
Client → Leader
Leader → AppendEntries(entry) → Followers
Majority ACK → Leader marks entry committed
Leader → notifies Followers → they commit
```

- Log entry: `(term, index, command)`
- Leader never overwrites its log
- Followers reject entries that don't match their log

**3. Safety**:
- Committed entries are never lost
- New leader must have all committed entries (guaranteed by election restriction)

**Cluster membership changes**:
- Joint consensus: transition through a state where both old and new config require majority
- Single-server changes: safer; add/remove one node at a time

**Used in**: etcd (Kubernetes backend), CockroachDB, TiKV (TiDB), Consul, InfluxDB Enterprise

**Raft vs Paxos**:
| | Raft | Paxos |
|-|------|-------|
| Understandability | High | Low |
| Single leader | Yes | Not required |
| Log ordering | Central to algorithm | Less explicit |
| Membership changes | Joint consensus / single-server | Complex |

---

### Byzantine Fault Tolerant (BFT) Algorithms

Required when nodes may behave maliciously or arbitrarily.

**PBFT (Practical Byzantine Fault Tolerance)** — Castro & Liskov, 1999:
- Tolerates f Byzantine failures with 3f+1 total nodes
- Three phases: Pre-prepare, Prepare, Commit
- O(N²) message complexity — doesn't scale past ~20 nodes

**Tendermint / HotStuff**:
- Modern BFT protocols used in blockchains
- O(N) message complexity (using threshold signatures)
- HotStuff used in: LibraBFT (Diem), Aptos, many DPoS blockchains

**When to use BFT**: blockchains, multi-organizational systems, safety-critical aerospace, financial networks where participants don't fully trust each other.

---

### Quorum Systems

**Quorum**: minimum number of nodes that must agree for an operation to succeed.

```
N = total replicas
W = write quorum
R = read quorum

Strong consistency: W + R > N   (read/write quorums overlap)
Availability:      W + R ≤ N   (no overlap required)
```

Common configurations:

| N | W | R | Property |
|---|---|---|----------|
| 3 | 2 | 2 | Strong consistency, tolerates 1 failure |
| 5 | 3 | 3 | Strong consistency, tolerates 2 failures |
| 3 | 1 | 1 | Fastest, eventual consistency |
| 3 | 3 | 1 | High durability writes, fast reads |
| 3 | 1 | 3 | Always read latest, fast writes |

**Sloppy quorum** (Dynamo-style):
- Allow writes to any N nodes, even if some preferred nodes are down
- "Hinted handoff": when preferred node recovers, the sloppy node sends it the data
- Increases availability at the cost of stronger consistency guarantees

---

## 7. Replication

Copying data across multiple nodes for fault tolerance and performance.

### Replication Strategies

**Single-Leader (Primary-Replica / Master-Slave)**
```
[Client writes] → [Leader] → [Follower 1]
                           → [Follower 2]
[Client reads]  → [Follower 1 or 2]
```

- All writes go to leader; replicated to followers
- Reads can be served from followers (possible stale reads)
- Failover: promote a follower to new leader
- **Used in**: MySQL, PostgreSQL (streaming replication), MongoDB, Redis Sentinel

**Multi-Leader (Multi-Master)**
```
[Region A writes] → [Leader A] ↔ [Leader B] ← [Region B writes]
```

- Multiple nodes accept writes
- Conflict resolution required (last-write-wins, CRDT, application-defined)
- Better write availability and latency (write to nearest datacenter)
- **Used in**: CouchDB, MySQL multi-source, Google Docs (CRDTs)

**Leaderless (Dynamo-style)**
```
[Client] → [Coordinator] → write to any W nodes
                         ← read from any R nodes
```

- No single leader; any node can accept reads/writes
- Uses quorum (W + R > N for consistency)
- Conflict resolution: version vectors, last-write-wins
- **Used in**: Amazon DynamoDB, Cassandra, Riak, Voldemort

### Synchronous vs Asynchronous Replication

| | Synchronous | Asynchronous | Semi-Synchronous |
|-|-------------|--------------|-----------------|
| Write confirms after | All replicas ACK | Leader only | At least 1 replica ACK |
| Data loss on failure | None | Possible | Minimal |
| Write latency | Higher | Lower | Medium |
| Use case | Financial, critical data | High throughput | Balance |

### Replication Logs

How changes are propagated to replicas:

**Statement-based**: replicate SQL statements → non-deterministic functions (NOW(), RAND()) cause divergence

**Write-Ahead Log (WAL) shipping**: replicate raw binary log → implementation-coupled, hard to use across versions

**Logical (Row-based) replication**: replicate row-level changes → flexible, used by Debezium for CDC

**Trigger-based**: application triggers write to separate replication table → flexible but slow

### Replication Lag & Read-After-Write Consistency

Replication lag: time between leader write and follower applying it (usually ms, but can be seconds under load).

Problems caused by replication lag:
- **Read-your-writes violation**: user writes, then reads from lagging replica, sees old data
- **Monotonic reads violation**: user reads new data, then old data (different replicas)
- **Causality violation**: user sees effect before cause

Solutions:
- Read from leader for items you just wrote
- Track replication position; only read from replicas caught up past your write position
- Sticky sessions (always read from same replica)

---

## 8. Partitioning & Sharding

Splitting data across multiple nodes to scale beyond what a single machine can store.

### Partitioning Strategies

**Hash Partitioning**
```
shard = hash(key) % N
```
- Even data distribution
- Cannot do efficient range queries (keys are randomly distributed)
- Adding/removing nodes requires remapping ~all keys

**Range Partitioning**
```
shard = lookup_range_table(key)
e.g., A–M → Shard 1, N–Z → Shard 2
```
- Efficient range scans
- Risk of hotspots (e.g., alphabetical surname distribution isn't uniform)
- Used in: HBase, Bigtable, DynamoDB (sort key ranges)

**Directory Partitioning**
```
shard = lookup_table[key]   // explicit mapping
```
- Maximum flexibility
- Lookup table is a bottleneck and single point of failure
- Good for: infrequent re-partitioning, non-uniform key spaces

**Geo Partitioning**
```
shard = region(user_location)
```
- Data sovereignty (GDPR: EU data in EU)
- Low latency (data close to users)
- Potential data skew if regions have unequal user counts

**Composite Partitioning**
- Combine strategies, e.g., hash by tenant ID, then range by timestamp within tenant

### Consistent Hashing

Maps nodes and keys to a virtual ring (0 to 2^32 - 1).

```
Ring:
  0 ─── [Node A: 0-90] ─── [Node B: 91-200] ─── [Node C: 201-300] ─── 360
Keys mapped clockwise to nearest node.
```

**Adding a node**: only the keys between the new node and its predecessor are remapped (~K/N keys, not all).

**Removing a node**: only that node's keys are remapped to the next node.

**Virtual nodes (vnodes)**:
- Each physical node owns multiple points on the ring
- Dramatically improves balance (especially when nodes have different capacities)
- Standard in Cassandra (256 vnodes per node by default)

```
Without vnodes: 3 nodes → uneven distribution
With vnodes:   3 nodes × 256 positions → much more even distribution
```

### Hotspots & Mitigation

A **hotspot** is when one shard receives disproportionate traffic (e.g., celebrity user, trending item).

**Causes**:
- Key skew: popular keys overwhelm one shard
- Timestamp keys: all writes go to the most recent shard
- Sequential IDs: all writes go to the highest shard

**Mitigations**:
- **Key salting**: append random suffix to hot key → spread across multiple shards → reads must query all N salted versions
- **Split hot shards**: detect and dynamically subdivide overloaded shards
- **Caching**: absorb read hotspots at cache layer
- **Read replicas for hot keys**: replicate specific hot partitions

### Cross-Shard Queries

When a query spans multiple shards (**scatter-gather**):
```
[Coordinator] → broadcast query to all shards → collect results → merge/sort → return
```

- Latency = max latency of any shard (slowest wins)
- High fan-out increases load on all shards
- Avoid by designing partition key to co-locate related data

**Mitigation strategies**:
- Denormalize data (store redundant copies in multiple shards)
- Pre-computed aggregations (materialized views)
- Route queries to a search index (Elasticsearch) that spans all shards

### Resharding

Redistributing data as the cluster grows (or shrinks).

**Challenges**:
- Must move data without downtime
- Must handle writes to keys being moved
- Must update routing information atomically

**Approaches**:
- **Double-write**: write to old and new shard during migration
- **Shadow write + cutover**: write to both, validate, then cutover reads
- **Consistent hashing** minimizes keys to move

---

## 9. Distributed Storage Systems

### Distributed File Systems

**HDFS (Hadoop Distributed File System)**
- Large files (blocks of 128 MB default), immutable once written
- **NameNode**: metadata (file → block mapping)
- **DataNode**: stores blocks, replicates to 3 nodes by default
- Optimized for sequential reads (MapReduce), not random access

**Google File System (GFS)** — inspiration for HDFS:
- Single master (choke point), chunk servers
- Append-heavy workloads; relaxed consistency model

**Ceph**:
- Open-source, object/block/file storage in one
- No central metadata server (CRUSH algorithm for placement)
- Used as Kubernetes storage backend (Rook-Ceph)

### Distributed Key-Value Stores

**Amazon Dynamo** (2007) — landmark paper:
- Always-writable KV store (AP system)
- Consistent hashing + virtual nodes
- Sloppy quorum + hinted handoff
- Vector clocks for conflict detection
- Last-write-wins or application-level conflict resolution
- Gossip for membership and failure detection

**Apache Cassandra**:
- Wide-column store; Dynamo-inspired replication + Google Bigtable data model
- CQL (Cassandra Query Language) — SQL-like
- Tunable consistency: ANY, ONE, QUORUM, LOCAL_QUORUM, ALL
- No single point of failure (leaderless)
- Partition key → hash → node; clustering key → sort order within partition
- Compaction: merges SSTables, removes tombstones

```
Data model:
  Keyspace → Table → Partition (partition key) → Rows (clustering key)
```

**Best for**: write-heavy, time-series, high availability requirements, globally distributed

**Apache HBase**:
- Column-family store built on HDFS; Bigtable implementation
- Strong consistency (single leader per region)
- **Master** coordinates Region Servers
- **Region Server** serves row ranges
- Best for: Hadoop ecosystem, random read/write on huge datasets

### NewSQL Databases

Strong consistency + SQL + horizontal scale.

**Google Spanner**:
- Globally distributed relational database
- External consistency (strict serializability) using TrueTime
- Paxos replication across datacenters
- Two-phase commit for distributed transactions
- Schema: interleaved tables for co-location

**CockroachDB**:
- Open-source Spanner-inspired
- PostgreSQL wire-compatible
- Raft consensus per range
- Distributed transactions with MVCC

**TiDB**:
- MySQL-compatible NewSQL
- TiKV (Raft KV) + TiDB (SQL layer) + PD (Placement Driver)

**YugabyteDB**:
- PostgreSQL + Cassandra APIs
- DocDB storage layer with Raft

**When to choose NewSQL over NoSQL**: need SQL + ACID + horizontal scale (financial apps, inventory, ticketing)

### Object Storage

**Amazon S3 / GCS / Azure Blob**:
- Flat namespace: bucket → object key → data
- Eventual consistency (S3 now offers strong consistency for PUTs)
- Virtually unlimited scale; 99.999999999% (11 nines) durability
- Versioning, lifecycle policies, replication
- Not a file system; no append; no partial updates

**Use for**: data lakes, static assets, backups, ML training data, event archives

---

## 10. Distributed Transactions

ACID transactions spanning multiple nodes/services.

### Two-Phase Commit (2PC)

```
Phase 1 — Prepare:
  Coordinator → "Prepare?" → all participants
  Participants → "Vote: Yes/No"

Phase 2 — Commit/Abort:
  If all Yes → Coordinator → "Commit" → all participants
  If any No  → Coordinator → "Abort"  → all participants
```

**Problems**:
- **Blocking**: if coordinator crashes after "Prepare" but before "Commit", participants are blocked indefinitely
- **Single point of failure**: coordinator crash = stuck transaction
- **Not partition-tolerant**: if participant can't hear coordinator, it blocks

**Used in**: traditional RDBMS distributed transactions, XA protocol, some NewSQL systems (as internal mechanism)

### Three-Phase Commit (3PC)

Adds a "Pre-commit" phase to reduce blocking. Non-blocking in synchronous networks, but still fails in asynchronous networks (subject to FLP). Rarely used in practice.

### Saga Pattern

Long-running distributed transactions decomposed into a sequence of local transactions, each with a **compensating transaction**.

```
T1 → T2 → T3 → T4
         ↓ (failure at T3)
C2 ← C1   (compensate T2, then T1)
```

**Choreography Saga** (event-driven):
```
Service A completes T1 → publishes event → Service B starts T2 → ...
On failure → Service B publishes failure event → Service A runs C1
```
- Decentralized; no coordinator
- Hard to visualize overall flow
- Best for: loosely coupled services, simple flows

**Orchestration Saga**:
```
Saga Orchestrator → "Do T1" → Service A
                 ← "Done"
                 → "Do T2" → Service B
                 ← "Failed" → "Do C1" → Service A
```
- Centralized control; easy to track state
- Orchestrator is a potential bottleneck/SPOF (mitigated with Raft/Paxos-backed orchestrator)
- Best for: complex multi-step flows, need for centralized monitoring

**Saga trade-offs**:
- No isolation between sagas (concurrent sagas can see each other's intermediate state)
- Compensating transactions must be idempotent
- Cannot fully undo some side effects (e.g., sent email)

**Countermeasures for isolation**:
- **Semantic lock**: mark records as "pending" during saga
- **Commutative updates**: order-independent operations
- **Pessimistic view**: assume the worst and re-read before compensating

### Distributed Locking

Coordinating access to shared resources across nodes.

**Redis-based locking (Redlock)**:
```
Acquire lock: SET key uuid PX 30000 NX  (30s TTL)
Release lock: if key == uuid then DEL key  (Lua script for atomicity)
```
- Simple and fast
- Redlock uses majority of N Redis nodes (typically 5) for fault tolerance
- Controversial: Martin Kleppmann argues it's not safe under clock skew

**Zookeeper-based locking**:
- Create ephemeral sequential node
- Lowest-numbered node holds the lock
- Others watch the next-lower node (avoids herd effect)
- Safe, but higher latency than Redis

**etcd-based locking**:
- Lease mechanism with TTL
- Reliable (Raft-backed); used in Kubernetes leader election

**Best practice**: always set a TTL on locks; always verify lock is still held before critical operations.

---

## 11. Coordination & Leader Election

### Why You Need Coordination

- Assigning work without duplication
- Electing a leader in a cluster
- Managing distributed locks
- Maintaining cluster membership

### Apache ZooKeeper

Centralized coordination service; CP system.

**Data model**: hierarchical namespace of znodes (like a filesystem)

**Znode types**:
- **Persistent**: survives client disconnect
- **Ephemeral**: deleted when creating client disconnects (used for leader election, presence)
- **Sequential**: auto-incremented suffix

**Watches**: one-time notification when a znode changes

**Leader Election with ZooKeeper**:
```
All candidates → create /election/leader- (ephemeral sequential)
Each watches the node with next-lower number
Lowest number = current leader
On leader disconnect → ephemeral node deleted → next candidate becomes leader
```

**Used in**: Kafka (pre-KRaft), HBase, Hadoop YARN, Solr

**Drawbacks**: complex to operate, performance bottleneck at scale, being replaced in modern systems

### etcd

Distributed KV store; CP system; Raft consensus.

**Used in**: Kubernetes (stores all cluster state), CoreDNS, Vitess

**Key features**:
- Watch API for real-time change notifications
- Leases with TTL (automatic cleanup of ephemeral data)
- Transactions (compare-and-swap)
- gRPC API

**Leader election with etcd**:
```
Candidate → PUT /election/leader value=candidate_id PX=10000 NX
Only first succeeds; others watch the key
On TTL expiry → re-elect
```

### Consul

Service mesh + coordination; uses Gossip + Raft.

**Features**:
- Service discovery (DNS + HTTP)
- Health checking
- KV store
- Distributed locking
- Multi-datacenter support (gossip between DCs)

**Modes**:
- Server: participate in Raft consensus (3 or 5 recommended)
- Client: forward requests to servers; participate in gossip

---

## 12. Distributed Messaging & Streaming

### Message Queues vs Event Streams

| | Message Queue | Event Stream |
|-|--------------|--------------|
| Model | Job queue | Ordered log |
| Consumption | Each message consumed once | Consumers read at own offset |
| Retention | Deleted after ACK | Retained (configurable: days, forever) |
| Ordering | Best-effort / FIFO per queue | Ordered per partition |
| Replay | Not possible | Yes — seek to any offset |
| Backpressure | Queue depth | Consumer lag |
| Examples | RabbitMQ, SQS, ActiveMQ | Kafka, Kinesis, Pulsar |

### Apache Kafka — Deep Dive

**Core Concepts**:

```
Topic: user-events
├── Partition 0: [msg0][msg1][msg2]...
├── Partition 1: [msg0][msg1][msg2]...
└── Partition 2: [msg0][msg1][msg2]...
```

- **Topic**: logical channel; split into partitions
- **Partition**: ordered, immutable log; unit of parallelism and ordering
- **Offset**: unique position within a partition (consumer tracks this)
- **Consumer Group**: set of consumers sharing partition assignment; each partition assigned to exactly one consumer in the group
- **Broker**: Kafka server (cluster: 3+ brokers recommended)
- **Replication**: each partition has a leader and N-1 followers; leader handles all reads/writes
- **ISR (In-Sync Replicas)**: followers caught up to leader; only ISR replicas can be elected leader

**Producer guarantees**:
- `acks=0`: fire and forget (fastest, may lose data)
- `acks=1`: leader ACK (may lose if leader fails before replication)
- `acks=all`: all ISR ACK (safest, slowest)
- `enable.idempotence=true`: exactly-once writes from producer to broker

**Consumer semantics**:
- At-least-once: commit offset after processing (retry may cause duplicates)
- At-most-once: commit offset before processing (crash may cause loss)
- Exactly-once: use Kafka transactions + idempotent consumers

**Kafka Transactions** (for exactly-once):
```
producer.beginTransaction()
producer.send(...)
consumer.commitSync(offsets, transactionalId)
producer.commitTransaction()
```

**Retention**: time-based (`log.retention.hours`) or size-based (`log.retention.bytes`)

**Log compaction**: retain only latest value per key; used for changelog topics

**Consumer Group Rebalancing**:
- Triggered when: member joins/leaves, partition count changes
- **Eager rebalance**: all members revoke partitions → stop-the-world
- **Cooperative (incremental) rebalance**: only move necessary partitions — much better

**Kafka Streams**:
- Embedded Java library for stream processing on Kafka
- Stateful operations with RocksDB-backed state stores
- Exactly-once processing (EOS)
- No separate cluster needed

**Kafka Connect**:
- Framework for integrating Kafka with external systems
- Source connectors (DB → Kafka): Debezium (CDC)
- Sink connectors (Kafka → DB/S3/Elasticsearch)

**KRaft mode** (Kafka without ZooKeeper):
- Kafka manages its own metadata using Raft
- Simpler operations; better scalability (millions of partitions)
- Default in Kafka 3.x+

**Kafka vs alternatives**:

| | Kafka | RabbitMQ | AWS SQS | AWS Kinesis |
|-|-------|----------|---------|-------------|
| Model | Log | Queue | Queue | Log |
| Throughput | Very high (millions/s) | High | Medium | High |
| Ordering | Per partition | Per queue | Best effort | Per shard |
| Replay | Yes | No | No | Yes (24h) |
| Retention | Configurable | Until ACK | Up to 14 days | 7 days (extendable) |
| Ops complexity | High | Medium | None (managed) | None (managed) |

### Delivery Semantics in Depth

**At-most-once**:
```
Producer sends → Broker → Consumer receives → commit offset → process
(crash after commit but before process → message lost)
```

**At-least-once**:
```
Producer sends → Broker → Consumer receives → process → commit offset
(crash before commit → reprocess on restart → possible duplicate)
```

**Exactly-once**:
```
Producer idempotent + Kafka transactions + idempotent consumer logic
(deduplication by message ID or operation must be naturally idempotent)
```

### Stream Processing Systems

**Apache Flink**:
- True streaming (event by event), not micro-batching
- Event-time processing with watermarks
- Exactly-once stateful processing
- Checkpointing to durable storage (S3, HDFS)
- Best for: low latency, complex stateful operations, CEP (complex event processing)

**Apache Spark Structured Streaming**:
- Micro-batching (also supports continuous mode)
- Unified batch + streaming API
- Strong ecosystem (MLlib, GraphX)
- Best for: teams already using Spark, ML pipelines

**Kafka Streams**:
- Embedded library (no cluster)
- Best for: Kafka-native apps, simpler topologies

**Apache Flink vs Spark Streaming**:
| | Flink | Spark Streaming |
|-|-------|----------------|
| Model | True streaming | Micro-batch (default) |
| Latency | Milliseconds | Seconds (micro-batch) |
| State | Rich, queryable | Limited |
| Exactly-once | Yes | Yes |
| Maturity | High | Very high |

---

## 13. Distributed Caching

### Why Distributed Cache?

- Single-node cache is lost on restart, limited by RAM
- Distributed cache: shared state across app servers, survives restarts, scales horizontally

### Redis — Deep Dive

**Data structures**: String, List, Hash, Set, Sorted Set, Bitmap, HyperLogLog, Streams, Geospatial

**Persistence options**:
- **RDB (Snapshot)**: periodic snapshot to disk; fast restart, may lose recent data
- **AOF (Append-Only File)**: log every write; slower restart, near-zero data loss
- **AOF + RDB**: recommended for production

**High Availability**:
- **Redis Sentinel**: monitors master + replicas; automatic failover; 3 sentinels minimum
- **Redis Cluster**: horizontal sharding across 16,384 hash slots; N masters + replicas

**Redis Cluster sharding**:
```
hash_slot = CRC16(key) % 16384
Each master owns a range of slots
Redirects (MOVED/ASK) guide clients to correct node
```

**Redis use cases**:
- Caching (most common)
- Session store
- Rate limiting (INCR + EXPIRE or sorted sets)
- Distributed locks (SET NX PX)
- Pub/Sub (limited — no persistence, no consumer groups)
- Leaderboards (Sorted Set: ZADD, ZRANK, ZRANGE)
- Message queue (List: LPUSH/BRPOP or Redis Streams)
- Real-time analytics (HyperLogLog for unique counts, Bitmaps for daily active users)

**Redis Streams** (Kafka-like):
- Append-only log with consumer groups
- Persistent, supports replay
- Lighter weight than Kafka; not for extreme throughput

### Cache Patterns

**Cache-aside (Lazy loading)**:
```
Read: check cache → hit? return → miss? query DB → store in cache → return
Write: write to DB → invalidate or update cache
```
- Application manages cache
- Cache contains only accessed data (no cold data)

**Read-through**:
- Cache sits between app and DB
- Cache handles DB reads on miss
- Application only interacts with cache

**Write-through**:
```
Write → cache AND DB synchronously
```
- Always consistent; higher write latency

**Write-behind (write-back)**:
```
Write → cache only → async flush to DB
```
- Fastest writes; risk of data loss on cache failure

**Refresh-ahead**:
- Proactively reload cache before TTL expires
- Reduces miss rate; wastes resources if item isn't accessed again

### Cache Consistency Strategies

| Strategy | Consistency | Complexity |
|----------|-------------|------------|
| TTL-based expiry | Eventual | Low |
| Cache invalidation on write | Strong | Medium |
| Cache-aside with version check | Strong | Medium |
| Write-through | Strong | Low |
| Two-phase invalidation | Strong | High |

### Thundering Herd / Cache Stampede

When a popular cache key expires, all requests simultaneously miss and hit the DB.

**Solutions**:
- **Mutex / probabilistic early expiry**: one request refreshes; others wait or get stale
- **Background refresh**: async cache refresh before expiry; requests always get cached data
- **Request coalescing**: deduplicate concurrent misses for same key

---

## 14. Networking in Distributed Systems

### RPC (Remote Procedure Call)

Makes remote calls look like local function calls.

**gRPC**:
- HTTP/2 transport; Protocol Buffers serialization
- Strongly typed `.proto` files → code generation
- Streaming: unary, server-streaming, client-streaming, bidirectional
- Interceptors (middleware for auth, logging, tracing)

```protobuf
service UserService {
  rpc GetUser (GetUserRequest) returns (User);
  rpc StreamUsers (Empty) returns (stream User);
}
```

**Protobuf vs JSON**:
| | Protobuf | JSON |
|-|----------|------|
| Size | 3-10× smaller | Larger |
| Speed | Faster encode/decode | Slower |
| Human-readable | No | Yes |
| Schema | Required (.proto) | Optional |
| Browser support | Limited | Native |

### Service Discovery

**Client-side discovery**:
```
[Client] → [Service Registry (Consul/Eureka)] → get instance list
[Client] → choose instance (load balance) → call directly
```
- Client handles load balancing
- More client logic; tighter coupling to registry

**Server-side discovery**:
```
[Client] → [Load Balancer / API Gateway] → [Service Registry] → route to instance
```
- Client only knows load balancer address
- Cleaner client; LB is a potential bottleneck

**DNS-based discovery**:
- Service name → DNS → IP (or multiple IPs)
- Kubernetes uses this (ClusterIP + CoreDNS)
- Simplest; limited control over routing

### Network Topologies in Distributed Systems

**Mesh**: every node connected to every other — high bandwidth, O(N²) connections
**Star**: central hub — single point of failure
**Ring**: each node connected to two neighbors — resilient, used in Cassandra ring
**Tree**: hierarchical — used in HBase, HDFS (name node tree)

### Backpressure

When a downstream service is slower than upstream produces, the queue grows unboundedly.

**Strategies**:
- **Drop**: shed load; return 503 (circuit breaker)
- **Buffer**: accept and queue; risk of memory exhaustion
- **Slow down upstream**: send feedback (reactive streams, TCP flow control)
- **Scale out**: add more consumers

**Reactive Streams** (Java): Publisher → Subscriber with `request(N)` to control flow

---

## 15. Fault Tolerance Patterns

### Circuit Breaker

```
CLOSED (normal) ──→ threshold failures ──→ OPEN (fail fast)
                                              ↓
                                    timeout → HALF-OPEN (probe)
                                              ↓
                              success → CLOSED  /  failure → OPEN
```

**States**:
- **CLOSED**: requests pass through; failures counted
- **OPEN**: requests immediately rejected (no downstream call)
- **HALF-OPEN**: allow N requests through to test recovery

**Metrics to track**: failure rate, slow call rate, consecutive failures

**Libraries**: Resilience4j (Java), Polly (.NET), Hystrix (legacy), Envoy proxy (built-in)

### Bulkhead

Isolate failures in one part of the system from cascading to others.

```
Without bulkhead:
[Service A calls both B and C]
B is slow → A's thread pool exhausted → A can't call C → cascade failure

With bulkhead:
[Service A has separate thread pool / semaphore for B and C]
B is slow → only B's pool fills → A still calls C normally
```

**Types**:
- **Thread pool bulkhead**: separate thread pool per downstream dependency
- **Semaphore bulkhead**: limit concurrent calls via semaphore (lighter than thread pool)
- **Process/container bulkhead**: run critical services in separate processes/pods

### Retry with Exponential Backoff and Jitter

```python
def retry(operation, max_attempts=5, base_delay=100ms, cap=10s):
    for attempt in range(max_attempts):
        try:
            return operation()
        except TransientError:
            if attempt == max_attempts - 1:
                raise
            # Exponential backoff with full jitter
            delay = random.uniform(0, min(cap, base_delay * 2**attempt))
            sleep(delay)
```

**Jitter strategies**:
- **Full jitter**: `random(0, min(cap, base * 2^n))` — best for reducing collision
- **Equal jitter**: `min(cap, base * 2^n) / 2 + random(0, min(cap, base * 2^n) / 2)`
- **Decorrelated jitter**: `random(base, previous_delay * 3)`

**When NOT to retry**:
- Non-idempotent operations (POST without idempotency key)
- Client errors (4xx) — retrying won't help
- Rate limit errors — wait for Retry-After header

### Timeout Hierarchy

```
Client request timeout
└── Service A call timeout
    ├── DB query timeout
    ├── Cache timeout
    └── Service B call timeout
        └── External API timeout
```

**Deadline propagation**: pass remaining budget down the call chain
```
Request starts with 5s budget
Service A uses 1s → passes 4s budget to Service B
Service B uses 1s → passes 3s budget to DB
```
Implemented via gRPC deadlines, HTTP headers (X-Request-Timeout)

### Idempotency

Safe to call multiple times with the same result.

**Naturally idempotent**: GET, DELETE (with check), PUT (full replace)
**Not naturally idempotent**: POST, PATCH (increment)

**Idempotency key pattern**:
```
Client generates UUID idempotency key
Client sends key in header: Idempotency-Key: uuid-123
Server stores (key → result) in DB
If key seen again: return stored result, skip processing
```

**Implementation**: store in Redis with TTL (24h), or in DB with unique constraint on key

### Graceful Degradation

Return partial/degraded response when a dependency fails, rather than total failure.

**Examples**:
- Product page: if recommendation service down → show page without recommendations
- News feed: if personalization fails → show generic trending content
- Payment: if fraud check times out → allow payment and do async fraud check

**Feature flags** enable runtime toggling of degraded mode.

---

## 16. Scalability Patterns

### Stateless Services

Move all session/user state to external store (Redis, DB).
- Any node can serve any request
- Easy horizontal scaling; just add nodes
- No sticky sessions required

### Database Read Scaling

**Read replicas**: route read queries to replica nodes
- Replication lag means reads may be stale
- Good for: analytics queries, search, non-critical reads

**CQRS Read Model**: separate optimized read database (e.g., Elasticsearch) updated via events
**Materialized views**: pre-computed query results stored and updated incrementally

### Write Scaling

**Sharding**: distribute writes across multiple primary nodes
**Write-optimized storage**: LSM-tree (Cassandra, RocksDB) vs B-Tree (PostgreSQL)
- LSM: fast sequential writes, slower reads (compaction needed)
- B-Tree: fast reads, slower writes (in-place updates)

**Event sourcing**: append-only event log is naturally write-scalable

### Horizontal vs Vertical Scaling

| | Vertical (Scale Up) | Horizontal (Scale Out) |
|-|---------------------|----------------------|
| Method | Bigger machine | More machines |
| Limit | Hardware ceiling | Virtually unlimited |
| Cost | Expensive at top | Commodity hardware |
| Complexity | Simple | Requires distributed thinking |
| Failure impact | Single machine failure = outage | Partial failure tolerable |
| Best for | Databases (simpler), quick wins | Stateless services, web tier |

### Auto-Scaling Strategies

**Reactive scaling**: scale in response to current load (CPU > 70% → add instances)
**Predictive scaling**: ML model predicts traffic → scale ahead of time
**Scheduled scaling**: known traffic patterns (e.g., 9am peak) → pre-scale

**Kubernetes HPA (Horizontal Pod Autoscaler)**:
```yaml
metrics:
- type: Resource
  resource:
    name: cpu
    target:
      averageUtilization: 70
- type: External  # custom metrics (RPS, queue depth)
```

### Rate Limiting

**Token Bucket**:
```
bucket holds up to capacity tokens
tokens added at rate r tokens/second
request consumes 1 token; if empty → reject
allows bursts up to capacity
```

**Leaky Bucket**:
```
requests enter queue; processed at fixed rate
excess requests dropped
smooth output regardless of bursty input
```

**Sliding Window Counter** (efficient implementation):
```
count = requests[current_window] + requests[prev_window] * overlap_fraction
if count > limit → reject
```

**Distributed rate limiting with Redis**:
```
INCR user:123:minute:1714560
EXPIRE user:123:minute:1714560 60
GET value → compare to limit
```

**Hierarchical rate limits**:
- Global: 10,000 RPS across all users
- Per-tenant: 1,000 RPS per organization
- Per-user: 100 RPS per individual user

---

## 17. Observability in Distributed Systems

### The Three Pillars

**Metrics** — aggregated numerical data over time:
- Infrastructure: CPU, memory, disk I/O, network I/O
- Application: RPS, latency, error rate, queue depth
- Business: orders/min, revenue/hour, signups/day

**Logs** — discrete events with context:
- Structured logging (JSON): machine-parseable, filterable
- Log levels: TRACE, DEBUG, INFO, WARN, ERROR, FATAL
- Correlation ID: tie all logs from one request across services

**Traces** — request lifecycle across services:
- **Span**: single operation (DB query, HTTP call, function)
- **Trace**: collection of spans forming a DAG
- **Context propagation**: trace ID + span ID passed via headers (`traceparent` W3C standard)
- **Sampling**: head-based (sample at ingress), tail-based (decide after trace complete)

**OpenTelemetry (OTel)**: open standard for metrics, logs, and traces
- Vendor-neutral SDK + collector
- Exporters for: Jaeger, Zipkin, Prometheus, Datadog, New Relic

### Distributed Tracing Implementation

```
[Service A] creates trace_id=abc, span_id=001
[Service A] → HTTP call → [Service B]
  Headers: traceparent: 00-abc-001-01
[Service B] creates span_id=002, parent=001
[Service B] → DB call → span_id=003, parent=002
```

Trace visualization: Gantt chart showing parallelism and critical path.

### Key Metrics

**The Four Golden Signals**:
1. **Latency**: time to serve a request (p50, p95, p99, p99.9)
2. **Traffic**: requests per second; throughput
3. **Errors**: error rate (5xx, 4xx, business errors)
4. **Saturation**: how full the system is (CPU %, queue depth, connection pool usage)

**Percentiles vs averages**:
- Average hides outliers
- p99 = worst 1% of users; p99.9 = worst 0.1%
- Use histograms for latency distribution

**SLI/SLO/SLA**:
```
SLI (measured):  99.94% availability over last 30 days
SLO (target):    99.9% availability
SLA (contract):  99.5% availability (with penalty)

Error budget: 1 - SLO = 0.1% = 43.8 minutes downtime/month
```

### Alerting Strategy

**Alert on symptoms, not causes**:
- Alert on: high error rate, high latency, saturation approaching limit
- Don't alert on: CPU usage (unless it's causing symptoms)

**Alert routing**:
- P1 (critical): page on-call immediately
- P2 (high): page during business hours
- P3 (medium): create ticket
- P4 (low): log for review

**Runbook**: every alert links to a runbook with diagnosis steps and resolution procedures

**Dead man's switch**: alert fires if a heartbeat stops (catches silent failures like stuck cron jobs)

---

## 18. Security in Distributed Systems

### Authentication Between Services

**mTLS (Mutual TLS)**:
- Both client and server present certificates
- Cryptographically verified identity
- Used in service meshes (Istio, Linkerd auto-provision certs via SPIFFE/SPIRE)

**JWT (JSON Web Tokens)**:
```
Header.Payload.Signature
```
- Stateless (no DB lookup to verify)
- Signed with private key; verified with public key
- Short-lived access token (15min) + long-lived refresh token
- **Risk**: cannot revoke before expiry without a blocklist

**Service Accounts / API Keys**:
- Static credentials for machine-to-machine auth
- Rotate regularly; store in secrets manager (Vault, AWS Secrets Manager)

**SPIFFE / SPIRE**:
- Standards for service identity in distributed systems
- Each workload gets a cryptographic identity (SPIFFE ID: `spiffe://trust-domain/path`)
- Used by Istio, Envoy for zero-trust networking

### Secrets Management

**HashiCorp Vault**:
- Dynamic secrets (generate DB credentials on-demand with TTL)
- Secret leasing + renewal
- Audit logging of every secret access
- Encryption as a service

**AWS Secrets Manager / Parameter Store**:
- Managed secrets with rotation
- KMS-backed encryption
- IAM-based access control

**Anti-patterns**:
- Secrets in environment variables (leaked in logs, process list)
- Secrets in container images
- Secrets in source code

### Network Security

**Zero Trust Networking**:
- Never trust, always verify
- No implicit trust based on network location
- Every request authenticated + authorized regardless of source

**Network segmentation**:
- VPC / VNet per environment
- Private subnets for backend services (no internet access)
- Security groups / NACLs to restrict traffic

**Service mesh security** (Istio):
```
Policy: only Service A can call Service B on port 8080
All traffic encrypted with mTLS automatically
Certificate rotation every 24h
```

### Data Security

**Encryption at rest**: AES-256 for stored data; KMS-managed keys
**Encryption in transit**: TLS 1.3 for all inter-service communication
**Field-level encryption**: encrypt specific sensitive fields (PII, PAN) separately

**Data classification**:
- **Public**: any user can see
- **Internal**: employees only
- **Confidential**: limited team access
- **Restricted**: minimal access (PII, financial data, credentials)

---

## 19. Distributed System Architectures

---

### 19.1 Master-Slave Architecture

One primary (master) node handles writes; one or more secondary (slave/replica) nodes handle reads.

```
          ┌─────────────────────────────┐
[Writes]──▶   Master (Primary)          │
          │   - Accepts all writes      │
          │   - Replicates to slaves    │
          └──────────┬──────────────────┘
                     │ Replication
          ┌──────────▼──────────────────┐
          │  Slave 1     Slave 2        │
          │  [Reads]     [Reads]        │
          └─────────────────────────────┘
```

**How it works**:
- Master processes all writes
- Changes propagate to slaves (sync or async)
- Reads distributed across slaves
- On master failure: promote a slave (requires election or manual intervention)

**Characteristics**:

| Property | Value |
|----------|-------|
| Consistency | Strong (sync) or eventual (async) |
| Write scalability | Limited (single master) |
| Read scalability | High (add more slaves) |
| Fault tolerance | Master failure = downtime until failover |
| Complexity | Low |

**When to choose Master-Slave**:
- Read-heavy workloads (10:1 or higher read:write ratio)
- Need for geographic read distribution
- Reporting/analytics running against replicas (to avoid impacting writes)
- Simple operational requirements
- PostgreSQL streaming replication, MySQL replication, Redis primary-replica

**When NOT to choose**:
- Write-heavy workloads (master becomes bottleneck)
- Need for zero-downtime failover without external tooling
- Globally distributed writes (high latency to single master)

**Real-world examples**: MySQL with read replicas, Redis Sentinel, MongoDB primary + secondaries

---

### 19.2 Peer-to-Peer (P2P)

All nodes are equal; no central coordinator. Nodes communicate directly with each other.

```
  [Node A] ──── [Node B]
     │    ╲   ╱   │
     │     ╲ ╱    │
  [Node D] ──── [Node C]
```

**Variants**:
- **Unstructured P2P**: random connections (Gnutella); hard to find resources
- **Structured P2P (DHT)**: Distributed Hash Table routes queries to responsible node (Chord, Kademlia)
- **Gossip networks**: information spreads by epidemic propagation

**DHT (Distributed Hash Table)**:
```
Each node responsible for keys in its range (consistent hashing)
To find key K: route to node responsible for hash(K)
Routing: O(log N) hops
```

**Characteristics**:

| Property | Value |
|----------|-------|
| Consistency | Eventual |
| Scalability | Very high |
| Single point of failure | None |
| Fault tolerance | High |
| Coordination overhead | Low |
| Query complexity | High (no central index) |

**When to choose P2P**:
- Massive scale without central coordination
- Fault tolerance is paramount
- Data ownership distributed among many parties
- Gossip/membership protocols within larger systems

**Real-world examples**: BitTorrent, IPFS, Cassandra (gossip layer), Amazon Dynamo, Bitcoin/Ethereum

**When NOT to choose**:
- Strong consistency required
- Complex queries spanning many nodes
- Need for centralized access control
- Small-scale systems (overhead not worth it)

---

### 19.3 Leader-Follower Cluster

A group of nodes elect a single leader via consensus. The leader makes all decisions; followers replicate the leader's state.

```
         ┌─────────────────────────────┐
         │     Leader (elected)        │
         │   - Receives client writes  │
         │   - Replicates log entries  │
         └────────┬───────────────────-┘
          Raft/Paxos replication
    ┌─────────────┼─────────────┐
[Follower 1]  [Follower 2]  [Follower 3]
```

**How it works (Raft)**:
- Nodes elect a leader (majority vote)
- Leader receives writes, appends to log, replicates to followers
- Entry committed when majority of nodes ACK
- On leader failure: followers elect new leader in seconds

**Characteristics**:

| Property | Value |
|----------|-------|
| Consistency | Linearizable |
| Write throughput | Moderate (single leader) |
| Fault tolerance | Tolerates ⌊(N-1)/2⌋ failures |
| Failover | Automatic (seconds) |
| Complexity | Medium |

**When to choose Leader-Follower Cluster**:
- Metadata storage requiring strong consistency (cluster state, config)
- Distributed locking and coordination
- Small datasets requiring consensus (not petabytes)
- Coordination services, not data storage

**Real-world examples**: etcd (Kubernetes), ZooKeeper, Consul, CockroachDB ranges, TiKV, InfluxDB Enterprise

**When NOT to choose**:
- Large-scale data storage (not designed for it)
- Extremely high write throughput (single leader bottleneck)
- Latency-sensitive workloads where consensus round-trip is too slow

---

### 19.4 Sharded Cluster

Data partitioned (sharded) across multiple independent nodes or replica sets, each responsible for a subset of data.

```
                 [Router / Coordinator]
                /         |          \
[Shard 1       ] [Shard 2       ] [Shard 3       ]
[Keys: A-1000  ] [Keys: 1001-2000] [Keys: 2001+  ]
[Primary+Replica] [Primary+Replica] [Primary+Replica]
```

**How it works**:
- Router (mongos, Cassandra coordinator) maps request key to correct shard
- Each shard is an independent replica set
- Config servers store shard mapping (in MongoDB, ZooKeeper, or etcd)

**Characteristics**:

| Property | Value |
|----------|-------|
| Write scalability | High (each shard takes a subset) |
| Read scalability | High |
| Cross-shard queries | Expensive (scatter-gather) |
| Complexity | High |
| Fault tolerance | Per-shard (replica sets within each shard) |

**When to choose Sharded Cluster**:
- Dataset too large for a single server (hundreds of TB to PB)
- Write throughput exceeds single-node capacity
- Partition key is well-defined and queries are mostly within a shard
- Working set fits in RAM per shard

**Design requirements**:
- Choose partition key carefully (avoid hotspots)
- Most queries should include the shard key
- Avoid excessive cross-shard transactions

**Real-world examples**: MongoDB sharded cluster, Cassandra, Vitess (MySQL sharding), Redis Cluster, Elasticsearch

**When NOT to choose**:
- Dataset fits on one server (adds complexity for no gain)
- Application requires frequent cross-shard joins
- Partition key is hard to define (schema doesn't lend itself to horizontal partitioning)

---

### 19.5 Multi-Region Active-Active

Multiple regions each serve traffic independently. All regions accept reads and writes. Data synchronized bidirectionally.

```
[Users US] ──▶ [Region: US-East]  ◀──▶ [Region: EU-West] ◀── [Users EU]
                [Services + DB]   sync  [Services + DB]
                      ▲                        ▲
                      │           sync         │
               [Region: APAC] ◀──────▶ [Region: US-West]
```

**How it works**:
- GeoDNS or Anycast routes users to nearest region
- Each region has full stack (compute + storage)
- Writes accepted locally; asynchronously replicated to other regions
- Conflict resolution required for concurrent writes to same data

**Conflict resolution strategies**:
- **Last-Write-Wins (LWW)**: highest timestamp wins — simple, may lose data
- **Application-level merge**: app defines how to merge conflicts (Google Docs)
- **CRDTs (Conflict-free Replicated Data Types)**: data structures that merge automatically and correctly

**Characteristics**:

| Property | Value |
|----------|-------|
| Availability | Very high (region failure isolated) |
| Latency | Very low (local region serves request) |
| Consistency | Eventual (cross-region) |
| Write conflicts | Must handle explicitly |
| Complexity | Very high |
| Cost | Very high |

**When to choose Multi-Region Active-Active**:
- Global user base with strict latency requirements (<100ms SLO globally)
- Regulatory requirements to keep data in specific regions, but serve from multiple
- Business continuity: any region can fail without service interruption
- 99.99%+ availability SLO required

**Real-world examples**: Netflix (global), DynamoDB global tables, Cassandra multi-datacenter, CockroachDB multi-region, CloudFlare

**When NOT to choose**:
- Application data has strong consistency requirements (conflicts are hard)
- Insufficient budget for multi-region infrastructure
- Low traffic volume (single region is sufficient)
- Simple applications where operational complexity outweighs benefits

---

### 19.6 Multi-Region Active-Passive

One primary region handles all writes. Secondary regions receive replicated data and serve reads, or wait as hot standby for failover.

```
[All Users] ──▶ [Primary Region: US-East]
                 [Accepts all reads + writes]
                        │ Async replication
                        ▼
               [DR Region: US-West]
               [Reads only / Standby]
```

**Variants**:
- **Active-Passive (hot standby)**: secondary is ready to take traffic immediately; failover in minutes
- **Active-Passive (warm standby)**: secondary running but needs some initialization; failover in 10–30 min
- **Active-Passive (cold standby)**: secondary infrastructure provisioned from backups; failover in hours

**RTO and RPO**:
```
RTO (Recovery Time Objective): how fast must we recover?
RPO (Recovery Point Objective): how much data loss is acceptable?

Hot standby:  RTO minutes, RPO seconds (near-zero with sync replication)
Warm standby: RTO 15-30 min, RPO minutes
Cold standby: RTO hours,    RPO hours
```

**Characteristics**:

| Property | Value |
|----------|-------|
| Availability | High (region failure = failover) |
| Consistency | Strong in primary; eventual in secondary |
| Write conflicts | None (single write region) |
| Complexity | Medium |
| Cost | Medium-High |
| Failover time | Minutes (hot) to hours (cold) |

**When to choose Multi-Region Active-Passive**:
- Disaster recovery requirements (RTO/RPO defined in SLA)
- Data consistency is paramount (can't afford conflicts of active-active)
- Write volume doesn't require geographic distribution
- Compliance: data must be replicated but writes must stay in one region

**Real-world examples**: AWS RDS Multi-AZ + read replicas, Aurora Global Database, PostgreSQL streaming replication to DR

**When NOT to choose**:
- Global users need low write latency (all writes go to primary region)
- Need zero-downtime on region failure (active-passive has a failover gap)
- Cost of idle DR region is not justified

---

### 19.7 Gossip-Based (Epidemic) Architecture

Nodes spread information by randomly selecting a subset of peers and sharing state. Information propagates exponentially, like an epidemic.

```
Round 1: A knows info X → tells B, C
Round 2: B tells D, E; C tells F, G
Round 3: D tells H, I; E tells J, K; ...
All N nodes informed in O(log N) rounds
```

**Push vs Pull vs Push-Pull**:
- **Push**: node sends its state to peers
- **Pull**: node asks peers for their state
- **Push-Pull**: both directions in one round (most efficient)

**Use cases within distributed systems**:
- **Cluster membership**: Cassandra, Consul, Riak — who is alive/dead?
- **Failure detection**: SWIM protocol uses gossip to propagate failure info
- **Data dissemination**: CRDTs propagate updates via gossip
- **Distributed counters**: approximate aggregate counts without central coordinator

**Characteristics**:

| Property | Value |
|----------|-------|
| Convergence | Eventual (O(log N) rounds) |
| Fault tolerance | Very high (no coordinator) |
| Consistency | Eventual |
| Bandwidth | O(N log N) per round |
| Message size | Grows with cluster state |

**When to use Gossip**:
- Cluster membership and failure detection in large clusters
- Decentralized state dissemination
- No central coordinator can be tolerated

**When NOT to use for primary data delivery**:
- Strong consistency required
- Need for guaranteed delivery (gossip can miss nodes)
- Small cluster where simpler mechanisms suffice

---

### 19.8 Hierarchical / Tree-Based Architecture

Nodes organized in a tree; parent coordinates children. Requests flow up to parents; responses or assignments flow down.

```
                [Root Coordinator]
               /                 \
    [Region Coordinator A]   [Region Coordinator B]
      /        \                /          \
[Worker 1] [Worker 2]   [Worker 3]    [Worker 4]
```

**How it works**:
- Root has global view
- Intermediate nodes manage their subtree
- Leaf nodes do actual work
- Messages aggregate as they travel up the tree

**Examples**:
- **HDFS**: NameNode (root) → DataNodes (leaves)
- **Kubernetes**: API server → kubelets (nodes) → pods (leaves)
- **HBase**: HMaster → Region Servers → Regions
- **MapReduce**: Job Tracker → Task Trackers → Map/Reduce tasks

**Characteristics**:

| Property | Value |
|----------|-------|
| Scalability | High (subtrees are independent) |
| Single point of failure | Root node |
| Fault tolerance | Medium (subtree fails if coordinator fails) |
| Latency | O(depth) for communication |
| Complexity | Medium |

**When to choose Hierarchical Architecture**:
- Aggregating data from many sources to a central place (monitoring, log collection)
- Task scheduling and work distribution at scale
- Resource management in large clusters

**When NOT to choose**:
- Latency-critical path requires minimal hops
- Root node becomes bottleneck (mitigate with multiple roots)

---

### 19.9 Lambda Architecture

Hybrid batch + streaming pipeline for large-scale data processing.

```
               ┌──────────────────────────────────────────┐
               │              Data Source                 │
               └────────────┬─────────────────────────────┘
                            │
                ┌───────────▼──────────┐
                │      Batch Layer     │
                │ (Hadoop/Spark)       │
                │ Reprocesses ALL data │
                │ high accuracy        │
                └───────────┬──────────┘
                            │ batch views (hours)
                ┌───────────▼──────────┐
                │     Speed Layer      │
                │ (Flink/Storm/Kafka)  │
                │ Only recent data     │
                │ low latency          │
                └───────────┬──────────┘
                            │ real-time views (seconds)
                ┌───────────▼──────────┐
                │    Serving Layer     │
                │ Merges batch +       │
                │ real-time views      │
                └──────────────────────┘
```

**Characteristics**:

| Property | Value |
|----------|-------|
| Latency | Low (speed layer) |
| Accuracy | High (batch layer) |
| Complexity | Very high (two codebases) |
| Fault tolerance | High |
| Reprocessing | Full historical reprocessing possible |

**When to choose Lambda Architecture**:
- Both real-time and historical accuracy required
- Stream processing is insufficient for complete accuracy (late data, corrections)
- Team capacity to maintain two separate processing pipelines
- Use case: fraud detection, IoT analytics, clickstream, recommendation systems

**When NOT to choose**:
- Streaming accuracy is sufficient for the use case (prefer Kappa)
- Small team cannot maintain two pipelines
- Low latency is not required (batch only is simpler)

---

### 19.10 Kappa Architecture

Simplification of Lambda — single streaming pipeline for both real-time and historical processing.

```
               ┌─────────────────────────────────────┐
               │          Data Source                │
               └───────────────┬─────────────────────┘
                               │
               ┌───────────────▼───────────────────┐
               │         Kafka (durable log)        │
               │    retain data for N days/forever   │
               └───────────────┬─────────────────────┘
                               │
               ┌───────────────▼───────────────────┐
               │    Stream Processor (Flink)         │
               │  processes real-time AND replay     │
               └───────────────┬─────────────────────┘
                               │
               ┌───────────────▼───────────────────┐
               │          Serving Layer              │
               └─────────────────────────────────────┘
```

**Historical reprocessing**: replay from Kafka offset 0 with a new version of the processing job.

**Characteristics**:

| Property | Value |
|----------|-------|
| Complexity | Lower than Lambda (one codebase) |
| Reprocessing | Via Kafka replay |
| Latency | Low |
| Requires | Long-term Kafka retention |

**When to choose Kappa Architecture**:
- Streaming is primary processing model
- Team wants to maintain single codebase
- Kafka retention is sufficient for reprocessing needs
- Use case: event-driven analytics, log processing, microservice event sourcing

**Kappa vs Lambda**:
| | Lambda | Kappa |
|-|--------|-------|
| Codebases | Two (batch + stream) | One |
| Batch accuracy | Highest | Good (may need tuning) |
| Operational complexity | High | Medium |
| Reprocessing | Full historical | Kafka retention limit |
| Choose when | Max accuracy needed | Simplicity preferred |

---

### 19.11 CQRS + Event Sourcing

**CQRS (Command Query Responsibility Segregation)**: separate the write model (command side) from the read model (query side).

**Event Sourcing**: store state as an immutable sequence of events rather than current state.

```
[Client]
    │
    ├── [Commands] ──▶ [Command Handler]
    │                       │
    │                  [Domain Logic]
    │                       │
    │                  [Event Store]  ◀── immutable event log
    │                       │
    │                  [Event Bus]
    │                  /    |    \
    │           [Proj A] [Proj B] [Proj C]
    │              │        │        │
    │           [Read DB] [Cache] [Search]
    │
    └── [Queries] ──▶ [Query Handler] ──▶ [Read Model]
```

**Event Store**: append-only log of domain events
```
[UserRegistered{id:1, email:a@b.com}]
[UserEmailChanged{id:1, email:c@d.com}]
[UserDeactivated{id:1}]
```
Current state = replay events from beginning (or from snapshot).

**Snapshots**: periodic snapshot of aggregate state to speed up replay.
```
Snapshot at event 500 → only replay events 500 → current
```

**Projections**: read models built by consuming events
- Can be rebuilt at any time by replaying events
- Different projections for different query patterns

**Characteristics**:

| Property | Value |
|----------|-------|
| Audit trail | Complete (every event stored) |
| Time travel | Rebuild state at any point in history |
| Read scalability | High (multiple optimized read models) |
| Write scalability | High (append-only event store) |
| Complexity | Very high |
| Eventual consistency | Read models lag behind write side |

**When to choose CQRS + Event Sourcing**:
- Complete audit log is a business/regulatory requirement
- Complex domain with multiple different query patterns
- Need to rebuild historical state for debugging or compliance
- Event-driven architecture where services subscribe to domain events
- Use cases: financial transactions, healthcare records, supply chain, e-commerce orders

**When NOT to choose**:
- Simple CRUD applications (CQRS adds enormous complexity for little gain)
- Team unfamiliar with event-driven design patterns
- Schema evolution of events is complex (must handle all historical event versions)
- Consistency requirements mean read models must be real-time

---

### 19.12 Cell-Based Architecture

System divided into independent, fully-functional cells. Each cell is a complete instance of the full system serving a subset of users/tenants.

```
            [Global Router / Control Plane]
           /            |               \
  ┌────────────┐ ┌────────────┐ ┌────────────┐
  │  Cell 1   │ │  Cell 2   │ │  Cell 3   │
  │ Users A-M │ │ Users N-Z │ │ EU Region │
  │           │ │           │ │           │
  │ [API]     │ │ [API]     │ │ [API]     │
  │ [Services]│ │ [Services]│ │ [Services]│
  │ [DB]      │ │ [DB]      │ │ [DB]      │
  │ [Cache]   │ │ [Cache]   │ │ [Cache]   │
  └────────────┘ └────────────┘ └────────────┘
```

**Cell assignment strategies**:
- User ID range (hash modulo)
- Tenant/organization ID
- Geography
- Random with lookup table

**Blast radius containment**:
- Failure in Cell 1 affects only Cell 1's users
- Cells do not share mutable state (no cross-cell DB calls in hot path)
- A deployment bug in Cell 1 doesn't affect Cell 2 or 3

**Characteristics**:

| Property | Value |
|----------|-------|
| Fault isolation | Excellent |
| Blast radius | Limited to one cell |
| Scalability | Add more cells |
| Data sovereignty | Natural (EU users in EU cell) |
| Cross-cell queries | Very expensive; by design discouraged |
| Operational complexity | High (many identical deployments) |

**When to choose Cell-Based Architecture**:
- Regulatory data residency requirements (GDPR, HIPAA per region)
- Need to limit blast radius of failures and deployments
- Multi-tenant SaaS where tenant isolation is critical
- At massive scale where even a single region needs internal isolation
- Rolling deployments: deploy to one cell, validate, then roll forward

**When NOT to choose**:
- Small-scale systems (overhead not justified)
- Queries that must span all users (cross-cell aggregation is very hard)
- When users need real-time collaboration across cells

**Real-world examples**: AWS (availability zones are cells), Slack, Shopify, Stripe (payment cells)

---

## 20. Architecture Selection Guide

### Decision Framework

When selecting a distributed architecture, evaluate:

1. **Consistency requirement**: Is strong consistency required, or is eventual consistency acceptable?
2. **Scale**: How much data, how many users, what QPS?
3. **Write vs read ratio**: Write-heavy vs read-heavy?
4. **Latency requirement**: Real-time (<10ms), near-real-time (<1s), batch (minutes/hours)?
5. **Geographic distribution**: Single region, multi-region reads, multi-region writes?
6. **Failure tolerance**: What is acceptable RTO and RPO?
7. **Operational maturity**: Team expertise and budget for infrastructure?
8. **Data sovereignty**: Legal requirements on where data must reside?

---

### Scenario → Architecture Mapping

#### Scenario: High Read-to-Write Ratio, Simple Data Model
**Example**: social media profile views, product catalog reads

**Choose**: Master-Slave with Read Replicas
- Single master for writes
- Multiple read replicas
- CDN cache for static data
- Redis for frequent reads

**Avoid**: Sharding (unnecessary complexity), Active-Active (overkill)

---

#### Scenario: Globally Distributed Users, Write Anywhere
**Example**: global multiplayer game, collaborative documents

**Choose**: Multi-Region Active-Active
- Writes accepted in nearest region
- CRDTs or LWW for conflict resolution
- Eventual consistency acceptable

**Avoid**: Master-Slave (high write latency for remote users), Active-Passive (failover gap)

---

#### Scenario: Disaster Recovery, Primary Region Handles All Writes
**Example**: enterprise application with DR requirements

**Choose**: Multi-Region Active-Passive
- Hot or warm standby based on RTO requirement
- Sync replication if RPO = 0, async if RPO = seconds acceptable

---

#### Scenario: Massive Dataset, Write-Heavy, Key-Value Access
**Example**: IoT sensor data, user activity logs, time-series metrics

**Choose**: Sharded Cluster (Cassandra or DynamoDB)
- Wide-column store with partition key = device ID or user ID
- Leaderless replication (high write availability)
- Tunable consistency per query

---

#### Scenario: Strong Consistency, Distributed Transactions Required
**Example**: financial ledger, inventory management, ticketing system

**Choose**: Leader-Follower Cluster (Raft) + NewSQL (CockroachDB / Google Spanner)
- Linearizable reads/writes
- Distributed transactions with MVCC
- Pay latency cost for correctness

**Avoid**: Cassandra, DynamoDB (eventual consistency makes financial correctness hard)

---

#### Scenario: Real-Time + Historical Data Processing
**Example**: fraud detection, IoT analytics, recommendation system

**Choose**:
- **Kappa** if streaming accuracy is sufficient and simpler ops preferred
- **Lambda** if exact historical reprocessing with batch accuracy required

---

#### Scenario: Full Audit Trail, Complex Domain, Multiple Query Patterns
**Example**: banking transactions, healthcare records, e-commerce orders

**Choose**: CQRS + Event Sourcing
- Event store for complete history
- Multiple projections for different query patterns (reports, search, real-time view)
- Combine with Kafka for event streaming

---

#### Scenario: Multi-Tenant SaaS, Tenant Isolation, Data Residency
**Example**: SaaS platform with EU GDPR requirements

**Choose**: Cell-Based Architecture
- Each cell serves a subset of tenants or a geographic region
- EU cell in EU data centers
- Blast radius limited to one cell

---

#### Scenario: Cluster Membership, Distributed Config, Leader Election
**Example**: Kubernetes control plane, distributed database coordination

**Choose**: Leader-Follower Cluster with Raft (etcd, ZooKeeper, Consul)
- Strong consistency for critical metadata
- Automatic leader election and failover

---

#### Scenario: Decentralized, No Trust Between Parties
**Example**: blockchain, multi-organization data sharing

**Choose**: P2P with BFT Consensus (PBFT, HotStuff, Tendermint)
- No central authority
- Cryptographic verification of all participants

---

#### Scenario: Millions of Connections, Event-Driven Microservices
**Example**: real-time notification system, IoT platform

**Choose**: Event-Driven Architecture with Kafka
- Decoupled producers and consumers
- Kafka for durable event log
- Consumers scale independently

---

### Architecture Trade-Off Summary Table

| Architecture | Consistency | Write Scale | Read Scale | Availability | Complexity | Best For |
|-------------|-------------|-------------|------------|--------------|-----------|----------|
| Master-Slave | Strong (sync) or Eventual | Low | High | Medium | Low | Read-heavy, simple |
| Leader-Follower (Raft) | Linearizable | Low | Medium | High (auto-failover) | Medium | Coordination, metadata |
| Sharded Cluster | Tunable | Very High | Very High | High | High | Massive datasets |
| Active-Active Multi-Region | Eventual | High | Very High | Very High | Very High | Global, low latency everywhere |
| Active-Passive Multi-Region | Strong (primary) | Medium | Medium | High (failover) | Medium | DR, compliance |
| P2P / DHT | Eventual | High | High | Very High | High | Decentralized, no trust |
| Gossip-based | Eventual | N/A | N/A | Very High | Low | Membership, failure detection |
| Lambda | Eventual (speed) / Strong (batch) | High | High | High | Very High | Real-time + historical |
| Kappa | Eventual | High | High | High | Medium | Streaming-first analytics |
| CQRS + Event Sourcing | Eventual (reads) | High | High | High | Very High | Audit, complex domain |
| Cell-Based | Depends on cell internals | High | High | Very High | Very High | Multi-tenant, blast radius |

---

## 21. Back-of-the-Envelope Estimation

### Latency Numbers (memorize these)

| Operation | Latency | Relative |
|-----------|---------|----------|
| L1 cache hit | ~1 ns | 1x |
| L2 cache hit | ~10 ns | 10x |
| RAM read | ~100 ns | 100x |
| Read 1 MB sequentially from RAM | ~250 µs | — |
| SSD random read | ~100 µs | 100,000x |
| HDD seek | ~10 ms | 10,000,000x |
| Round trip within datacenter | ~500 µs | — |
| Round trip same region (cross-AZ) | ~1-3 ms | — |
| Round trip cross-region (US-EU) | ~75 ms | — |
| Round trip cross-region (US-APAC) | ~150 ms | — |
| DNS lookup | ~10-100 ms | — |
| TLS handshake | ~50-100 ms | — |

### Storage Units

```
1 KB = 1,000 bytes          | 1 KiB = 1,024 bytes
1 MB = 1,000,000 bytes      | 1 MiB = 1,048,576 bytes
1 GB = 10^9 bytes           | 1 GiB = 2^30 bytes
1 TB = 10^12 bytes          | 1 TiB = 2^40 bytes
1 PB = 10^15 bytes
```

### Time Units

```
1 day    = 86,400 seconds ≈ 10^5 seconds
1 month  = 2.6M  seconds ≈ 2.5 × 10^6 seconds
1 year   = 31.5M seconds ≈ 3.15 × 10^7 seconds
```

### Common Sizes

| Data | Size |
|------|------|
| ASCII character | 1 byte |
| Unicode character (UTF-8) | 1-4 bytes |
| UUID / GUID | 16 bytes |
| IP address (IPv4) | 4 bytes |
| IP address (IPv6) | 16 bytes |
| Unix timestamp (64-bit) | 8 bytes |
| Average tweet | ~300 bytes |
| Web page (HTML only) | ~50 KB |
| High-res photo (compressed) | ~3 MB |
| 4K video frame | ~8 MB |
| 1 hour HD video | ~1 GB |
| Average song (MP3) | ~4 MB |

### QPS Estimation Formula

```
QPS = DAU × requests_per_user_per_day / 86,400
Peak QPS ≈ QPS × 2 to 5 (depends on traffic pattern)

Storage per day = QPS × avg_object_size × 86,400
Bandwidth = QPS × avg_response_size
```

### Worked Example: WhatsApp Scale

```
Assumptions:
  DAU = 500M
  Messages per user per day = 40
  Average message size = 100 bytes
  Media messages = 10% (avg 500 KB each)

QPS (messages):
  = 500M × 40 / 86,400
  = 20B / 86,400
  ≈ 230,000 messages/sec

Peak QPS ≈ 230,000 × 3 = 690,000 messages/sec

Storage (text messages per day):
  = 500M × 40 × 100 bytes
  = 2 TB/day

Storage (media per day):
  = 500M × 40 × 0.1 × 500 KB
  = 1 PB/day  (hence media offloaded to S3/CDN)

Connections:
  WebSocket per active user; 500M connections = large fleet of connection servers
  Each server: ~10,000 concurrent WebSocket connections (with tuning)
  Servers needed: 500M / 10,000 = 50,000 servers for connection layer
```

---

## 22. Glossary

| Term | Definition |
|------|------------|
| **AOF** | Append-Only File — Redis persistence mechanism logging every write |
| **Availability** | % of time system is operational and serving requests |
| **Backpressure** | Signal from downstream to upstream to slow production rate |
| **Bloom Filter** | Probabilistic data structure for set membership (no false negatives, possible false positives) |
| **Byzantine Failure** | Node behaves arbitrarily or maliciously |
| **CAP Theorem** | Distributed system can only guarantee 2 of: Consistency, Availability, Partition Tolerance |
| **CRDT** | Conflict-free Replicated Data Type — data structure that merges concurrent updates automatically |
| **Circuit Breaker** | Pattern that stops calls to a failing service to allow recovery |
| **Compaction** | Merging SSTables in LSM-tree storage to reclaim space and improve read performance |
| **Consistent Hashing** | Hash ring that minimizes key remapping when nodes are added/removed |
| **CQRS** | Command Query Responsibility Segregation — separate read and write models |
| **Dead Letter Queue** | Queue holding messages that failed processing for inspection/retry |
| **Durability** | Data survives system crashes (WAL, fsync, replication) |
| **Ephemeral Node** | ZooKeeper node deleted when the creating client disconnects |
| **Error Budget** | Allowed downtime/errors = 1 - SLO target |
| **Event Sourcing** | Store state as sequence of immutable events rather than current state |
| **Failover** | Automatic switch to backup node on primary failure |
| **Fan-out** | Distributing one request or event to multiple targets |
| **FLP Impossibility** | Consensus cannot be guaranteed in async system with even one crash failure |
| **Gossip Protocol** | Information propagation via random peer selection (epidemic-style) |
| **HLC** | Hybrid Logical Clock — combines physical and logical time |
| **Hinted Handoff** | Storing data for a down node, delivering when it recovers (Dynamo) |
| **Hotspot** | Node/partition receiving disproportionate traffic |
| **Idempotency** | Operation produces same result regardless of how many times applied |
| **ISR** | In-Sync Replicas — Kafka followers caught up with leader |
| **Lamport Clock** | Logical counter establishing causal ordering of events |
| **Latency** | Time for a single request/response |
| **Leader Election** | Process by which nodes choose one leader via consensus |
| **Lease** | Time-limited lock or leadership token; expires automatically |
| **Linearizability** | Strongest consistency — ops appear instantaneous in real-time order |
| **LSM-Tree** | Log-Structured Merge-Tree — write-optimized storage (Cassandra, RocksDB) |
| **MVCC** | Multi-Version Concurrency Control — maintains multiple versions for concurrent access |
| **Offset** | Position of a message in a Kafka partition |
| **PACELC** | Extension of CAP: also considers latency vs consistency tradeoff without partitions |
| **Partition** | Network partition (nodes can't communicate) or data partition (sharding) |
| **Phi Accrual Detector** | Failure detector returning suspicion level, not binary alive/dead |
| **Projection** | Read model built by consuming a stream of events |
| **Quorum** | Minimum nodes that must agree for operation to succeed |
| **RDB** | Redis Database snapshot — periodic persistence |
| **Replication Factor** | Number of copies of data across nodes |
| **RPO** | Recovery Point Objective — max acceptable data loss |
| **RTO** | Recovery Time Objective — max acceptable downtime |
| **Saga** | Distributed transaction pattern using local transactions + compensating actions |
| **Shard** | One partition of a horizontally partitioned dataset |
| **Snapshot** | Point-in-time copy of system state used to speed up event replay |
| **SLI** | Service Level Indicator — measured metric |
| **SLO** | Service Level Objective — internal target |
| **SLA** | Service Level Agreement — external commitment with penalties |
| **SPIFFE** | Secure Production Identity Framework for Everyone — service identity standard |
| **SSTable** | Sorted String Table — immutable disk file in LSM-tree storage |
| **Throughput** | Number of operations completed per unit of time |
| **Tombstone** | Marker indicating a deleted key in LSM-tree (actual deletion happens at compaction) |
| **TTL** | Time to Live — expiry for cache entries, DNS records, locks |
| **Two-Phase Commit** | Distributed transaction protocol with prepare + commit phases |
| **Vector Clock** | Per-node counter array tracking causality between distributed events |
| **Virtual Node (vnode)** | Multiple positions on consistent hash ring per physical node for better balance |
| **WAL** | Write-Ahead Log — changes written to log before applying (durability) |
| **Watch** | ZooKeeper mechanism for one-time notification when a znode changes |
| **Watermark** | In stream processing: marker indicating all events up to time T have been received |
| **Write Amplification** | Additional writes caused by storage compaction (LSM-tree trade-off) |
| **Znode** | Node in ZooKeeper's hierarchical data namespace |

---

*Distributed Systems Reference — complete coverage of concepts, algorithms, and architecture patterns with scenario-based selection guidance. Optimized for IDE search and quick lookup.*
