# Design Food Delivery Service Like Zomato / Swiggy

# 1. Requirements Clarification

## Functional Requirements

### Customer Features

1. Search restaurants
2. View menu
3. Place order
4. Online payment
5. Live order tracking
6. Ratings/reviews
7. Notifications

### Restaurant Features

1. Manage menu
2. Accept/reject order
3. Update preparation status

### Delivery Partner Features

1. Accept delivery request
2. Live location sharing
3. Mark delivery completed

---

# 2. Non Functional Requirements

1. High availability
2. Low latency
3. Real time tracking
4. Scalability
5. Fault tolerance
6. Eventual consistency

---

# 3. Capacity Estimation

Suppose:

100 million users
5 million orders/day
Peak traffic during lunch/dinner

### Traffic Pattern

1. Read heavy during browsing
2. Write heavy during peak ordering

---

# 4. High Level Architecture

```text id="8v8zq2"
Clients
   |
Load Balancer
   |
API Gateway
   |
--------------------------------------------------------
| User | Restaurant | Order | Payment | Delivery |
--------------------------------------------------------
| Tracking | Notification | Recommendation Service |
--------------------------------------------------------
                        |
                     Kafka
                        |
--------------------------------------------------------
| Order Workers | Delivery Workers | Tracking Workers |
--------------------------------------------------------
                        |
--------------------------------------------------------
| Cache | Databases | Search Engine | Maps System |
--------------------------------------------------------
```

---

# 5. Core APIs

## Search Restaurants

```http id="j1xg7h"
GET /restaurants?location=Delhi
```

## Place Order

```http id="x1j3tq"
POST /order
{
  user_id,
  restaurant_id,
  items,
  payment_method
}
```

## Track Order

```http id="b0q2fi"
GET /track/{order_id}
```

---

# 6. Database Design

## User Table

```text id="5ovf74"
User
-----
user_id
name
phone
address
```

## Restaurant Table

```text id="z0avv3"
Restaurant
----------
restaurant_id
name
location
rating
is_open
```

## Menu Table

```text id="w6ev0s"
Menu
----
item_id
restaurant_id
name
price
availability
```

## Order Table

```text id="zv1g89"
Order
-----
order_id
user_id
restaurant_id
delivery_partner_id
status
total_amount
created_at
```

---

# 7. Database Choice

## SQL Database

Use for:

1. Orders
2. Payments
3. Transactions

Examples:

1. PostgreSQL
2. MySQL

---

## NoSQL Database

Use for:

1. Tracking events
2. User activity
3. Recommendation data

Examples:

1. MongoDB
2. Apache Cassandra

---

# 8. Restaurant Search System

Important interview topic.

Requirements:

1. Nearby restaurants
2. Fast filtering
3. Search by cuisine/rating

Use:

Elasticsearch

Indexed fields:

1. Restaurant name
2. Cuisine
3. Geo location
4. Rating

---

# 9. Real Time Delivery Tracking

MOST IMPORTANT feature.

Delivery partner app continuously sends GPS coordinates.

```text id="pd7sh0"
Delivery App
      |
Tracking Service
      |
Redis / Kafka
      |
Customer Live Updates
```

Use:

1. WebSockets
2. Pub/Sub architecture

---

# 10. Order Workflow

```text id="g0s2g3"
Customer Places Order
        |
Order Service
        |
Payment Service
        |
Restaurant Accepts
        |
Delivery Partner Assigned
        |
Food Pickup
        |
Delivery Completed
```

---

# 11. Delivery Partner Assignment

Important interview discussion.

### Goal

Assign nearest delivery partner efficiently.

### Approach

1. Maintain active drivers geo location
2. Use geo spatial indexing
3. Select nearest available rider

Possible tools:

1. Redis GeoSearch
2. Elasticsearch geo queries

---

# 12. Event Driven Architecture

Use:

Apache Kafka

Events:

1. Order placed
2. Payment completed
3. Rider assigned
4. Order delivered

Benefits:

1. Async processing
2. Retry handling
3. Scalability

---

# 13. Payment System

Use external gateways:

1. Razorpay
2. Stripe

Important concepts:

1. Idempotency
2. Retry mechanism
3. Payment reconciliation

---

# 14. Caching Strategy

Use:

Redis

Cache:

1. Restaurant lists
2. Menus
3. Hot items
4. User sessions
5. Active delivery partners

---

# 15. Notification System

Send:

1. Order confirmation
2. Rider assigned
3. Delivery updates

Channels:

1. Push notifications
2. SMS
3. Email

---

# 16. Recommendation Engine

Suggest:

1. Nearby restaurants
2. Frequently ordered food
3. Trending dishes

Pipeline:

```text id="c6y3hx"
User Activity
      |
ML Recommendation Engine
      |
Personalized Suggestions
```

Possible frameworks:

1. TensorFlow
2. PyTorch

---

# 17. Scaling Strategy

## Horizontal Scaling

Scale:

1. Order service
2. Tracking service
3. Search service

---

## Database Sharding

Shard using:

```text id="lrxj2f"
hash(city_id)
```

Reason:
Food delivery traffic usually city based.

---

# 18. Reliability

1. Multi region deployment
2. Replication
3. Failover
4. Backup systems

---

# 19. Bottlenecks

## Peak Hour Traffic

Solution:
Auto scaling.

---

## Tracking Load

Millions of GPS updates.

Solution:
Kafka + Redis streaming.

---

## Search Latency

Solution:
Geo indexing + caching.

---

# 20. Security Considerations

1. Secure payments
2. JWT authentication
3. API rate limiting
4. HTTPS everywhere

---

# 21. Final Architecture

```text id="1jzt9m"
Clients
   |
Load Balancer
   |
API Gateway
   |
--------------------------------------------------------
| User | Order | Restaurant | Delivery | Payment |
--------------------------------------------------------
| Tracking | Notification | Recommendation |
--------------------------------------------------------
                      |
                    Kafka
                      |
--------------------------------------------------------
| Order Workers | Tracking Workers | Delivery Workers |
--------------------------------------------------------
                      |
--------------------------------------------------------
| Redis | PostgreSQL | Elasticsearch | Cassandra |
--------------------------------------------------------
                      |
                 Maps + CDN
```

---

# 22. Interview Closing Statement

> “Food delivery systems are real time distributed systems where the major challenges are scalable order processing, live delivery tracking, and efficient delivery partner assignment. Using event driven architecture, geo spatial indexing, distributed caching, and asynchronous processing helps achieve low latency and high scalability.”

---

# Important Keywords To Mention

1. Geo spatial indexing
2. Real time tracking
3. Event driven architecture
4. Distributed cache
5. Kafka streaming
6. Horizontal scaling
7. Idempotency
8. WebSockets
9. Low latency
10. Fault tolerance
