# Design Video Conferencing System Like Zoom Communications / Google Meet

# 1. Requirements Clarification

## Functional Requirements

### Core Features

1. User can create meeting
2. Join meeting using link/code
3. Audio/video calling
4. Screen sharing
5. Chat messaging
6. Recording meetings
7. Raise hand/reactions
8. Mute/unmute
9. Participant management

---

# 2. Non Functional Requirements

1. Low latency
2. High availability
3. Scalability
4. Fault tolerance
5. Real time communication
6. High video/audio quality

---

# 3. Capacity Estimation

Suppose:

100 million daily users
10 million concurrent meetings
Average meeting duration = 30 mins

### Main Challenge

Real time media streaming at scale.

---

# 4. High Level Architecture

```text id="1kz39j"
Clients
   |
Load Balancer
   |
Signaling Servers
   |
------------------------------------------------
| Meeting Service | Chat Service | Auth Service |
------------------------------------------------
| Recording Service | Notification Service |
------------------------------------------------
                    |
              Media Servers
                    |
------------------------------------------------
| STUN/TURN Servers | SFU/MCU Cluster |
------------------------------------------------
                    |
------------------------------------------------
| Redis | Databases | Object Storage |
------------------------------------------------
```

---

# 5. Core APIs

## Create Meeting

```http id="f58qlv"
POST /meeting/create
```

## Join Meeting

```http id="0d6n5j"
POST /meeting/join
{
  meeting_id,
  user_id
}
```

## Send Chat Message

```http id="zqk0gm"
POST /chat/send
```

---

# 6. Core Components

## A. Signaling Server

Used for:

1. Session setup
2. Exchange SDP
3. ICE candidate exchange

Protocols:

1. WebSocket
2. HTTPS

---

# 7. WebRTC Architecture

Modern video conferencing uses:

WebRTC

Handles:

1. Audio streaming
2. Video streaming
3. Peer connection

---

# 8. Peer To Peer Model

For small meetings.

```text id="4r9mkf"
User A <------> User B
```

### Pros

1. Low server cost
2. Low latency

### Cons

1. Poor scalability
2. Upload bandwidth issue

---

# 9. Mesh Architecture Problem

If 10 users:

Each user sends stream to 9 others.

Connections:

```text id="b2br6f"
N × (N - 1)
```

Very expensive.

---

# 10. SFU Architecture

MOST IMPORTANT interview topic.

Use:

Selective Forwarding Unit.

```text id="cwy4k7"
Users
  |
 SFU
  |
Forwards Streams
```

### How It Works

1. Each user uploads one stream
2. SFU forwards streams to participants
3. No heavy video processing

### Advantages

1. Scalable
2. Lower server CPU
3. Better for large meetings

Examples:

1. Jitsi
2. mediasoup

---

# 11. MCU Architecture

Multipoint Control Unit.

```text id="fvnx07"
Users
  |
 MCU
  |
Mixed Video Output
```

### Pros

1. Lower client bandwidth
2. Simple client rendering

### Cons

1. Very expensive CPU
2. High server cost

---

# 12. Best Interview Answer

Use SFU architecture.

Because:

1. Better scalability
2. Lower latency
3. Industry standard

---

# 13. STUN and TURN Servers

Important interview topic.

## STUN

Helps discover public IP.

## TURN

Relays media when P2P fails.

Use:

1. NAT traversal
2. Firewall bypass

Protocols:

1. UDP preferred
2. TCP fallback

---

# 14. Meeting Flow

```text id="b01ec9"
User Creates Meeting
        |
Meeting Service
        |
Generate Meeting ID
        |
Participants Join
        |
WebRTC Connection Setup
        |
Media Streams Routed via SFU
```

---

# 15. Chat System

Separate service.

Use:

1. WebSockets
2. Pub/Sub architecture

Store:

1. Meeting chat history
2. Attachments

---

# 16. Screen Sharing

Screen captured using browser/device APIs.

Flow:

```text id="xyt98v"
Screen Capture
      |
WebRTC Stream
      |
SFU
      |
Participants
```

---

# 17. Recording System

Important system design point.

### Approach

1. SFU duplicates streams
2. Recording workers combine streams
3. Store in object storage

Use:

1. Amazon Web Services S3
2. Video transcoding pipeline

---

# 18. Adaptive Bitrate Streaming

Very important for quality optimization.

If internet weak:

1. Reduce resolution
2. Lower frame rate

Benefits:

1. Smooth meetings
2. Lower packet loss

---

# 19. Database Design

## Meeting Table

```text id="g8d3dn"
Meeting
-------
meeting_id
host_id
created_at
status
```

## Participant Table

```text id="btb91r"
Participant
-----------
meeting_id
user_id
join_time
leave_time
```

---

# 20. Caching

Use:

Redis

Cache:

1. Active meetings
2. Participant sessions
3. Presence status

---

# 21. Event Driven Architecture

Use:

Apache Kafka

Events:

1. User joined
2. User left
3. Recording started
4. Chat messages

---

# 22. Scaling Strategy

## Horizontal Scaling

Scale:

1. Signaling servers
2. SFU servers
3. Chat servers

---

## Geo Distributed Deployment

Deploy servers near users.

Benefits:

1. Lower latency
2. Better quality

---

# 23. Reliability

1. Multi region deployment
2. Redundant SFU servers
3. Failover routing
4. Auto reconnection

---

# 24. Security

Very important in interview.

### Security Features

1. End to end encryption
2. JWT authentication
3. Secure meeting links
4. Waiting rooms
5. Role based access

---

# 25. Bottlenecks

## Large Meetings

Solution:
Multiple SFUs.

---

## Network Packet Loss

Solution:
Adaptive bitrate + retransmission.

---

## Recording CPU Load

Solution:
Dedicated recording workers.

---

# 26. Final Architecture

```text id="h4skx5"
Clients
   |
Load Balancer
   |
Signaling Servers
   |
------------------------------------------------
| Meeting | Chat | Auth | Recording Service |
------------------------------------------------
                    |
------------------------------------------------
| STUN/TURN | SFU Cluster | Media Workers |
------------------------------------------------
                    |
------------------------------------------------
| Redis | PostgreSQL | Object Storage |
------------------------------------------------
```

---

# 27. Interview Closing Statement

> “Video conferencing systems are low latency real time distributed systems where the main challenges are scalable media streaming, network reliability, and synchronization. Using WebRTC, SFU architecture, adaptive bitrate streaming, and distributed media servers helps achieve scalable and high quality conferencing.”

---

# Important Keywords To Mention

1. WebRTC
2. SFU architecture
3. STUN/TURN
4. Adaptive bitrate
5. Real time communication
6. WebSockets
7. NAT traversal
8. Low latency
9. Horizontal scaling
10. End to end encryption

