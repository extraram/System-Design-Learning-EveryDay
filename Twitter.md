# Design X (Twitter) System Design Interview Answer

## 1. Clarify Requirements

### Functional Requirements

1. User can post tweets
2. User can follow/unfollow users
3. User can view timeline/feed
4. User can like and retweet
5. User can search tweets/users
6. Real time notifications
7. Media upload support

### Non Functional Requirements

1. High availability
2. Low latency feed loading
3. Massive scalability
4. Fault tolerance
5. Eventual consistency acceptable
6. Read heavy system

---

# 2. Estimate Scale

Suppose:

1 billion users
200 million daily active users
500 million tweets/day

### Storage Estimation

If 1 tweet ≈ 300 bytes

500M × 300 bytes ≈ 150 GB/day

With media + metadata → TBs/day

### Traffic Pattern

Read heavy system

Read : Write = 100 : 1

Timeline generation is biggest challenge.

---

# 3. High Level Components

```text
Clients
  |
Load Balancer
  |
API Gateway
  |
------------------------------------------------
| User Service | Tweet Service | Feed Service |
------------------------------------------------
| Notification Service | Search Service |
------------------------------------------------
        |
 Message Queue (Kafka/RabbitMQ)
        |
------------------------------------------------
| Cache | Databases | Object Storage |
------------------------------------------------
```

---

# 4. Core APIs

## Post Tweet

```http
POST /tweet
{
  user_id,
  text,
  media_url
}
```

## Follow User

```http
POST /follow
{
  follower_id,
  followee_id
}
```

## Get Timeline

```http
GET /timeline/{user_id}
```

---

# 5. Database Design

## User Table

```text
User
-----
user_id
name
bio
followers_count
following_count
```

## Tweet Table

```text
Tweet
------
tweet_id
user_id
content
timestamp
likes_count
retweet_count
```

## Follow Table

```text
Follow
-------
follower_id
followee_id
```

---

# 6. Database Choice

## SQL

Use for:

1. User profile
2. Relationships

Examples:

1. PostgreSQL
2. MySQL

## NoSQL

Use for:

1. Tweets
2. Timeline storage
3. Massive writes

Examples:

1. Cassandra
2. MongoDB

---

# 7. Timeline Generation

This is the MOST IMPORTANT interview discussion.

Two approaches:

---

## A. Fan Out On Write

When celebrity posts:

1. Push tweet into followers' timelines immediately.

### Pros

1. Fast read
2. Low timeline latency

### Cons

1. Heavy write amplification
2. Celebrity problem

Example:

If celebrity has 100M followers
One tweet → 100M writes

---

## B. Fan Out On Read

Timeline generated during request time.

### Pros

1. Cheap writes
2. Better for celebrities

### Cons

1. Slow reads

---

# Best Interview Answer

Use Hybrid Approach

### Normal users

Use Fan Out On Write

### Celebrities

Use Fan Out On Read

This is what interviewers expect.

---

# 8. Feed Generation Flow

## User posts tweet

```text
User
  |
Tweet Service
  |
Store Tweet
  |
Publish Event to Kafka
  |
Feed Workers Consume Event
  |
Update Followers Timeline Cache
```

### Why Kafka?

Because:

1. Asynchronous processing
2. Retry support
3. Scalability
4. Event driven architecture

Use:

Apache Kafka

---

# 9. Caching Strategy

Very important in interview.

Use:

Redis

### Cache:

1. User profile
2. Hot tweets
3. Timeline
4. Followers list

### Why?

Reduce DB hits and improve latency.

---

# 10. Timeline Storage

Each user has:

```text
timeline:user123
[
 tweet1,
 tweet2,
 tweet3
]
```

Stored in Redis.

Recent tweets cached.

Older tweets fetched from DB.

---

# 11. Media Storage

Tweets may contain:

1. Images
2. Videos

Use Object Storage:

1. Amazon Web Services S3
2. CDN for fast delivery

Use:

1. Cloudflare
2. Akamai Technologies

---

# 12. Load Balancer

Use:

1. Round Robin
2. Health checks

Distribute traffic across servers.

Example:

NGINX

---

# 13. Scaling

## Horizontal Scaling

Add more:

1. Feed servers
2. Tweet servers
3. Cache nodes

---

# 14. Partitioning / Sharding

Tweets distributed by:

```text
hash(user_id)
```

This prevents single DB overload.

---

# 15. Search System

Use:

Elasticsearch

For:

1. Hashtags
2. Tweet search
3. User search

---

# 16. Notification System

Event driven architecture.

```text
Tweet Created
   |
Kafka
   |
Notification Service
   |
Push Notification
```

---

# 17. Reliability

## Replication

Master replica setup.

## Backups

Regular snapshots.

## Multi Region Deployment

Improve availability.

---

# 18. Bottlenecks

## Celebrity Problem

One celebrity tweet creates huge fan out.

### Solution

Hybrid feed generation.

---

## Hot Partitions

Some users extremely active.

### Solution

Better shard strategy.

---

# 19. Consistency

Use Eventual Consistency.

Why?

Because perfect consistency slows system.

Small delay in timeline acceptable.

---

# 20. Final Architecture

```text
Clients
   |
Load Balancer
   |
API Gateway
   |
------------------------------------------------
| Auth | User | Tweet | Feed | Search Service |
------------------------------------------------
         |
      Kafka
         |
------------------------------------------------
| Feed Workers | Notification Workers |
------------------------------------------------
         |
------------------------------------------------
| Redis Cache | Cassandra | Elasticsearch |
------------------------------------------------
         |
       CDN + S3
```

---

# 21. Interview Closing Statement

You can end interview with:

> "Twitter is a read heavy distributed system. The biggest challenge is scalable timeline generation. A hybrid fan out strategy with caching, asynchronous queues, and distributed databases helps achieve low latency and high scalability."

---

# Important Interview Keywords

Mention these confidently:

1. Scalability
2. High availability
3. Eventual consistency
4. Fan out on write
5. Fan out on read
6. Caching
7. Message queues
8. Sharding
9. CDN
10. Horizontal scaling

These words create strong interviewer impression.

