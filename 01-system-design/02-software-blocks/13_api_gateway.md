# API Gateway

> This file continues from [12 - Event Bus](./12_event_bus.md).
>
> The system now has many independent services.
> But the Frontend needs data from several of them.
>
> Should the Frontend call every service directly? No — that ties it to the Backend.
> The fix is an **API Gateway**.

---

## 1. Overview

An API Gateway is the **single entry point** for all client requests.
The Frontend talks only to the Gateway.
The Gateway talks to the services behind it.

```text
Frontend
   ↓
API Gateway
   ↓
many services
```

It sits in front of the services, like a front desk.

---

## 2. Why

Without a Gateway, the Frontend must know every service.

```text
Frontend
├── User Service
├── Product Service
└── Order Service
```

This causes problems:

- The Frontend must know each service's address.
- It must make many calls for one screen.
- If a service moves or changes, the Frontend breaks.

The Frontend becomes **tightly coupled** to the Backend architecture.

---

## 3. Single Entry Point

With a Gateway, the Frontend talks to **one** place only.

```text
Frontend
      │
      ▼
API Gateway
├── User Service
├── Product Service
└── Order Service
```

The Frontend no longer knows how many services exist.
It only knows the Gateway.

---

## 4. Request Routing

The Gateway decides **where each request goes**.

```text
Frontend → Gateway → "/users"   → User Service
Frontend → Gateway → "/products" → Product Service
Frontend → Gateway → "/orders"  → Order Service
```

The Frontend sends a request to a path.
The Gateway looks at the path and forwards it to the right service.

---

## 5. Hiding Internal Services

The real services stay **hidden** behind the Gateway.

- The Frontend never sees them.
- Their addresses can change.
- New services can be added.
- Old services can be removed.

None of this affects the Frontend.
The Gateway is the only thing the Frontend sees.

---

## 6. Simplifying the Frontend

Because the Frontend talks to one Gateway, it gets simpler:

- It knows **one** address, not many.
- It does not care how many services exist.
- It does not care where they live.

The complexity moves to the Gateway, where it belongs.

---

## 7. Centralizing Common Functionality

Some jobs are needed by **every** request.
Instead of repeating them in each service, the Gateway can do them once.

Examples (we will not explain these now — each gets its own chapter):

- Checking who the user is.
- Checking permissions.
- Limiting how often a client can call.
- Logging and monitoring.

Doing these in one place keeps the services focused on their real job.

---

## 8. Connections

| From → To                | Purpose                        |
| ------------------------ | ------------------------------ |
| Frontend → API Gateway    | Send all requests to one place  |
| API Gateway → Services    | Forward each request to the right one |

The Gateway is the **middle**.
Clients come in one side. Services sit on the other.

```text
Frontend  →  API Gateway  →  User / Product / Order Services
```

---

## 9. Practical Example

A user opens a page that needs their profile, their orders, and a product list.

**Without a Gateway:**

```text
Frontend
├── call User Service     → get profile
├── call Order Service    → get orders
└── call Product Service  → get products
```

The Frontend knows three services and calls each one.

**With a Gateway:**

```text
Frontend
   │
   ▼
API Gateway
├── /users    → User Service    → profile
├── /orders   → Order Service   → orders
└── /products → Product Service → products
```

The Frontend sends three requests to **one** address.
The Gateway routes each one to the right service.
The Frontend never knew which services existed.

---

## 10. Failure and Limits

- **Gateway down** — the whole system becomes unreachable from the Frontend. The Gateway is a single point of failure.
- **Gateway slow** — every request passes through it, so a slow Gateway slows everything.
- **Too much logic in the Gateway** — it stops being a simple entry point and becomes a heavy service itself.

A Gateway is useful when the Backend has **many** services.
With only one service, a Gateway may be unnecessary.

---

## 11. System Position

An API Gateway is a **separate component** in the Network/Entry Layer.
It sits between the clients and the services.

```text
Frontend → API Gateway → many Backend Services
```

Type:

- It is the **entry point** of the Backend.
- It routes, hides, and centralizes — it does **not** do the business work itself.

---

## Common Technologies

These all implement the API Gateway concept — a single entry point in front of many services.

- **Kong Gateway**
- **NGINX**
- **Envoy Proxy**
- **Spring Cloud Gateway**
- **Azure API Management**
- **AWS API Gateway**

They differ in style, but the architectural role is the same: **one door for the client, many doors behind it**.

---

## 12. Key Takeaways

* An API Gateway is the **single entry point** for all client requests.
* The Frontend talks to **one** Gateway, not to many services.
* The Gateway **routes** each request to the correct service.
* It **hides** the internal services, so the Frontend stays simple.
* It can **centralize** common jobs that every request needs.
* Clients talk to one Gateway. The Gateway talks to many services.

---

## 13. Next Block

```text
Current Solution
      ↓
The API Gateway is now the single
entry point. The Frontend talks to
one place, and the Gateway routes
each request to the right service.
      ↓
Remaining Problem
      ↓
As the number of users grows, one
Gateway server may not be enough.
If that server becomes overloaded or
unavailable, the whole system is affected.
How can we distribute traffic across
multiple servers?
      ↓
Next Block
      ↓
Load Balancer
```

[Load Balancer](./14_load_balancer.md)