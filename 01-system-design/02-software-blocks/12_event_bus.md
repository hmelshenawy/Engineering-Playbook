# Event Bus

> This file continues from [11 - Workers](./11_workers.md).
>
> A Worker can do slow work in the background.
> But when the work is done, **many parts of the system** may need to react.
>
> Should the Worker call each one directly? No — that ties everything together.
> The fix is an **Event Bus**.

---

## 1. Overview

An Event Bus is a **broadcaster**.
One part of the system announces that something happened.
Other parts listen and react — each on its own.

```text
Something happened
  ↓
Event Bus
  ↓
Many listeners react
```

It is a middle point that spreads the news.

---

## 2. Why

After a Worker finishes a job, several things may need to happen:

- Send an email.
- Update analytics.
- Create an audit log.
- Notify another service.

If the Worker calls each one directly, it must **know about every service**.
Add a new service → change the Worker. Remove a service → change the Worker again.

```text
Worker → Email
Worker → Analytics
Worker → Inventory
(tightly coupled)
```

This is **tight coupling**. It makes the system hard to change.

An Event Bus fixes this.
The Worker just says: "OrderCompleted."
It does not care who listens.

---

## 3. What is an Event?

An **Event** is a small message that says: **"something happened."**

```text
Event: OrderCompleted
Data:  { orderId: 42, total: 120 }
```

An event is a **fact**, not a request.
It does not ask anyone to do anything.
It just announces the news. Whoever cares can react.

---

## 4. The Two Roles

There are two roles in this pattern:

- **Publisher** — the one who announces the event.
- **Subscriber** — the one who listens and reacts.

```text
Publisher  →  Event Bus  →  Subscribers
```

For our example:

- Publisher = Worker
- Subscribers = Email Service, Analytics Service, Inventory Service

---

## 5. One Event, Many Listeners

This is the key idea.
One event can reach **many** subscribers at the same time.

```text
OrderCompleted
       │
       ▼
   Event Bus
       │
 ┌─────┼─────┐
 ▼     ▼     ▼
Email Analytics Inventory
```

The Publisher sends the event **once**.
The Event Bus hands it to **everyone** who subscribed.

---

## 6. Loose Coupling

Because of the Event Bus, the Publisher and Subscribers do not know each other.

```text
Publisher  →  does NOT know who listens
Subscriber →  does NOT know who published
```

- The Worker does not know the Email Service exists.
- The Email Service does not know the Worker exists.
- They only share the **event name**.

This is **loose coupling**. It makes the system easy to change.

---

## 7. Why This Makes the System Easier to Extend

Want to add a new reaction to `OrderCompleted`?
For example, a new "Rewards Service" that gives points.

- You do **not** touch the Worker.
- You do **not** touch the other services.
- You just make the Rewards Service **subscribe** to the event.

```text
OrderCompleted
       │
   Event Bus
       │
 ┌─────┼─────┬─────┐
 ▼     ▼     ▼     ▼
Email Analytics Inventory Rewards (new)
```

The new service plugs in without anyone else changing.
That is the power of an Event Bus.

---

## 8. Queue vs Event Bus

These two are easy to mix up.

| Concept      | What it does            |
| ------------ | ----------------------- |
| Message Queue | Moves **work** to a worker |
| Event Bus     | Broadcasts **information** to many |

```text
Queue:    one job  →  one worker
Event Bus: one event →  many listeners
```

A Message Queue is about **doing** work.
An Event Bus is about **spreading** news.

---

## 9. Connections

| From → To                | Purpose                       |
| ------------------------ | ----------------------------- |
| Publisher → Event Bus      | Announce an event              |
| Event Bus → Subscribers    | Deliver the event to each one  |

The Publisher sends the event **once**.
The Event Bus fans it out to **all** subscribers.

---

## 10. Practical Example

A user places an order. The Worker finishes processing it.

```text
User places an order
   ↓
Worker finishes processing
   ↓
Publish: OrderCompleted
   ↓
Event Bus
   ↓
┌──────────────┬──────────────┬──────────────┐
▼              ▼              ▼
Email Service  Analytics Service  Inventory Service
(reacts)       (reacts)          (reacts)
```

Each service reacts **independently**:

- Email Service → sends a confirmation email.
- Analytics Service → updates the sales numbers.
- Inventory Service → removes the sold items from stock.

The Worker did not call any of them.
It only said: "OrderCompleted."
The Event Bus did the rest.

---

## 11. Failure and Limits

- **Subscriber down** — it misses the event. (How to retry and guarantee delivery gets its own chapter.)
- **Event Bus down** — no news spreads. Publishers and Subscribers cannot talk through it.
- **Too many events** — the system can get noisy and hard to follow.

An Event Bus is useful when **many** parts need to react to the **same** thing.
If only one part cares, a direct call may be simpler.

---

## 12. System Position

An Event Bus is an **Infrastructure Component** — a shared broadcaster between services.

```text
Publisher → Event Bus → many Subscribers
```

Type:

- It is the **communication layer** for events.
- It does **not** do the work — it only **spreads** the news.

---

## Common Technologies

These all implement the Event Bus concept — a way to broadcast events to many listeners.

- **Apache Kafka**
- **NATS**
- **RabbitMQ (Event-based usage)**
- **AWS EventBridge**
- **Azure Event Grid**
- **Google Eventarc**

They differ in style, but the architectural role is the same: **one event, many listeners**.

---

## 13. Key Takeaways

* An Event Bus is a **broadcaster** that spreads the news.
* An **Event** is a fact — "something happened" — not a request.
* Two roles: **Publisher** (announces) and **Subscriber** (reacts).
* One event can reach **many** subscribers at the same time.
* It creates **loose coupling** — the Publisher does not know who listens.
* A **Message Queue moves work**. An **Event Bus broadcasts information**.

---

## 14. Next Block

```text
Current Solution
      ↓
An Event Bus lets one event notify
many independent services, without
them knowing about each other.
      ↓
Remaining Problem
      ↓
Our system now has many independent services.
As it grows, the Frontend cannot talk to
every service directly.
We need a single entry point for all
client requests.
      ↓
Next Block
      ↓
API Gateway
```

[API Gateway](./13_api_gateway.md)