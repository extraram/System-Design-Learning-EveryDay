# Design Instagram System

## 1. Clarify Requirements

### Functional Requirements

1. User signup/login
2. Upload photo/video
3. Follow/unfollow users
4. News feed
5. Like/comment/share posts
6. Stories feature
7. Reels support
8. Notifications
9. Search hashtags/users
10. Direct messaging

---

# 2. Non Functional Requirements

1. High availability
2. Massive scalability
3. Low latency
4. Fault tolerance
5. Eventual consistency
6. Media optimized architecture

---

# 3. Capacity Estimation

Suppose:

2 billion users
700 million daily active users
100 million posts/day
Billions of feed reads/day

### Traffic Nature

Read heavy system

Feed requests >> post uploads

---

# 4. Core Challenges

Instagram system design mainly focuses on:

1. Feed generation
2. Media storage
3. Reels streaming
4. Story expiration
5. Recommendation engine
6. Notification scalability

---

# 5. High Level Architecture

```text id="nuwtxu"
Clients
   |
Load Balancer
   |
API Gateway
   |
------------------------------------------------
| User | Post | Feed | Story | Reel Service |
------------------------------------------------
| Search | Notification | Chat Service |
------------------------------------------------
                   |
                Kafka
                   |
------------------------------------------------
| Feed Workers | Media Workers | ML Workers |
------------------------------------------------
                   |
------------------------------------------------
| Cache | Databases | Object Storage |
------------------------------------------------
                   |
                 CDN
```

---

# 6. Core APIs

## Upload Post

```http id="2qu6mu"
POST /post/upload
{
  user_id,
  caption,
  media
}
```

## Get Feed

```http id="80n5rf"
GET /feed/{user_id}
```

## Upload Story

```http id="0x39i3"
POST /story/upload
```

## Like Post

```http id="vzz7vt"
POST /post/like
```

---

# 7. Database Design

## User Table

```text id="ojz5o2"
User
-----
user_id
username
bio
profile_pic
followers_count
```

## Post Table

```text id="4m48yv"
Post
-----
post_id
user_id
caption
media_url
timestamp
likes_count
```

## Follow Table

```text id="8z3fxa"
Follow
-------
follower_id
followee_id
```

## Story Table

```text id="o55a0i"
Story
------
story_id
user_id
media_url
created_at
expires_at
```

---

# 8. Database Choices

## SQL Database

Use for:

1. User data
2. Relationships
3. Transactions

Examples:

1. PostgreSQL
2. MySQL

---

## NoSQL Database

Use for:

1. Feed storage
2. Post metadata
3. Activity tracking

Examples:

1. Apache Cassandra
2. MongoDB

---

# 9. Media Storage Architecture

MOST IMPORTANT for Instagram.

Photos/videos are huge.

Use Object Storage:

1. Amazon Web Services S3
2. Distributed storage system

---

# Media Upload Flow

```text id="g0eqbz"
Client Uploads Media
        |
Media Service
        |
Compression/Transcoding
        |
Object Storage
        |
CDN Distribution
```

---

# 10. CDN Usage

Very important point.

Use CDN because:

1. Faster image loading
2. Reduced latency
3. Lower backend load

Examples:

1. Cloudflare
2. Akamai Technologies

---

# 11. Feed Generation

Biggest interview discussion.

Two models:

---

# A. Fan Out On Write

When user posts:
Push post to followers’ feed.

### Pros

1. Fast feed loading
2. Better user experience

### Cons

1. Huge writes
2. Celebrity issue

---

# B. Fan Out On Read

Generate feed during request.

### Pros

1. Lower writes
2. Better for celebrities

### Cons

1. Slower reads

---

# Best Interview Answer

Use Hybrid Feed Strategy

### Normal Users

Fan Out On Write

### Celebrities

Fan Out On Read

---

# 12. Feed Ranking

Instagram feed is personalized.

Ranking factors:

1. User engagement
2. Likes/comments
3. Watch time
4. Relationship strength
5. Recency
6. Relevance

---

# 13. Recommendation Engine

Used for:

1. Explore page
2. Suggested reels
3. Suggested users

Pipeline:

```text id="n3z2iw"
User Activity
      |
Feature Extraction
      |
ML Model
      |
Recommended Content
```

Possible frameworks:

1. TensorFlow
2. PyTorch

---

# 14. Story System Design

Stories disappear after 24 hours.

### Approach

1. Store expiration timestamp
2. Background cleanup workers delete expired stories

```text id="4h0nwl"
expires_at = created_at + 24h
```

---

# 15. Reels Architecture

Reels require video streaming optimization.

### Pipeline

```text id="ll5m3r"
Upload Video
     |
Transcoding
     |
Multiple Resolutions Generated
     |
CDN Delivery
```

Use adaptive streaming.

---

# 16. Caching

Use:

Redis

Cache:

1. Feed
2. Hot posts
3. User profiles
4. Stories
5. Followers list

---

# 17. Event Driven Architecture

Use:

Apache Kafka

Events:

1. Post created
2. Like added
3. Notification triggered
4. Feed updated

Benefits:

1. Async processing
2. Loose coupling
3. Scalability

---

# 18. Notification Service

For:

1. Likes
2. Comments
3. Follows
4. Messages

Flow:

```text id="5c12ew"
Action Event
    |
Kafka
    |
Notification Workers
    |
Push Notification
```

---

# 19. Search System

Use:

Elasticsearch

For:

1. Hashtags
2. Users
3. Captions
4. Explore search

---

# 20. Direct Messaging

Separate microservice.

Requirements:

1. Real time messaging
2. Delivery acknowledgment
3. Online status

Use:

1. WebSockets
2. Persistent connections

---

# 21. Scaling Strategy

## Horizontal Scaling

Add more:

1. Feed servers
2. Media servers
3. Cache nodes

---

## Database Sharding

Shard by:

```text id="i6p8u2"
hash(user_id)
```

---

# 22. Reliability

1. Multi region deployment
2. Replication
3. Backup strategy
4. Failover systems

---

# 23. Bottlenecks

## Celebrity Feed Explosion

Solution:
Hybrid feed strategy.

---

## Viral Reels Traffic

Solution:
Heavy CDN caching.

---

## Large Video Uploads

Solution:
Chunked uploads + async transcoding.

---

# 24. Final Architecture

```text id="f6t1zw"
Clients
   |
Load Balancer
   |
API Gateway
   |
------------------------------------------------
| User | Feed | Post | Story | Reel | Chat |
------------------------------------------------
| Search | Notification | Recommendation |
------------------------------------------------
                    |
                  Kafka
                    |
------------------------------------------------
| Feed Workers | ML Workers | Media Workers |
------------------------------------------------
                    |
------------------------------------------------
| Redis | Cassandra | Elasticsearch |
------------------------------------------------
                    |
             Object Storage + CDN
```

---

# 25. Interview Closing Statement

> “Instagram is a media heavy distributed system where the major challenges are scalable feed generation, media delivery, and personalized recommendations. Using hybrid feed architecture, CDN caching, event driven services, and distributed databases helps achieve low latency and scalability for billions of users.”

---

# Important Keywords To Mention

1. Hybrid feed generation
2. CDN optimization
3. Media transcoding
4. Event driven architecture
5. Distributed cache
6. Recommendation engine
7. Horizontal scaling
8. Sharding
9. Eventual consistency
10. Low latency

