# Design Key Value Store (Like Redis or Amazon DynamoDB)

This is a very common System Design interview question because it tests:

1. Storage design
2. Scalability
3. Partitioning
4. Replication
5. Consistency
6. Distributed systems concepts

---

# 1. Problem Statement

Design a distributed Key Value Store supporting:

```text
PUT(key, value)

GET(key)

DELETE(key)
```

Example:

```text
PUT("user1", "Ramji")

GET("user1")

Output: Ramji
```

---

# 2. Functional Requirements

### APIs

```text
PUT(key, value)

GET(key)

DELETE(key)
```

### Features

1. Store arbitrary values
2. Fast reads
3. Fast writes
4. Persistent storage
5. Distributed system

---

# 3. Non Functional Requirements

1. High availability
2. Horizontal scalability
3. Low latency
4. Fault tolerance
5. Data durability

---

# 4. Capacity Estimation

Suppose:

100 Million Keys

Average Key Size = 32 Bytes

Average Value Size = 1 KB

Storage:

```text
100M × 1KB

≈ 100 GB
```

With replication:

```text
100GB × 3

≈ 300 GB
```

---

# 5. High Level Architecture

```text
Clients
    |
Load Balancer
    |
Key Value API Servers
    |
--------------------------------
| Cache | Storage Nodes |
--------------------------------
```

---

# 6. API Design

## PUT

```http
PUT /kv

{
  "key":"user123",
  "value":"Ramji"
}
```

---

## GET

```http
GET /kv/user123
```

---

## DELETE

```http
DELETE /kv/user123
```

---

# 7. Basic Database Design

Store records as:

```text
Key          Value
-------------------------
user123      Ramji
user124      Amit
user125      Mohit
```

Internally:

```text
HashMap<Key, Value>
```

Average complexity:

```text
GET     O(1)

PUT     O(1)

DELETE  O(1)
```

---

# 8. Single Server Problem

Suppose:

```text
1 Billion Keys
```

One server becomes:

1. Storage bottleneck
2. CPU bottleneck
3. Memory bottleneck

Need distribution.

---

# 9. Partitioning (Sharding)

MOST IMPORTANT INTERVIEW TOPIC.

Use:

```text
hash(key)
```

Example:

```text
hash(user123) % 4
```

Output:

```text
Node 2
```

Architecture:

```text
             Router
                |
--------------------------------
| Node1 Node2 Node3 Node4 |
--------------------------------
```

Benefits:

1. Load distribution
2. Horizontal scaling

---

# 10. Consistent Hashing

Interviewers love this.

Problem:

```text
hash(key) % N
```

Fails when nodes added/removed.

Solution:

### Consistent Hashing

```text
Hash Ring

Node A
Node B
Node C
```

Keys assigned clockwise.

Benefits:

1. Minimal data movement
2. Easy scaling
3. Better fault tolerance

---

# 11. Replication

Need fault tolerance.

### Replication Factor = 3

```text
Primary
   |
Replica1
   |
Replica2
```

Example:

```text
user123

Stored On:

Node A
Node B
Node C
```

---

# 12. Write Flow

```text
Client
   |
Coordinator Node
   |
Primary Node
   |
Replicas
```

Flow:

1. Receive write
2. Store primary copy
3. Replicate
4. Acknowledge

---

# 13. Read Flow

```text
Client
   |
Coordinator
   |
Replica Node
```

Read from nearest healthy replica.

Benefits:

1. Fast reads
2. Better scalability

---

# 14. Consistency Models

### Strong Consistency

After write:

```text
All reads see latest value
```

Pros:

1. Accurate

Cons:

1. Slower

---

### Eventual Consistency

After write:

```text
Some replicas update later
```

Pros:

1. Faster
2. Highly available

Used in:

Amazon DynamoDB

---

# 15. CAP Theorem

Must mention in interview.

System can guarantee only two:

```text
Consistency

Availability

Partition Tolerance
```

Most large KV stores choose:

```text
Availability
+
Partition Tolerance
```

---

# 16. Caching Layer

Use:

Redis

Architecture:

```text
Client
  |
Redis Cache
  |
Storage Nodes
```

Benefits:

1. Faster reads
2. Lower DB load

---

# 17. Storage Engine

### LSM Tree

Used by:

1. Apache Cassandra
2. RocksDB

Write Flow:

```text
Write
  |
MemTable
  |
SSTables
```

Advantages:

1. Fast writes
2. Sequential disk writes

---

# 18. Failure Handling

Suppose:

```text
Node B Crashes
```

Solution:

1. Read from replicas
2. Rebuild node
3. Re replicate data

---

# 19. Gossip Protocol

Used for node discovery.

Nodes exchange:

```text
Health Status
Membership
```

Used in:

Apache Cassandra

---

# 20. Compaction

LSM trees create multiple files.

Compaction:

```text
Merge files
Remove duplicates
Remove deleted entries
```

Benefits:

1. Better read performance
2. Less storage

---

# 21. Monitoring

Track:

1. Read latency
2. Write latency
3. Node failures
4. Replication lag
5. Storage usage

---

# 22. Final Architecture

```text
Clients
    |
Load Balancer
    |
Coordinator Nodes
    |
------------------------------------
| Consistent Hash Ring             |
------------------------------------
| Node1 | Node2 | Node3 | Node4 |
------------------------------------
        |
------------------------------------
| Replication Layer               |
------------------------------------
        |
------------------------------------
| SSD Storage + LSM Trees         |
------------------------------------
        |
Redis Cache
```

---

# 23. Interview Deep Dive Topics

If interviewer asks advanced questions, discuss:

### How to handle hot keys?

Solution:

1. Key replication
2. Local caching
3. Request throttling

---

### How to scale?

Solution:

1. Add nodes
2. Consistent hashing
3. Auto rebalancing

---

### How to avoid data loss?

Solution:

1. Replication
2. WAL (Write Ahead Log)
3. Snapshots

---

# 24. Interview Closing Statement

> "A distributed Key Value Store is built using consistent hashing for partitioning, replication for fault tolerance, LSM trees for efficient storage, caching for low latency, and eventual consistency for high availability. The design should scale horizontally while maintaining durability and fault tolerance."

---

# Keywords That Impress Interviewers

1. Consistent Hashing
2. Replication Factor
3. CAP Theorem
4. Eventual Consistency
5. Quorum Reads/Writes
6. LSM Tree
7. SSTable
8. Gossip Protocol
9. Write Ahead Log
10. Horizontal Scaling
11. Data Rebalancing
12. Fault Tolerance
13. Partition Tolerance
14. Coordinator Node
15. Hot Key Problem
