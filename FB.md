# Design Meta Platforms Facebook News Feed System

## 1. Problem Statement

Design Facebook News Feed where users can:

1. Create posts
2. Follow/friend users
3. Like/comment/share posts
4. View personalized feed
5. Refresh feed in real time

Goal:

Deliver highly personalized feed with very low latency for billions of users.

---

# 2. Functional Requirements

## Core Features

1. User creates post
2. User follows/friends others
3. Generate personalized feed
4. Rank posts
5. Infinite scrolling
6. Real time updates
7. Likes/comments/shares

---

# 3. Non Functional Requirements

1. Highly scalable
2. Highly available
3. Low latency
4. Fault tolerant
5. Eventual consistency acceptable
6. Read heavy system

---

# 4. Capacity Estimation

Suppose:

2 billion users
500 million daily active users
1 billion posts/day

### Read Heavy System

Feed refresh happens much more than posting.

Read : Write ≈ 100:1

---

# 5. High Level Architecture

```text id="yrg9ly"
Clients
   |
Load Balancer
   |
API Gateway
   |
--------------------------------------------------
| User Service | Post Service | Feed Service |
--------------------------------------------------
| Ranking Service | Notification Service |
--------------------------------------------------
                 |
             Kafka
                 |
--------------------------------------------------
| Feed Workers | ML Ranking Workers |
--------------------------------------------------
                 |
--------------------------------------------------
| Cache | Databases | Search Engine |
--------------------------------------------------
```

---

# 6. Core APIs

## Create Post

```http id="d3l6k5"
POST /post
{
  user_id,
  content,
  media_url
}
```

## Get Feed

```http id="w9pl01"
GET /feed/{user_id}
```

## Like Post

```http id="rrtw5r"
POST /like
{
  user_id,
  post_id
}
```

---

# 7. Database Design

## User Table

```text id="ejf04j"
User
-----
user_id
name
profile_pic
```

## Post Table

```text id="vn2j0d"
Post
-----
post_id
user_id
content
media_url
timestamp
```

## Friend/Follow Table

```text id="x1i6vz"
Connections
-----------
user_id
friend_id
```

---

# 8. Database Choices

## Relational DB

Use:

1. User metadata
2. Relationships

Examples:

1. PostgreSQL
2. MySQL

---

## NoSQL DB

Use:

1. Feed storage
2. Posts
3. Activity logs

Examples:

1. Apache Cassandra
2. MongoDB

---

# 9. Biggest Challenge → Feed Generation

This is MOST IMPORTANT part in interview.

Two approaches:

---

# A. Push Model (Fan Out On Write)

When user posts:
Push post to followers’ feed immediately.

```text id="flvgn7"
Post Created
   |
Feed Workers
   |
Followers Timeline Updated
```

### Advantages

1. Fast feed loading
2. Low read latency

### Disadvantages

1. Heavy writes
2. Celebrity issue

Example:

Celebrity with 100M followers
1 post = 100M writes

---

# B. Pull Model (Fan Out On Read)

Feed generated during request time.

```text id="obz0iv"
User Opens Feed
   |
Fetch Friends Posts
   |
Rank Posts
   |
Return Feed
```

### Advantages

1. Cheap writes
2. Better for celebrities

### Disadvantages

1. Slow reads
2. High computation during refresh

---

# Best Interview Answer

Use Hybrid Model

### Normal Users

Use Push Model

### Celebrities

Use Pull Model

This is real world industry approach.

---

# 10. Feed Ranking System

Facebook feed is NOT chronological.

It uses ranking algorithms.

Ranking factors:

1. Likes
2. Comments
3. Shares
4. Watch time
5. User interaction history
6. Recency
7. Relevance score

---

# 11. ML Ranking Pipeline

```text id="jlwmx8"
Candidate Posts
      |
Feature Extraction
      |
ML Ranking Model
      |
Sorted Feed
      |
Returned to User
```

Possible technologies:

1. TensorFlow
2. PyTorch

---

# 12. Cache Strategy

Very important discussion point.

Use:

Redis

Cache:

1. User feed
2. Hot posts
3. User profile
4. Friend list

---

# 13. Feed Storage

Example:

```text id="q7b9yr"
feed:user123
[
 post101,
 post102,
 post103
]
```

Recent feed cached.

Older data fetched from DB.

---

# 14. Event Driven Architecture

Use message queues:

1. Apache Kafka
2. RabbitMQ

Purpose:

1. Async processing
2. Decoupled services
3. Retry mechanism
4. Scalability

---

# 15. Media Storage

Posts may contain:

1. Images
2. Videos

Use:

1. Amazon Web Services S3
2. CDN for delivery

Examples:

1. Cloudflare
2. Akamai Technologies

---

# 16. Infinite Scrolling

Use Cursor Based Pagination.

Avoid OFFSET pagination.

Example:

```http id="a67dy7"
GET /feed?cursor=post_100
```

Advantages:

1. Faster
2. Stable pagination
3. Better scalability

---

# 17. Real Time Updates

Use:

1. WebSockets
2. Long polling

For:

1. Live notifications
2. Feed refresh
3. Reactions

---

# 18. Sharding

Partition data using:

```text id="k9fclq"
hash(user_id)
```

Benefits:

1. Load distribution
2. Better scalability

---

# 19. Search System

Use:

Elasticsearch

For:

1. User search
2. Post search
3. Hashtag search

---

# 20. Reliability

## Replication

Primary replica setup.

## Multi Region Deployment

Improve availability globally.

## Backups

Periodic snapshots.

---

# 21. Bottlenecks

## Celebrity Fan Out Problem

Solution:
Use Pull Model for celebrities.

---

## Cache Misses

Solution:
Distributed caching.

---

## Ranking Latency

Solution:
Precompute rankings for active users.

---

# 22. Final Architecture

```text id="0krkpx"
Clients
   |
Load Balancer
   |
API Gateway
   |
--------------------------------------------------
| User | Post | Feed | Ranking | Notification |
--------------------------------------------------
                  |
                Kafka
                  |
--------------------------------------------------
| Feed Workers | ML Workers | Notification Workers |
--------------------------------------------------
                  |
--------------------------------------------------
| Redis | Cassandra | Elasticsearch |
--------------------------------------------------
                  |
             CDN + Object Storage
```

---

# 23. Interview Closing Statement

> “Facebook News Feed is a large scale read heavy distributed system. The key challenge is generating personalized feeds efficiently for billions of users. Using hybrid feed generation, caching, asynchronous processing, ML ranking, and distributed databases helps achieve low latency and massive scalability.”

---

# Important Keywords To Mention

1. Hybrid fan out
2. Feed ranking
3. Event driven architecture
4. Distributed cache
5. Cursor pagination
6. Eventual consistency
7. Horizontal scaling
8. ML ranking
9. Sharding
10. Low latency

