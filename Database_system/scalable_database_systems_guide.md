# Designing Scalable, Advanced, Fast & Secure Database Systems
### A Comprehensive Reference for Engineers, Architects & Researchers

> **Context:** This guide is written with high-performance, mission-critical applications in mind — including advanced research systems such as anti-gravity propulsion platforms — where data integrity, sub-millisecond latency, fault tolerance, and extreme security are non-negotiable.

---

## Table of Contents

1. [Foundational Principles](#1-foundational-principles)
2. [Database Architecture Patterns](#2-database-architecture-patterns)
3. [Data Modeling & Schema Design](#3-data-modeling--schema-design)
4. [Indexing Strategies](#4-indexing-strategies)
5. [Query Optimization](#5-query-optimization)
6. [Scalability Techniques](#6-scalability-techniques)
7. [Replication & High Availability](#7-replication--high-availability)
8. [Distributed Database Systems](#8-distributed-database-systems)
9. [Caching Layers](#9-caching-layers)
10. [Storage Engines & I/O Optimization](#10-storage-engines--io-optimization)
11. [Transactions & Concurrency Control](#11-transactions--concurrency-control)
12. [Security: Authentication & Authorization](#12-security-authentication--authorization)
13. [Encryption: At Rest & In Transit](#13-encryption-at-rest--in-transit)
14. [Auditing, Logging & Compliance](#14-auditing-logging--compliance)
15. [Backup, Recovery & Disaster Planning](#15-backup-recovery--disaster-planning)
16. [Monitoring & Observability](#16-monitoring--observability)
17. [Database for Time-Series & Sensor Data](#17-database-for-time-series--sensor-data)
18. [Graph Databases & Relationship Modeling](#18-graph-databases--relationship-modeling)
19. [Vector & Embedding Databases](#19-vector--embedding-databases)
20. [Database DevOps & CI/CD](#20-database-devops--cicd)
21. [Multi-Tenancy Design](#21-multi-tenancy-design)
22. [Edge & Embedded Databases](#22-edge--embedded-databases)
23. [Advanced Topics](#23-advanced-topics)
24. [Anti-Gravity Research: Domain-Specific Design](#24-anti-gravity-research-domain-specific-design)
25. [Quick Reference Checklists](#25-quick-reference-checklists)

---

## 1. Foundational Principles

### CAP Theorem
Every distributed database must choose two out of three guarantees:

| Property | Description |
|---|---|
| **Consistency** | Every read receives the most recent write |
| **Availability** | Every request receives a response (not necessarily the latest data) |
| **Partition Tolerance** | System continues operating despite network partitions |

**PACELC Extension:** Also consider Latency vs Consistency trade-offs even when no partition exists.

### ACID Properties
| Property | Meaning |
|---|---|
| **Atomicity** | Transactions are all-or-nothing |
| **Consistency** | DB moves from one valid state to another |
| **Isolation** | Concurrent transactions don't interfere |
| **Durability** | Committed data survives failures |

### BASE Properties (for distributed/NoSQL)
- **Basically Available** — System is available most of the time
- **Soft State** — State may change even without input
- **Eventually Consistent** — System will eventually converge to consistency

### Data Normalization vs Denormalization

**Normal Forms (relational):**
- **1NF** — Atomic columns, no repeating groups
- **2NF** — No partial dependencies on composite keys
- **3NF** — No transitive dependencies
- **BCNF** — Every determinant is a candidate key
- **4NF / 5NF** — Handle multi-valued and join dependencies

**When to Denormalize:**
- Read-heavy workloads
- Pre-aggregated analytics tables
- Materialized views for dashboards
- Latency-critical lookup tables

---

## 2. Database Architecture Patterns

### Monolithic Database
- Single instance handles all reads and writes
- Simple to operate; becomes a bottleneck at scale
- Use for early-stage or low-traffic systems

### CQRS (Command Query Responsibility Segregation)
- Separate write models (commands) from read models (queries)
- Write DB is normalized and consistent; read DB is denormalized and fast
- Use event sourcing to synchronize both sides

```
[Client] ──► [Command API] ──► [Write DB (PostgreSQL)]
                                      │
                               [Event Bus / Kafka]
                                      │
[Client] ◄── [Query API]  ◄── [Read DB (Elasticsearch/Redis)]
```

### Event Sourcing
- Store events rather than current state
- Reconstruct state by replaying event log
- Benefits: full audit trail, time-travel queries, event replay for bug reproduction

### Lambda Architecture
- **Batch Layer** — Processes historical data (e.g., Hadoop/Spark)
- **Speed Layer** — Processes real-time streams (e.g., Flink/Kafka Streams)
- **Serving Layer** — Merges batch and speed views for queries

### Kappa Architecture
- Simplified Lambda; treats everything as a stream
- Single processing path (Kafka + Flink)
- Reprocessing history done by replaying event log from offset 0

### Polyglot Persistence
Use multiple database types each optimized for its workload:

| Data Type | Best Database |
|---|---|
| Transactional records | PostgreSQL, MySQL |
| Key-Value lookups | Redis, DynamoDB |
| Document storage | MongoDB, Couchbase |
| Graph relationships | Neo4j, Amazon Neptune |
| Time-series sensor data | InfluxDB, TimescaleDB |
| Full-text search | Elasticsearch, OpenSearch |
| Analytical queries | ClickHouse, Snowflake, BigQuery |
| Vector/embedding search | Pinecone, Weaviate, pgvector |

---

## 3. Data Modeling & Schema Design

### Relational (SQL) Design Best Practices

```sql
-- Use surrogate keys, not natural keys
CREATE TABLE experiments (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    code        VARCHAR(50) UNIQUE NOT NULL,        -- natural key as unique constraint
    name        TEXT NOT NULL,
    status      experiment_status NOT NULL,          -- ENUM type
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at  TIMESTAMPTZ                          -- soft delete
);

-- Immutable audit table (append-only)
CREATE TABLE experiment_events (
    id              BIGSERIAL PRIMARY KEY,
    experiment_id   UUID NOT NULL REFERENCES experiments(id),
    event_type      TEXT NOT NULL,
    payload         JSONB NOT NULL,
    occurred_at     TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**Key rules:**
- Always use `UUID` or `BIGSERIAL` primary keys, never user-visible business IDs
- Store timestamps as `TIMESTAMPTZ` (with timezone) always
- Use `ENUM` or lookup tables for finite states; never raw strings for status
- Implement soft deletes (`deleted_at`) on sensitive data
- Use `JSONB` (PostgreSQL) for flexible/schemaless nested data within relational systems

### Document Model Design (MongoDB)

```json
{
  "_id": "expr_7a2f9b",
  "metadata": {
    "name": "Gravitational Field Test Alpha",
    "status": "active",
    "tags": ["anti-gravity", "phase-1", "classified"]
  },
  "readings": [
    { "timestamp": "2024-01-01T00:00:00Z", "g_force": 0.0023, "altitude_m": 15.4 },
    { "timestamp": "2024-01-01T00:01:00Z", "g_force": 0.0041, "altitude_m": 16.1 }
  ],
  "equipment": {
    "sensor_id": "sens_0x9A",
    "calibration_date": "2023-12-01"
  }
}
```

**Guidelines:**
- Embed frequently co-accessed data (denormalize aggressively)
- Reference large or rarely-accessed sub-documents
- Design around access patterns, not entities

### Schema Evolution Strategies
- **Expand/Contract Pattern** — Add new column → migrate data → remove old column
- **Blue/Green Schema Migrations** — Run two schema versions simultaneously
- **Backward-compatible changes** — Adding nullable columns, adding tables, adding indexes
- **Breaking changes** — Renaming columns (always add + copy + drop)
- Use migration tools: Flyway, Liquibase, Alembic, golang-migrate

---

## 4. Indexing Strategies

### B-Tree Indexes (Default)
Best for: equality and range queries on sortable data

```sql
CREATE INDEX idx_experiments_status ON experiments(status);
CREATE INDEX idx_readings_timestamp ON sensor_readings(experiment_id, recorded_at DESC);
```

### Hash Indexes
Best for: exact equality only (O(1) lookup), not ranges

```sql
CREATE INDEX idx_sessions_token ON sessions USING HASH (token);
```

### Composite Indexes
Order matters — most selective column first, then direction matches query

```sql
-- Query: WHERE experiment_id = ? AND recorded_at > ? ORDER BY recorded_at DESC
CREATE INDEX idx_readings_composite ON sensor_readings(experiment_id, recorded_at DESC);
```

### Partial Indexes
Index only a subset of rows — saves space and improves speed

```sql
-- Only index active experiments
CREATE INDEX idx_active_experiments ON experiments(created_at)
WHERE status = 'active' AND deleted_at IS NULL;
```

### Covering Indexes (Index-Only Scans)
Include all columns needed by a query so the engine never touches the table

```sql
CREATE INDEX idx_experiment_cover
ON experiments(status, created_at)
INCLUDE (id, name);
```

### Expression / Functional Indexes

```sql
CREATE INDEX idx_email_lower ON users(LOWER(email));
CREATE INDEX idx_payload_field ON events((payload->>'event_type'));  -- JSONB
```

### Full-Text Indexes

```sql
-- PostgreSQL
CREATE INDEX idx_fts ON experiments USING GIN(to_tsvector('english', name || ' ' || description));

-- Query
SELECT * FROM experiments WHERE to_tsvector('english', name) @@ to_tsquery('gravitat:*');
```

### GiST / SP-GiST Indexes
For geometric, range, and spatial data

```sql
CREATE INDEX idx_ranges ON schedules USING GIST(during);  -- tsrange type
```

### BRIN Indexes
Extremely compact for naturally-ordered large tables (e.g., log tables, time-series)

```sql
CREATE INDEX idx_logs_brin ON system_logs USING BRIN(logged_at);
```

### Index Maintenance Rules
- Remove unused indexes (`pg_stat_user_indexes`)
- Run `ANALYZE` after bulk loads
- Use `REINDEX CONCURRENTLY` on bloated indexes
- Never index low-cardinality columns alone (e.g., boolean, gender)

---

## 5. Query Optimization

### EXPLAIN ANALYZE Workflow

```sql
EXPLAIN (ANALYZE, BUFFERS, FORMAT TEXT)
SELECT e.id, e.name, COUNT(r.id) as reading_count
FROM experiments e
JOIN sensor_readings r ON r.experiment_id = e.id
WHERE e.status = 'active'
GROUP BY e.id, e.name;
```

Watch for: **Seq Scan** on large tables, high **actual rows vs estimated rows**, **Sort** spills to disk

### Common Optimization Techniques

**Avoid N+1 queries:** Use JOINs or batch fetches instead of per-row queries

```sql
-- BAD: N+1 — one query per experiment
SELECT * FROM sensor_readings WHERE experiment_id = ?;  -- repeated N times

-- GOOD: one query
SELECT e.*, r.*
FROM experiments e
JOIN sensor_readings r ON r.experiment_id = e.id
WHERE e.id = ANY(ARRAY['id1','id2','id3']);
```

**Pagination:** Use keyset (cursor) pagination instead of OFFSET for large datasets

```sql
-- BAD: slow at large offsets
SELECT * FROM readings ORDER BY id LIMIT 100 OFFSET 50000;

-- GOOD: keyset pagination
SELECT * FROM readings WHERE id > :last_seen_id ORDER BY id LIMIT 100;
```

**CTEs (Common Table Expressions):**

```sql
WITH active_experiments AS (
    SELECT id, name FROM experiments WHERE status = 'active'
),
latest_readings AS (
    SELECT DISTINCT ON (experiment_id) *
    FROM sensor_readings
    ORDER BY experiment_id, recorded_at DESC
)
SELECT ae.name, lr.g_force
FROM active_experiments ae
JOIN latest_readings lr ON lr.experiment_id = ae.id;
```

**Window Functions:**

```sql
SELECT
    experiment_id,
    recorded_at,
    g_force,
    AVG(g_force) OVER (PARTITION BY experiment_id ORDER BY recorded_at
                       ROWS BETWEEN 9 PRECEDING AND CURRENT ROW) AS moving_avg_10
FROM sensor_readings;
```

### Query Plan Hints (PostgreSQL)
Use `pg_hint_plan` extension when planner makes wrong choices:

```sql
/*+ SeqScan(experiments) IndexScan(sensor_readings idx_readings_composite) */
SELECT ...
```

### Prepared Statements
Reduce parse/plan overhead for repeated queries

```sql
PREPARE get_experiment(UUID) AS
    SELECT * FROM experiments WHERE id = $1 AND deleted_at IS NULL;

EXECUTE get_experiment('abc-123');
```

---

## 6. Scalability Techniques

### Vertical Scaling
- Add CPU, RAM, faster NVMe SSDs
- Limited ceiling; appropriate for early growth
- Use for write-heavy single-node databases

### Horizontal Scaling Patterns

#### Read Replicas
- Offload read queries to replicas (async replication)
- Suitable for read-heavy workloads (reporting, analytics)
- Replication lag is the key trade-off

#### Sharding (Horizontal Partitioning)
Split data across multiple independent nodes by a shard key

| Sharding Strategy | Description | Trade-off |
|---|---|---|
| **Range sharding** | Rows split by key range | Hot spots if key is sequential |
| **Hash sharding** | Rows distributed by hash(key) | Even distribution; no range queries |
| **Directory sharding** | Lookup table maps key → shard | Flexible; lookup table bottleneck |
| **Geo sharding** | Split by region/geography | Low latency; cross-region queries expensive |

**Shard key selection rules:**
- High cardinality
- Even distribution of writes
- Co-located with common query patterns
- Never use auto-increment integers (hot spot)

#### Table Partitioning (within one node)

```sql
-- Range partitioning by time (PostgreSQL)
CREATE TABLE sensor_readings (
    id          BIGSERIAL,
    experiment_id UUID NOT NULL,
    recorded_at TIMESTAMPTZ NOT NULL,
    g_force     DOUBLE PRECISION
) PARTITION BY RANGE (recorded_at);

CREATE TABLE sensor_readings_2024_01 PARTITION OF sensor_readings
    FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');

CREATE TABLE sensor_readings_2024_02 PARTITION OF sensor_readings
    FOR VALUES FROM ('2024-02-01') TO ('2024-03-01');
```

Benefits: partition pruning (queries scan fewer partitions), easy archival/dropping old data

#### Connection Pooling
Database connections are expensive — always pool them

| Pooler | Mode | Use Case |
|---|---|---|
| **PgBouncer** | Transaction / Session | PostgreSQL production standard |
| **pgpool-II** | Session + load balancing | HA + read load balancing |
| **HikariCP** | JVM connection pool | Java/Kotlin apps |
| **asyncpg** / **psycopg3** | Async native | Python async apps |

```ini
# pgbouncer.ini (transaction mode — most scalable)
[databases]
mydb = host=127.0.0.1 port=5432 dbname=mydb

[pgbouncer]
pool_mode = transaction
max_client_conn = 10000
default_pool_size = 100
```

---

## 7. Replication & High Availability

### Synchronous vs Asynchronous Replication

| Mode | Consistency | Latency | Use Case |
|---|---|---|---|
| **Synchronous** | Strong | Higher | Financial, safety-critical |
| **Asynchronous** | Eventual | Lower | Analytics replicas, geo-replication |
| **Semi-synchronous** | Tunable | Tunable | Balance |

### PostgreSQL Streaming Replication

```bash
# Primary postgresql.conf
wal_level = replica
max_wal_senders = 10
synchronous_standby_names = 'standby1'

# Replica recovery.conf
primary_conninfo = 'host=primary port=5432 user=replicator password=...'
restore_command = 'cp /archive/%f %p'
```

### Patroni (PostgreSQL HA Cluster)
- Automatic leader election via etcd/ZooKeeper/Consul
- Automatic failover in < 30 seconds
- REST API for cluster management

```yaml
# patroni.yml
name: pg-node-1
restapi:
  listen: 0.0.0.0:8008
etcd:
  hosts: etcd1:2379,etcd2:2379
postgresql:
  listen: 0.0.0.0:5432
  data_dir: /data/postgres
  parameters:
    max_connections: 500
    shared_buffers: 4GB
    wal_level: replica
```

### MySQL/MariaDB Group Replication
- Multi-primary or single-primary modes
- Paxos-based distributed consensus
- Integrated with MySQL Router for transparent failover

### MongoDB Replica Sets
- Automatic primary election
- Reads can be directed to secondaries
- Configurable write concern levels: `w:1`, `w:majority`, `w:all`

---

## 8. Distributed Database Systems

### Consensus Algorithms

**Raft:**
- Leader election, log replication
- Used by etcd, CockroachDB, TiKV
- Easier to understand than Paxos; production-proven

**Paxos:**
- Classic consensus algorithm
- Multi-Paxos used in Chubby, Spanner

**Viewstamped Replication:**
- Predecessor to Raft; used in some systems

### Distributed SQL Databases

| System | Architecture | Consistency |
|---|---|---|
| **CockroachDB** | Raft + MVCC + geo-partitioning | Serializable |
| **Google Spanner** | TrueTime + Paxos | Linearizable |
| **YugabyteDB** | Raft + DocDB | Serializable |
| **TiDB** | Raft + TiKV + TiFlash | Snapshot isolation |

### NoSQL Distributed Systems

**Amazon DynamoDB:**
- Single-digit millisecond latency at any scale
- Partition key + sort key model
- DynamoDB Streams for CDC (Change Data Capture)
- Global Tables for multi-region active-active

**Apache Cassandra:**
- Wide-column store, masterless ring topology
- Tunable consistency (ONE, QUORUM, ALL)
- Best for write-heavy, append-mostly workloads

```cql
-- Cassandra: design around query patterns
CREATE TABLE sensor_readings_by_experiment (
    experiment_id UUID,
    recorded_at   TIMESTAMP,
    g_force       DOUBLE,
    altitude_m    DOUBLE,
    PRIMARY KEY (experiment_id, recorded_at)
) WITH CLUSTERING ORDER BY (recorded_at DESC)
  AND compaction = {'class': 'TimeWindowCompactionStrategy',
                    'compaction_window_size': '1', 'compaction_window_unit': 'DAYS'};
```

### Two-Phase Commit (2PC)
Used in distributed transactions across multiple nodes:
1. **Prepare Phase** — Coordinator asks all participants to prepare
2. **Commit Phase** — If all prepared, coordinator sends commit; otherwise abort

Drawback: blocking protocol; vulnerable to coordinator failure — use Saga pattern instead for long-running transactions.

### Saga Pattern
Break distributed transactions into a sequence of local transactions with compensating actions

```
[Create Experiment] → [Allocate Equipment] → [Assign Personnel]
         ↑                    ↑                      ↑
   [Cancel Exp]       [Deallocate Equip]      [Unassign Staff]  ← compensating
```

---

## 9. Caching Layers

### Cache Strategies

| Strategy | Description | Use Case |
|---|---|---|
| **Cache-Aside (Lazy)** | App checks cache, loads DB on miss | General purpose |
| **Write-Through** | Write to cache + DB simultaneously | Strong consistency |
| **Write-Behind (Async)** | Write to cache, flush to DB async | High write throughput |
| **Read-Through** | Cache sits in front; loads DB on miss | Transparent caching |
| **Refresh-Ahead** | Pre-populate cache before expiry | Predictable access patterns |

### Redis Best Practices

```python
import redis
import json
from datetime import timedelta

r = redis.Redis(host='redis', port=6379, decode_responses=True,
                socket_timeout=0.1, socket_connect_timeout=0.1)

def get_experiment(experiment_id: str) -> dict:
    cache_key = f"experiment:{experiment_id}"
    
    # Cache-aside pattern
    cached = r.get(cache_key)
    if cached:
        return json.loads(cached)
    
    data = db.query("SELECT * FROM experiments WHERE id = %s", experiment_id)
    r.setex(cache_key, timedelta(minutes=15), json.dumps(data))
    return data
```

**Redis data structures for specific use cases:**

```bash
# Sorted set — real-time leaderboard / priority queue
ZADD experiment_queue 1700000000 "exp_001"   # timestamp as score
ZRANGEBYSCORE experiment_queue 0 +inf LIMIT 0 10

# HyperLogLog — approximate count of unique values (ultra memory-efficient)
PFADD active_users:2024-01-01 "user_001" "user_002"
PFCOUNT active_users:2024-01-01

# Pub/Sub — real-time event streaming
SUBSCRIBE channel:experiment_alerts
PUBLISH channel:experiment_alerts '{"id":"exp_001","alert":"anomaly_detected"}'

# Streams — durable message log (Redis 5+)
XADD sensor_stream '*' experiment_id exp_001 g_force 0.0023
XREAD COUNT 10 STREAMS sensor_stream 0
```

**Redis Cluster configuration:**
- Minimum 6 nodes (3 primary + 3 replica) for production
- Hash slots: 16,384 slots distributed across primaries
- Use Sentinel for single-primary HA (simpler than Cluster)

### Cache Invalidation Strategies
- **TTL-based** — Simple; tolerate stale data for TTL window
- **Event-driven** — Invalidate on write via CDC or pub/sub
- **Version tagging** — `cache_key:v{version_number}`
- **Write-through** — Always update cache on write

---

## 10. Storage Engines & I/O Optimization

### Storage Engine Internals

**B-Tree Storage (PostgreSQL heap + B-tree indexes):**
- Pages are 8KB blocks
- MVCC: multiple versions of rows stored
- VACUUM reclaims dead tuples

**LSM-Tree (Log-Structured Merge-Tree):**
- Used by RocksDB, Cassandra, LevelDB
- Writes go to in-memory memtable → flushed to immutable SSTables
- Compaction merges SSTables, removes tombstones
- Excellent write throughput; slightly higher read amplification

**Column Storage (Parquet, ClickHouse MergeTree):**
- Store data column-by-column
- Excellent compression ratios (same-type data)
- Fast analytical aggregations (only read relevant columns)
- Poor for point lookups or row-level updates

### PostgreSQL Tuning Parameters

```ini
# Memory (rule of thumb for 64GB RAM server)
shared_buffers = 16GB              # 25% of RAM
effective_cache_size = 48GB        # OS cache estimate (75% of RAM)
work_mem = 64MB                    # Per sort/hash operation
maintenance_work_mem = 2GB         # For VACUUM, CREATE INDEX

# WAL (Write-Ahead Log)
wal_buffers = 64MB
checkpoint_completion_target = 0.9
min_wal_size = 1GB
max_wal_size = 8GB

# Parallelism
max_worker_processes = 16
max_parallel_workers = 8
max_parallel_workers_per_gather = 4

# Connections
max_connections = 200              # Use connection pooler; keep this low

# Query planner
random_page_cost = 1.1             # Set to 1.1 for SSD (default 4.0 for HDD)
effective_io_concurrency = 200     # SSD: 200, HDD: 2
```

### Disk I/O Best Practices
- Use NVMe SSDs for primary storage (WAL + data)
- Separate WAL directory onto its own NVMe device
- Use RAID 10 for both redundancy and read performance
- Enable `noatime` and `nodiratime` in `/etc/fstab`
- Use XFS or ext4 (avoid NFS for latency-critical data)
- Enable filesystem-level write ordering for WAL safety

### Write-Ahead Log (WAL) Optimization

```ini
# Synchronous commit levels (PostgreSQL)
synchronous_commit = on          # Safest — waits for WAL flush
synchronous_commit = remote_write # Waits for standby to receive (not flush)
synchronous_commit = off         # Fastest — risk of last ~32ms of data on crash
```

---

## 11. Transactions & Concurrency Control

### Isolation Levels

| Level | Dirty Read | Non-Repeatable Read | Phantom Read | Use |
|---|---|---|---|---|
| **Read Uncommitted** | Possible | Possible | Possible | Almost never |
| **Read Committed** | No | Possible | Possible | Default (most DBs) |
| **Repeatable Read** | No | No | Possible | MySQL InnoDB default |
| **Serializable** | No | No | No | Financial, critical |

```sql
BEGIN TRANSACTION ISOLATION LEVEL SERIALIZABLE;
-- Operations guaranteed to be equivalent to some serial execution
COMMIT;
```

### MVCC (Multi-Version Concurrency Control)
- Readers don't block writers; writers don't block readers
- Each transaction sees a consistent snapshot
- PostgreSQL uses `xmin`/`xmax` system columns to track row visibility
- Enables time-travel queries and snapshot isolation

### Optimistic vs Pessimistic Locking

**Pessimistic Locking:**
```sql
-- Lock rows for update (blocks other writers)
SELECT * FROM experiments WHERE id = $1 FOR UPDATE;
SELECT * FROM experiments WHERE id = $1 FOR UPDATE SKIP LOCKED;  -- queue workers
```

**Optimistic Locking (version-based):**
```sql
-- Add version column
UPDATE experiments
SET status = 'complete', version = version + 1
WHERE id = $1 AND version = $2;   -- fails if another writer updated first
-- If 0 rows affected → conflict detected → retry
```

### Deadlock Prevention
- Always acquire locks in consistent global order
- Keep transactions short — never hold locks across user interactions
- Use `NOWAIT` or lock timeouts
- Use `SELECT ... FOR UPDATE SKIP LOCKED` for job queues

```sql
-- Safe job queue pattern (no deadlocks)
WITH next_job AS (
    SELECT id FROM job_queue
    WHERE status = 'pending'
    ORDER BY priority DESC, created_at ASC
    LIMIT 1
    FOR UPDATE SKIP LOCKED
)
UPDATE job_queue SET status = 'processing', started_at = NOW()
FROM next_job
WHERE job_queue.id = next_job.id
RETURNING *;
```

### Distributed Transactions (Cross-Service)
- Avoid 2PC in microservices — use Saga pattern (see Section 8)
- Use outbox pattern for atomic DB write + event publish:

```sql
-- Outbox table: written in same transaction as business data
CREATE TABLE outbox_events (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    aggregate_id UUID NOT NULL,
    event_type  TEXT NOT NULL,
    payload     JSONB NOT NULL,
    created_at  TIMESTAMPTZ DEFAULT NOW(),
    published_at TIMESTAMPTZ
);
```

---

## 12. Security: Authentication & Authorization

### Authentication Mechanisms

**Password-based (PostgreSQL HBA):**
```ini
# pg_hba.conf
# TYPE  DATABASE  USER      ADDRESS     METHOD
local   all       postgres              peer
host    mydb      app_user  10.0.0.0/8  scram-sha-256
hostssl mydb      app_user  0.0.0.0/0   scram-sha-256   # TLS required
```

**Certificate-based (mutual TLS):**
```ini
hostssl all all 0.0.0.0/0 cert clientcert=verify-full
```

**IAM-based (AWS RDS):**
- No passwords; use IAM role tokens (rotate every 15 min)
- Eliminate secret management overhead

**LDAP/Active Directory Integration:**
```ini
host mydb all 0.0.0.0/0 ldap ldapserver=ldap.corp.internal ldapprefix="corp\"
```

### Role-Based Access Control (RBAC)

```sql
-- Create roles (least privilege)
CREATE ROLE read_only;
CREATE ROLE read_write;
CREATE ROLE admin_role;

GRANT SELECT ON ALL TABLES IN SCHEMA public TO read_only;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO read_write;
GRANT ALL PRIVILEGES ON DATABASE mydb TO admin_role;

-- Application users get only what they need
CREATE USER app_service WITH PASSWORD '...' IN ROLE read_write;
CREATE USER analytics_service WITH PASSWORD '...' IN ROLE read_only;

-- Row-Level Security
ALTER TABLE experiments ENABLE ROW LEVEL SECURITY;

CREATE POLICY exp_isolation ON experiments
    USING (project_id = current_setting('app.current_project_id')::uuid);
```

### Row-Level Security (RLS)
Essential for multi-tenant databases — database enforces tenant isolation

```sql
-- Each user only sees their own data
CREATE POLICY user_isolation ON sensor_readings
    FOR ALL
    USING (user_id = current_user_id())   -- custom function checks JWT claim
    WITH CHECK (user_id = current_user_id());

-- Application sets context before queries
SELECT set_config('app.user_id', $user_id, true);
```

### Column-Level Encryption (Application-Layer)

```python
from cryptography.fernet import Fernet

class EncryptedField:
    def __init__(self, key: bytes):
        self.fernet = Fernet(key)
    
    def encrypt(self, value: str) -> bytes:
        return self.fernet.encrypt(value.encode())
    
    def decrypt(self, token: bytes) -> str:
        return self.fernet.decrypt(token).decode()
```

### Secret Management
- **Never** store DB passwords in source code or environment variables in production
- Use: HashiCorp Vault, AWS Secrets Manager, Azure Key Vault
- Rotate credentials automatically (Vault dynamic secrets)
- Use service accounts with just-in-time credential provisioning

```bash
# Vault dynamic PostgreSQL credentials (rotate every 1 hour)
vault write database/roles/app-role \
    db_name=mydb \
    creation_statements="CREATE USER '{{name}}' WITH PASSWORD '{{password}}' VALID UNTIL '{{expiration}}'..." \
    default_ttl=1h max_ttl=24h
```

### SQL Injection Prevention

```python
# NEVER: string concatenation
query = f"SELECT * FROM users WHERE name = '{user_input}'"  # VULNERABLE

# ALWAYS: parameterized queries
cursor.execute("SELECT * FROM users WHERE name = %s", (user_input,))

# ALWAYS: use an ORM query builder
User.query.filter_by(name=user_input).first()  # SQLAlchemy — safe
```

---

## 13. Encryption: At Rest & In Transit

### Encryption at Rest

**Transparent Data Encryption (TDE):**
- PostgreSQL: `pgcrypto` extension + `pg_tde` (PGDG)
- MySQL: InnoDB tablespace encryption
- SQL Server: Built-in TDE
- Oracle: Advanced Security Option
- Cloud: AWS RDS encryption, Azure TDE (AES-256, automatic)

**Column-Level Encryption:**
```sql
-- pgcrypto extension
CREATE EXTENSION pgcrypto;

-- Store encrypted value
UPDATE personnel SET ssn_encrypted = pgp_sym_encrypt(ssn, 'master_key_here');

-- Query encrypted value
SELECT pgp_sym_decrypt(ssn_encrypted::bytea, 'master_key_here') AS ssn
FROM personnel WHERE id = $1;
```

**Full Disk Encryption:**
- Linux: LUKS (Linux Unified Key Setup) with AES-256-XTS
- AWS: EBS volume encryption (automatic key management via KMS)
- Physical: Hardware Security Modules (HSMs) for key storage

### Encryption in Transit

```ini
# PostgreSQL ssl configuration
ssl = on
ssl_cert_file = 'server.crt'
ssl_key_file = 'server.key'
ssl_ca_file = 'ca.crt'
ssl_min_protocol_version = 'TLSv1.3'
ssl_ciphers = 'HIGH:!aNULL:!MD5:!RC4'
```

**Certificate Rotation:**
- Automate with Let's Encrypt + cert-manager (Kubernetes)
- Or internal PKI (HashiCorp Vault PKI backend)
- Rotate every 90 days minimum; automate rotation to avoid outages

### Key Management Best Practices
- Use envelope encryption: data key (DEK) encrypted by key encryption key (KEK)
- Store KEKs in HSM or cloud KMS — never in the database itself
- Implement key versioning for rotation without re-encrypting all data
- Audit all key access (who decrypted what, when)

---

## 14. Auditing, Logging & Compliance

### Database Audit Logging

```sql
-- PostgreSQL pgaudit extension
CREATE EXTENSION pgaudit;

-- Audit all DML on sensitive tables
SET pgaudit.log = 'all';
SET pgaudit.log_catalog = off;

-- Table-level audit
SECURITY LABEL FOR pgaudit ON TABLE personnel IS 'AUDIT';
```

**Log every:** Authentication attempts (success/failure), DDL changes (schema), DML on sensitive tables, privilege escalation, connection from unusual IPs

### Audit Log Schema

```sql
CREATE TABLE audit_log (
    id              BIGSERIAL PRIMARY KEY,
    occurred_at     TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    actor_id        UUID,                     -- who performed the action
    actor_type      TEXT,                     -- 'user', 'service', 'admin'
    action          TEXT NOT NULL,            -- 'SELECT', 'INSERT', etc.
    resource_type   TEXT NOT NULL,            -- 'experiment', 'sensor_reading'
    resource_id     TEXT,
    before_state    JSONB,                    -- snapshot before change
    after_state     JSONB,                    -- snapshot after change
    ip_address      INET,
    session_id      UUID,
    success         BOOLEAN NOT NULL,
    failure_reason  TEXT
) WITH (fillfactor = 100);  -- append-only; no updates ever

-- Immutable: revoke UPDATE/DELETE from all application roles
REVOKE UPDATE, DELETE ON audit_log FROM app_write_role;
```

### Compliance Frameworks

| Standard | Key DB Requirements |
|---|---|
| **GDPR** | Right to erasure, data minimization, breach notification |
| **HIPAA** | PHI encryption, audit trails, access controls |
| **PCI-DSS** | Cardholder data encryption, quarterly access reviews |
| **SOC 2 Type II** | Continuous monitoring, access logs, change management |
| **FedRAMP** | FIPS 140-2 encryption, US-only data residency |

**GDPR Right to Erasure Implementation:**
- Pseudonymize PII via crypto-shredding (delete encryption key, data becomes unreadable)
- Maintain non-PII analytics data; remove personal identifiers

---

## 15. Backup, Recovery & Disaster Planning

### Backup Types

| Type | Description | Recovery Speed | Storage |
|---|---|---|---|
| **Full backup** | Complete copy | Fastest (restore one file) | Largest |
| **Incremental** | Changes since last backup (any) | Slowest (chain replay) | Smallest |
| **Differential** | Changes since last full | Medium | Medium |
| **WAL archiving** | Continuous WAL shipping | Point-in-time | Moderate |

### PostgreSQL Backup Strategy

```bash
# Continuous archiving (pg_basebackup + WAL)
pg_basebackup -h primary -U replicator -D /backup/base -Ft -z -P

# WAL archiving configuration
archive_mode = on
archive_command = 'aws s3 cp %p s3://db-backups/wal/%f'

# Point-in-time recovery (PITR)
restore_command = 'aws s3 cp s3://db-backups/wal/%f %p'
recovery_target_time = '2024-01-15 14:30:00 UTC'
recovery_target_action = 'promote'
```

**pgBackRest (recommended for production):**

```ini
[global]
repo1-type=s3
repo1-s3-bucket=my-db-backups
repo1-s3-region=us-east-1
repo1-cipher-type=aes-256-cbc  # Encrypt backups
repo1-retention-full=4          # Keep 4 full backups
repo1-retention-diff=14         # Keep 14 differential

[main]
pg1-path=/var/lib/postgresql/data
```

### RTO & RPO Targets

| Tier | RTO | RPO | Strategy |
|---|---|---|---|
| **Tier 0 (Critical)** | < 1 min | 0 (zero data loss) | Synchronous replica + instant failover |
| **Tier 1 (High)** | < 15 min | < 1 min | Async replica + PITR |
| **Tier 2 (Standard)** | < 4 hours | < 1 hour | Daily backup + WAL archiving |
| **Tier 3 (Low)** | < 24 hours | < 24 hours | Daily snapshot |

### Disaster Recovery Checklist
- [ ] Backups automated and tested weekly (restore drill)
- [ ] Backups stored in geographically separate region
- [ ] Backup encryption keys stored separately from backups
- [ ] PITR tested to confirm target recovery window
- [ ] Runbook documented and accessible offline
- [ ] RTO/RPO validated with last quarterly DR test

---

## 16. Monitoring & Observability

### Key Metrics to Monitor

**Latency:**
- Query p50, p95, p99 execution times
- Connection acquisition time
- Replication lag (bytes/seconds)

**Throughput:**
- Transactions per second (TPS)
- Reads/writes per second
- Cache hit ratio (target > 99%)

**Saturation:**
- Active connections vs max_connections
- CPU utilization (query workers)
- I/O wait percentage
- WAL write rate

**Errors:**
- Failed transactions / rollback rate
- Deadlock frequency
- Replication errors
- Connection refused count

### PostgreSQL Monitoring Queries

```sql
-- Long-running queries
SELECT pid, now() - query_start AS duration, state, query
FROM pg_stat_activity
WHERE state != 'idle' AND query_start < NOW() - INTERVAL '30 seconds'
ORDER BY duration DESC;

-- Table bloat (dead tuples)
SELECT relname, n_dead_tup, n_live_tup,
       round(100 * n_dead_tup::float / nullif(n_live_tup + n_dead_tup, 0), 1) AS dead_pct
FROM pg_stat_user_tables
ORDER BY n_dead_tup DESC;

-- Index usage
SELECT schemaname, tablename, indexname, idx_scan, idx_tup_read
FROM pg_stat_user_indexes
ORDER BY idx_scan ASC;   -- low scan = potentially unused

-- Cache hit ratio
SELECT sum(heap_blks_hit)::float / (sum(heap_blks_hit) + sum(heap_blks_read)) AS cache_hit_ratio
FROM pg_statio_user_tables;

-- Replication lag
SELECT client_addr, state,
       pg_wal_lsn_diff(sent_lsn, replay_lsn) AS lag_bytes
FROM pg_stat_replication;
```

### Observability Stack

```yaml
# Prometheus + Grafana + postgres_exporter
scrape_configs:
  - job_name: postgres
    static_configs:
      - targets: ['postgres-exporter:9187']

# Key dashboards:
# - pg_activity: real-time query monitor
# - Grafana dashboard ID 9628: PostgreSQL overview
# - Custom: slow query heatmap, connection pool utilization
```

### Alerting Rules (Prometheus)

```yaml
groups:
  - name: database
    rules:
      - alert: HighReplicationLag
        expr: pg_replication_lag > 300   # 5 minutes
        severity: critical
      
      - alert: LowCacheHitRatio
        expr: pg_cache_hit_ratio < 0.95
        severity: warning
      
      - alert: HighConnectionCount
        expr: pg_connections_active / pg_connections_max > 0.85
        severity: warning
      
      - alert: LongRunningQuery
        expr: pg_long_running_query_seconds > 300
        severity: warning
```

---

## 17. Database for Time-Series & Sensor Data

### Time-Series Design Principles
- **Immutable writes** — sensor data never changes; optimize for append
- **Time-ordered storage** — data naturally partitioned and indexed by time
- **Downsampling** — aggregate old data to reduce storage (1s → 1min → 1hr)
- **Retention policies** — automatically drop data older than N days
- **Compression** — time-series data compresses 10-100x with delta encoding

### TimescaleDB (PostgreSQL Extension)

```sql
-- Create hypertable (auto-partitioned by time)
CREATE TABLE sensor_readings (
    time            TIMESTAMPTZ NOT NULL,
    experiment_id   UUID NOT NULL,
    sensor_id       TEXT NOT NULL,
    g_force         DOUBLE PRECISION,
    altitude_m      DOUBLE PRECISION,
    temperature_k   DOUBLE PRECISION
);

SELECT create_hypertable('sensor_readings', 'time', chunk_time_interval => INTERVAL '1 day');

-- Native compression (90%+ compression ratio on time-series)
ALTER TABLE sensor_readings SET (
    timescaledb.compress,
    timescaledb.compress_orderby = 'time DESC',
    timescaledb.compress_segmentby = 'experiment_id, sensor_id'
);

SELECT add_compression_policy('sensor_readings', INTERVAL '7 days');

-- Continuous aggregate (materialized rolling window)
CREATE MATERIALIZED VIEW readings_hourly
WITH (timescaledb.continuous) AS
SELECT
    time_bucket('1 hour', time) AS bucket,
    experiment_id,
    AVG(g_force) AS avg_g,
    MAX(g_force) AS max_g,
    MIN(altitude_m) AS min_alt
FROM sensor_readings
GROUP BY bucket, experiment_id;

-- Refresh policy
SELECT add_continuous_aggregate_policy('readings_hourly',
    start_offset => INTERVAL '3 hours',
    end_offset => INTERVAL '1 hour',
    schedule_interval => INTERVAL '1 hour');
```

### InfluxDB (Purpose-built time-series)

```flux
// Flux query: average g-force per experiment, last 24h
from(bucket: "anti_gravity_data")
  |> range(start: -24h)
  |> filter(fn: (r) => r._measurement == "sensor_readings")
  |> filter(fn: (r) => r._field == "g_force")
  |> aggregateWindow(every: 1m, fn: mean, createEmpty: false)
  |> group(columns: ["experiment_id"])
  |> yield(name: "mean_g_force")
```

### Apache Kafka + Apache Flink (Streaming Pipeline)

```
[Sensors] → [Kafka Topics] → [Flink Stream Processing] → [TimescaleDB]
                                      │
                              [Real-time Alerts]
                              [InfluxDB metrics]
```

---

## 18. Graph Databases & Relationship Modeling

### When to Use Graph Databases
- Complex many-to-many relationships
- Traversal queries (friends-of-friends, shortest path)
- Fraud detection (connection patterns)
- Dependency graphs, knowledge graphs

### Neo4j Cypher Query Language

```cypher
// Create nodes and relationships
CREATE (e:Experiment {id: 'exp_001', name: 'Alpha Test'})
CREATE (s:Sensor {id: 'sens_001', type: 'gravimeter'})
CREATE (r:Researcher {id: 'res_001', name: 'Dr. Chen'})
CREATE (e)-[:USES]->(s)
CREATE (r)-[:LEADS]->(e)

// Find all experiments using a specific sensor type
MATCH (e:Experiment)-[:USES]->(s:Sensor)
WHERE s.type = 'gravimeter'
RETURN e.name, collect(s.id) AS sensors

// Shortest path between two nodes
MATCH path = shortestPath(
    (e1:Experiment {id: 'exp_001'})-[*]-(e2:Experiment {id: 'exp_005'})
)
RETURN path
```

### Graph Modeling for Research Networks

```cypher
// Knowledge graph for anti-gravity research
(:Paper)-[:CITES]->(:Paper)
(:Researcher)-[:AUTHORED]->(:Paper)
(:Paper)-[:DESCRIBES]->(:Phenomenon)
(:Experiment)-[:VALIDATES]->(:Phenomenon)
(:Experiment)-[:USES]->(:Equipment)
```

---

## 19. Vector & Embedding Databases

### Use Cases
- Semantic similarity search over research papers
- AI-powered anomaly detection on sensor patterns
- Natural language queries over experimental data
- Recommendation systems for related experiments

### pgvector (PostgreSQL Extension)

```sql
CREATE EXTENSION vector;

-- Store 1536-dimensional embeddings (OpenAI text-embedding-ada-002)
CREATE TABLE research_papers (
    id          UUID PRIMARY KEY,
    title       TEXT,
    abstract    TEXT,
    embedding   vector(1536)
);

-- HNSW index for approximate nearest neighbor search
CREATE INDEX ON research_papers USING hnsw (embedding vector_cosine_ops)
WITH (m = 16, ef_construction = 64);

-- Semantic similarity search
SELECT title, 1 - (embedding <=> $query_embedding) AS similarity
FROM research_papers
ORDER BY embedding <=> $query_embedding
LIMIT 10;
```

### Pinecone / Weaviate (Dedicated Vector DBs)

```python
import pinecone

pinecone.init(api_key="...", environment="us-east-1-aws")
index = pinecone.Index("research-papers")

# Upsert vectors
index.upsert([
    ("paper_001", embedding_vector, {"title": "Gravitational Anomaly Study", "year": 2024})
])

# Query
results = index.query(vector=query_embedding, top_k=10,
                      filter={"year": {"$gte": 2020}},
                      include_metadata=True)
```

---

## 20. Database DevOps & CI/CD

### Migration Management (Flyway)

```sql
-- V1__initial_schema.sql
CREATE TABLE experiments (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name TEXT NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- V2__add_status_column.sql
ALTER TABLE experiments ADD COLUMN status TEXT NOT NULL DEFAULT 'draft';
CREATE INDEX idx_experiments_status ON experiments(status);

-- V3__add_sensor_readings.sql
CREATE TABLE sensor_readings (
    id BIGSERIAL PRIMARY KEY,
    experiment_id UUID NOT NULL REFERENCES experiments(id),
    recorded_at TIMESTAMPTZ NOT NULL,
    g_force DOUBLE PRECISION
);
```

```bash
# Run migrations
flyway -url=jdbc:postgresql://localhost/mydb migrate

# Validate migration state
flyway info

# Rollback (requires undo scripts)
flyway undo
```

### CI/CD Pipeline for Database Changes

```yaml
# GitHub Actions: database CI
- name: Run migration on staging
  run: |
    flyway -url=$STAGING_DB_URL -user=$DB_USER -password=$DB_PASS migrate

- name: Run schema validation
  run: |
    pg_dump --schema-only $STAGING_DB_URL > /tmp/schema.sql
    diff expected_schema.sql /tmp/schema.sql

- name: Run integration tests
  run: pytest tests/integration/ --db-url=$STAGING_DB_URL

- name: Apply to production (manual approval gate)
  if: github.ref == 'refs/heads/main'
  environment: production  # requires manual approval
  run: flyway -url=$PROD_DB_URL migrate
```

### Database as Code
- Store all schema definitions in version control
- Treat migrations as immutable (never modify past migrations)
- Use feature flags in application code to separate deploy from migration
- Always test rollback procedures before production deploys

---

## 21. Multi-Tenancy Design

### Patterns

| Pattern | Isolation | Cost | Scalability |
|---|---|---|---|
| **Separate database per tenant** | Strongest | High | Complex |
| **Separate schema per tenant** | Strong | Medium | Moderate |
| **Shared schema + tenant_id column** | Weakest (enforced by app/RLS) | Lowest | Best |

### Shared Schema with RLS (Recommended for most)

```sql
-- Every table has tenant_id
ALTER TABLE experiments ADD COLUMN tenant_id UUID NOT NULL;
ALTER TABLE experiments ENABLE ROW LEVEL SECURITY;

-- Policy: users only see their tenant's data
CREATE POLICY tenant_isolation ON experiments
    USING (tenant_id = current_setting('app.tenant_id')::uuid);

-- Application sets tenant context per request
SELECT set_config('app.tenant_id', :tenant_id, true);
-- All subsequent queries automatically scoped to tenant
```

### Tenant Routing (Schema-per-tenant)

```python
from sqlalchemy import create_engine, event

def get_tenant_engine(tenant_id: str):
    engine = create_engine(DATABASE_URL)
    
    @event.listens_for(engine, "connect")
    def set_schema(dbapi_conn, connection_record):
        cursor = dbapi_conn.cursor()
        cursor.execute(f"SET search_path TO tenant_{tenant_id}, public")
        cursor.close()
    
    return engine
```

---

## 22. Edge & Embedded Databases

### Use Cases for Anti-Gravity Research
- Onboard sensor data collection (autonomous vehicles, drones, spacecraft)
- Offline-first field data collection
- Real-time local processing before cloud sync

### SQLite (Embedded)

```python
import sqlite3

# WAL mode for concurrent reads
conn = sqlite3.connect('/data/local_readings.db')
conn.execute("PRAGMA journal_mode=WAL")
conn.execute("PRAGMA synchronous=NORMAL")   # Faster than FULL; safe with WAL
conn.execute("PRAGMA cache_size=-65536")    # 64MB cache
conn.execute("PRAGMA temp_store=MEMORY")
```

### DuckDB (Analytical Embedded)

```python
import duckdb

# Analytical queries on Parquet files directly — no server needed
conn = duckdb.connect()
result = conn.execute("""
    SELECT experiment_id, AVG(g_force), MAX(altitude_m)
    FROM read_parquet('/data/sensor_readings_*.parquet')
    WHERE recorded_at > CURRENT_DATE - INTERVAL 7 DAYS
    GROUP BY experiment_id
    ORDER BY AVG(g_force) DESC
""").df()
```

### Edge Sync Architecture

```
[Edge Sensor DB (SQLite)] ──► [Delta Sync Queue] ──► [Central Cloud DB]
         ↑                                                     │
         └──────────── Config/Command Sync ◄──────────────────┘
```

---

## 23. Advanced Topics

### Database Proxy & Service Mesh
- **ProxySQL** — MySQL query routing, filtering, rewriting
- **pgBouncer** — PostgreSQL connection pooling
- **Envoy** — L7 proxy with mTLS for DB traffic in service mesh
- **AWS RDS Proxy** — Managed connection pooling with IAM auth

### Change Data Capture (CDC)

```python
# Debezium: stream DB changes to Kafka
# docker-compose snippet
debezium:
  image: debezium/connect:2.4
  environment:
    BOOTSTRAP_SERVERS: kafka:9092
    GROUP_ID: 1
    CONFIG_STORAGE_TOPIC: debezium_config
    OFFSET_STORAGE_TOPIC: debezium_offsets

# Register PostgreSQL connector
POST /connectors
{
  "name": "pg-connector",
  "config": {
    "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
    "database.hostname": "postgres",
    "database.dbname": "mydb",
    "table.include.list": "public.experiments,public.sensor_readings",
    "plugin.name": "pgoutput",
    "publication.name": "dbz_publication"
  }
}
```

### Materialized Views & Incremental Maintenance

```sql
-- Standard materialized view (full refresh)
CREATE MATERIALIZED VIEW experiment_summary AS
SELECT e.id, e.name, COUNT(r.id) AS reading_count, AVG(r.g_force) AS avg_g
FROM experiments e
LEFT JOIN sensor_readings r ON r.experiment_id = e.id
GROUP BY e.id, e.name
WITH DATA;

CREATE UNIQUE INDEX ON experiment_summary(id);  -- allows REFRESH CONCURRENTLY

-- Refresh without locking reads
REFRESH MATERIALIZED VIEW CONCURRENTLY experiment_summary;
```

### Foreign Data Wrappers (PostgreSQL FDW)

```sql
-- Query remote PostgreSQL, MySQL, S3, REST APIs as local tables
CREATE EXTENSION postgres_fdw;
CREATE SERVER remote_server FOREIGN DATA WRAPPER postgres_fdw
    OPTIONS (host 'analytics-db.internal', dbname 'analytics');

CREATE FOREIGN TABLE remote_experiments (
    id UUID, name TEXT, created_at TIMESTAMPTZ
) SERVER remote_server OPTIONS (schema_name 'public', table_name 'experiments');

-- Now query it like a local table
SELECT * FROM remote_experiments WHERE created_at > NOW() - INTERVAL '1 day';
```

### Logical Decoding & Streaming

```sql
-- Create logical replication slot
SELECT pg_create_logical_replication_slot('my_slot', 'pgoutput');

-- Read changes (real-time CDC without Debezium)
SELECT * FROM pg_logical_slot_get_changes('my_slot', NULL, NULL,
    'proto_version', '1', 'publication_names', 'my_publication');
```

### In-Memory Databases
- **Redis** — Primary use: cache, queues, pub/sub
- **Apache Ignite** — Distributed in-memory SQL + compute
- **VoltDB** — ACID in-memory SQL for ultra-low latency transactions
- **SingleStore (MemSQL)** — Hybrid row/column in-memory + disk

### HTAP (Hybrid Transactional/Analytical Processing)
Process both OLTP and OLAP workloads on the same platform:
- **TiDB** — Raft-based HTAP with TiFlash columnar storage
- **SingleStore** — In-memory row store + disk column store
- **Google AlloyDB** — PostgreSQL with HTAP via columnar engine

---

## 24. Anti-Gravity Research: Domain-Specific Design

This section addresses database requirements specific to anti-gravity propulsion research systems — where sensor data volume, precision, security, and real-time analytics are paramount.

### Core Data Model

```sql
-- Projects / Research Programs
CREATE TABLE research_programs (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    code            VARCHAR(20) UNIQUE NOT NULL,
    classification  TEXT NOT NULL,              -- 'PUBLIC', 'CONFIDENTIAL', 'SECRET'
    name            TEXT NOT NULL,
    description     TEXT,
    status          TEXT NOT NULL DEFAULT 'active',
    created_at      TIMESTAMPTZ DEFAULT NOW()
);

-- Experiments
CREATE TABLE experiments (
    id                  UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    program_id          UUID NOT NULL REFERENCES research_programs(id),
    code                VARCHAR(50) UNIQUE NOT NULL,
    name                TEXT NOT NULL,
    hypothesis          TEXT,
    status              TEXT NOT NULL DEFAULT 'planned',
    target_g_reduction  DOUBLE PRECISION,       -- target % gravitational reduction
    started_at          TIMESTAMPTZ,
    completed_at        TIMESTAMPTZ,
    created_at          TIMESTAMPTZ DEFAULT NOW()
);

-- Equipment / Sensors
CREATE TABLE equipment (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    serial_number   TEXT UNIQUE NOT NULL,
    model           TEXT NOT NULL,
    sensor_type     TEXT NOT NULL,  -- 'gravimeter', 'accelerometer', 'field_meter'
    precision_nm_s2 DOUBLE PRECISION,           -- precision in nm/s²
    calibrated_at   TIMESTAMPTZ,
    status          TEXT NOT NULL DEFAULT 'active'
);

-- High-frequency sensor readings (hypertable for TimescaleDB)
CREATE TABLE sensor_readings (
    time            TIMESTAMPTZ NOT NULL,
    experiment_id   UUID NOT NULL,
    equipment_id    UUID NOT NULL,
    g_force_ms2     DOUBLE PRECISION NOT NULL,  -- m/s² (full precision)
    g_reduction_pct DOUBLE PRECISION,           -- % reduction vs baseline
    altitude_m      DOUBLE PRECISION,
    field_strength  DOUBLE PRECISION,           -- Tesla
    temperature_k   DOUBLE PRECISION,
    pressure_pa     DOUBLE PRECISION,
    anomaly_flag    BOOLEAN DEFAULT FALSE
) PARTITION BY RANGE (time);

-- Personnel with clearance levels
CREATE TABLE researchers (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    employee_id     TEXT UNIQUE NOT NULL,
    name            TEXT NOT NULL,              -- store encrypted in production
    clearance_level INTEGER NOT NULL DEFAULT 1, -- 1-5 clearance tiers
    role            TEXT NOT NULL,
    active          BOOLEAN NOT NULL DEFAULT TRUE,
    last_login      TIMESTAMPTZ
);

-- Full audit trail
CREATE TABLE data_access_log (
    id              BIGSERIAL PRIMARY KEY,
    accessed_at     TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    researcher_id   UUID NOT NULL,
    resource_type   TEXT NOT NULL,
    resource_id     TEXT NOT NULL,
    action          TEXT NOT NULL,
    ip_address      INET,
    classification_accessed TEXT,
    success         BOOLEAN NOT NULL
);
```

### Security: Clearance-Based Row-Level Security

```sql
-- Enable RLS on classified data
ALTER TABLE experiments ENABLE ROW LEVEL SECURITY;
ALTER TABLE sensor_readings ENABLE ROW LEVEL SECURITY;

-- Function to get current researcher clearance
CREATE OR REPLACE FUNCTION current_clearance_level()
RETURNS INTEGER AS $$
    SELECT clearance_level FROM researchers
    WHERE id = current_setting('app.researcher_id')::uuid AND active = TRUE;
$$ LANGUAGE SQL SECURITY DEFINER;

-- Clearance-based access policy
CREATE POLICY experiment_clearance ON experiments
    FOR SELECT
    USING (
        CASE classification
            WHEN 'PUBLIC'       THEN TRUE
            WHEN 'CONFIDENTIAL' THEN current_clearance_level() >= 2
            WHEN 'SECRET'       THEN current_clearance_level() >= 4
            ELSE FALSE
        END
    );
```

### High-Frequency Data Pipeline

```
[Sensors @ 10kHz]
      │
[Edge Buffer (SQLite WAL)]   ← local, survives network loss
      │
[Kafka Topic: raw_readings]  ← durable, partitioned by experiment_id
      │
[Flink Stream Processor]     ← real-time anomaly detection, windowed aggregates
      ├──► [TimescaleDB]       ← full-resolution data, 90-day hot tier
      ├──► [InfluxDB]          ← metrics and dashboards
      └──► [S3 Parquet]        ← cold storage, indefinite retention
```

### TimescaleDB Tuning for Sensor Data

```sql
-- Chunk interval: 1 day for 10kHz data (~864M rows/day per experiment)
SELECT create_hypertable('sensor_readings', 'time',
    chunk_time_interval => INTERVAL '4 hours',   -- smaller chunks for high rate
    partitioning_column => 'experiment_id',
    number_partitions => 16);

-- Compress chunks older than 1 day
ALTER TABLE sensor_readings SET (
    timescaledb.compress,
    timescaledb.compress_orderby = 'time DESC',
    timescaledb.compress_segmentby = 'experiment_id, equipment_id'
);
SELECT add_compression_policy('sensor_readings', INTERVAL '1 day');

-- Tiered storage: move old chunks to cheaper storage
SELECT add_tiering_policy('sensor_readings', INTERVAL '30 days');

-- Continuous aggregate: 1-second rollup
CREATE MATERIALIZED VIEW readings_1s
WITH (timescaledb.continuous) AS
SELECT
    time_bucket('1 second', time) AS bucket,
    experiment_id, equipment_id,
    AVG(g_force_ms2) AS avg_g,
    STDDEV(g_force_ms2) AS stddev_g,
    MAX(g_reduction_pct) AS max_reduction,
    COUNT(*) AS sample_count
FROM sensor_readings
GROUP BY bucket, experiment_id, equipment_id;
```

### Anomaly Detection Query

```sql
-- Flag readings that deviate > 3σ from 60-second rolling mean
WITH rolling_stats AS (
    SELECT
        time, experiment_id, g_force_ms2,
        AVG(g_force_ms2) OVER w AS rolling_mean,
        STDDEV(g_force_ms2) OVER w AS rolling_stddev
    FROM sensor_readings
    WHERE time > NOW() - INTERVAL '10 minutes'
    WINDOW w AS (PARTITION BY experiment_id
                 ORDER BY time
                 RANGE BETWEEN INTERVAL '60 seconds' PRECEDING AND CURRENT ROW)
)
UPDATE sensor_readings sr
SET anomaly_flag = TRUE
FROM rolling_stats rs
WHERE sr.time = rs.time
  AND sr.experiment_id = rs.experiment_id
  AND ABS(rs.g_force_ms2 - rs.rolling_mean) > 3 * rs.rolling_stddev;
```

### Backup Strategy for Research Data

```bash
# Tier 1: WAL archiving → S3 (continuous, ~1s RPO)
archive_command = 'pgbackrest --stanza=research-db archive-push %p'

# Tier 2: Full backup nightly
pgbackrest --stanza=research-db backup --type=full

# Tier 3: Cold archive — export to immutable S3 bucket (WORM)
aws s3 sync /backup/cold/ s3://research-archive-worm/ \
    --storage-class GLACIER_IR \
    --metadata-directive COPY

# Retention: all data retained indefinitely (legal/research requirement)
# Compression: Parquet + Zstd → typical 50:1 compression on time-series
```

---

## 25. Quick Reference Checklists

### New Database Setup Checklist
- [ ] Enable SSL/TLS for all connections (`hostssl` only in pg_hba.conf)
- [ ] Configure connection pooling (PgBouncer transaction mode)
- [ ] Set up streaming replication with at least 1 standby
- [ ] Enable WAL archiving to offsite storage
- [ ] Configure automated daily backups + weekly restore test
- [ ] Enable `pgaudit` for audit logging on sensitive tables
- [ ] Create application roles with least-privilege access
- [ ] Enable Row-Level Security on all multi-tenant / classified tables
- [ ] Set `pg_stat_statements` for query performance tracking
- [ ] Deploy monitoring (prometheus + postgres_exporter + Grafana)
- [ ] Set up alerting for lag, connections, disk, long queries
- [ ] Document RTO/RPO targets and test DR procedure
- [ ] Enable PITR and verify recovery to target time works

### Performance Optimization Checklist
- [ ] All foreign keys have indexes
- [ ] All common WHERE/JOIN columns indexed
- [ ] No unused indexes (check `pg_stat_user_indexes`)
- [ ] `EXPLAIN ANALYZE` reviewed for all slow queries
- [ ] `work_mem` tuned for sort/hash operations
- [ ] `shared_buffers` set to ~25% RAM
- [ ] `random_page_cost = 1.1` for SSD storage
- [ ] `VACUUM` and `ANALYZE` running automatically
- [ ] Partition large time-based tables
- [ ] Cache hit ratio > 99% (verify via `pg_statio_user_tables`)
- [ ] Connection pool sized correctly (avoid idle connections)
- [ ] Avoid `SELECT *` — select only needed columns

### Security Hardening Checklist
- [ ] No default passwords; rotate all credentials
- [ ] `scram-sha-256` authentication (not md5)
- [ ] TLS 1.3 only; disable older protocols
- [ ] Encryption at rest enabled (TDE or filesystem)
- [ ] Encryption keys stored in HSM or cloud KMS
- [ ] Application using parameterized queries only (no string concat)
- [ ] Secrets in Vault/Secrets Manager (not env vars or code)
- [ ] Audit log enabled and alerts on suspicious access
- [ ] Network: DB accessible only from app subnets (no public IP)
- [ ] Regular access review (quarterly at minimum)
- [ ] Penetration testing annually

---

## References & Further Reading

- **PostgreSQL Documentation** — https://www.postgresql.org/docs/
- **Designing Data-Intensive Applications** — Martin Kleppmann (O'Reilly)
- **Database Internals** — Alex Petrov (O'Reilly)
- **TimescaleDB Docs** — https://docs.timescale.com
- **CockroachDB Architecture** — https://www.cockroachlabs.com/docs/stable/architecture/
- **Redis Command Reference** — https://redis.io/commands/
- **Debezium CDC** — https://debezium.io/documentation/
- **pgBackRest** — https://pgbackrest.org/user-guide.html
- **Patroni** — https://patroni.readthedocs.io/
- **NIST Database Security Guidelines** — SP 800-111, SP 800-162

---

*This document is a living reference. Review and update with each major architecture change, security audit, or technology upgrade. Last structural revision: 2025.*
