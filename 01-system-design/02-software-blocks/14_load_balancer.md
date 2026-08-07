# Load Balancer

> This file continues from [13 - API Gateway](./13_api_gateway.md).
>
> The API Gateway is now the single entry point.
> But as users grow, **one server is no longer enough**.
> If all requests hit a single server, it can overload, slow down, or crash — and take the whole system down with it.
>
> The fix is a **Load Balancer**.

---

## 1. Overview

A Load Balancer is a **traffic distributor**.
It sits in front of multiple servers and spreads requests across them.

```text
Requests
   ↓
Load Balancer
├── Server 1
├── Server 2
└── Server 3
```

To the outside, the servers look like **one** system.
Behind the scenes, the work is shared.

---

## 2. Why

One server has limits.

- Too many requests → it becomes **overloaded**.
- Responses get **slower**.
- If it **crashes**, the system goes down completely.

```text
Users → API Gateway → one Backend Server
                        ↑ overloaded, risky
```

The problem is not that the server is bad.
The problem is that **one** server cannot do everything.

---

## 3. Distributing Requests

A Load Balancer takes incoming requests and sends each one to a **different** server.

```text
Request
   │
   ▼
Load Balancer
   │
 ┌───┼───┐
 ▼   ▼   ▼
S1  S2  S3
```

- Request 1 → Server 1
- Request 2 → Server 2
- Request 3 → Server 3
- Request 4 → Server 1 again, and so on.

No single server takes all the load.
The work is shared.

---

## 4. Multiple Backend Servers

With a Load Balancer, the Backend is no longer one machine.
It is a **group** of machines that work together.

```text
Users
   ↓
API Gateway
   ↓
Load Balancer
├── Backend Server 1
├── Backend Server 2
└── Backend Server 3
```

To the Gateway, the group looks like one Backend.
The Load Balancer hides the fact that there are several.

---

## 5. High Availability

With one server, a crash means the system is **down**.

With several servers behind a Load Balancer, a crash is **absorbed**.

```text
Server 2 crashes
   ↓
Load Balancer stops sending to Server 2
   ↓
Sends requests to Server 1 and Server 3 instead
   ↓
Users keep working, maybe a little slower
```

The system stays **available** even when one server fails.
This is called **High Availability**.

---

## 6. Better Performance

Sharing work across servers also makes the system **faster**.

- Each server handles fewer requests.
- Each request gets answered sooner.
- More users can be served at the same time.

```text
One server:     1000 requests → slow
Three servers:  ~333 each → faster
```

More servers, less waiting.

---

## 7. Basic Health Checks

The Load Balancer does not send requests to a dead server.
It regularly asks each server: "Are you okay?"

```text
Server 1: "I'm fine"   → gets requests
Server 2: no answer     → gets skipped
Server 3: "I'm fine"   → gets requests
```

This is a **health check**.
We keep it high level here — the details get their own chapter.

---

## 8. Connections

| From → To                  | Purpose                        |
| -------------------------- | ------------------------------ |
| Gateway → Load Balancer     | Send all traffic to one place   |
| Load Balancer → Servers     | Spread requests across them     |
| Load Balancer → (skip dead) | Avoid unhealthy servers         |

The Load Balancer is the **middle** between the entry point and the servers.

```text
Gateway → Load Balancer → many Backend Servers
```

---

## 9. Practical Example

Users send requests through the API Gateway.

**Without a Load Balancer:**

```text
Users
   ↓
API Gateway
   ↓
Backend Server
   ↓
traffic increases → server becomes busy → users get slow responses
```

**With a Load Balancer:**

```text
Users
   ↓
API Gateway
   ↓
Load Balancer
├── Backend Server 1
├── Backend Server 2
└── Backend Server 3
```

The Load Balancer spreads the requests:

```text
Request 1 → Server 1
Request 2 → Server 2
Request 3 → Server 3
Request 4 → Server 1
...
```

If Server 2 crashes, the Load Balancer simply stops sending to it.
The other two keep working. Users barely notice.

---

## 10. Failure and Limits

- **Load Balancer down** — it becomes a single point of failure. (In real systems, the Load Balancer itself is duplicated to avoid this.)
- **All servers busy** — spreading requests does not help if every server is overloaded. The system needs more servers.
- **Servers not in sync** — if a user's data is on one server but not the others, requests can give different answers. (This is solved with shared storage — a later topic.)

A Load Balancer is useful when one server is not enough.
With very little traffic, one server may be fine.

---

## 11. System Position

A Load Balancer is an **Infrastructure Component** in the Network/Entry Layer.
It sits between the API Gateway and the Backend servers.

```text
Frontend → API Gateway → Load Balancer → many Backend Servers
```

Type:

- It is the **traffic distributor** of the system.
- It does **not** do business work — it only **routes** requests.

---

## Common Technologies

These all implement the Load Balancer concept — spreading traffic across multiple servers.

- **NGINX**
- **HAProxy**
- **AWS Elastic Load Balancer (ELB)**
- **Azure Load Balancer**
- **Google Cloud Load Balancing**

They differ in style, but the architectural role is the same: **many servers working together as one**.

---

## 12. Key Takeaways

* A Load Balancer **distributes requests** across multiple servers.
* It lets the Backend be a **group** of servers, not one machine.
* It gives **High Availability** — if one server crashes, the others keep going.
* It gives **better performance** — each server handles less work.
* It uses **health checks** to avoid sending traffic to dead servers.
* A Load Balancer lets multiple servers work together as one system.

---

## 13. Next Block

```text
Current Solution
      ↓
The application can now handle more traffic.
The Load Balancer spreads requests across
multiple servers, so no single server
is overloaded.
      ↓
Remaining Problem
      ↓
But some files are too large to store
inside the application itself.
Examples:
- User profile images
- Documents
- Videos
- Backups
Where should these files be stored?
      ↓
Next Block
      ↓
File Storage
```

[File Storage](./15_file_storage.md)