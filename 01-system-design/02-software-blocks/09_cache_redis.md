# Cache and Redis

> This file continues from [08 - Authorization](./08_authorization.md).
>
> The Backend works. But as users grow, it keeps asking the Database for the **same data again and again**.
> That is wasteful. The fix is a **Cache**.

---

## 1. Overview

A Cache is a **small, fast storage** between the Backend and the Database.
It keeps a copy of data that is asked for often.

```text
Backend
  ↓
Cache     (fast, temporary)
  ↓
Database  (slow, permanent)
```

The Database is the **source of truth**.
The Cache is just a **shortcut** — it does not replace the Database.

### Cache vs Database

| Property   | Cache        | Database       |
| ---------- | ------------ | -------------- |
| Speed      | Very fast    | Slower          |
| Storage    | Temporary    | Permanent       |
| Role       | Shortcut     | Source of truth |

---

## 2. Why

The Database is permanent, but slow.
If 10,000 users ask for the same popular product, the Database answers the **same question 10,000 times**.

With a Cache, the Backend asks the Database **once**, keeps the answer, and serves the next users from the Cache — instantly.

Less work for the Database. Faster answers for the user.

---

## 3. Connections

| From → To          | Purpose                        |
| ------------------ | ------------------------------ |
| Backend → Cache     | Ask for data (first)            |
| Cache → Backend     | Return the value or "not found" |
| Backend → Database  | Ask only if the Cache misses    |
| Backend → Cache     | Save the value after a miss     |

The Cache never calls anyone. It only answers.
The Backend always asks the Cache **first**, then the Database only if needed.

---

## 4. Simple Flow

Every Cache lookup has one of two results:

- **Cache Hit** — the data is in the Cache. Return it fast.
- **Cache Miss** — the data is not there. Go to the Database.

```text
Backend
   ↓
Cache
   │
 ┌─┴──┐
 Hit   Miss
 │     │
 │     ▼
 │   Database
 │     │
 └─────┘
   ↓
Response
```

---

## 5. Practical Example

After login, the user opens their profile. The Backend needs the user profile.

**First time — Cache Miss:**

```text
Backend needs profile for user 42
   ↓
Cache: not found  (Miss)
   ↓
Database: returns profile
   ↓
Backend saves a copy in Cache
   ↓
Response returned
```

**Second time — Cache Hit:**

```text
Backend needs profile for user 42
   ↓
Cache: found!  (Hit)
   ↓
Response returned  (Database not touched)
```

The first request filled the Cache.
The second request was much faster — the Database was never called.

> One important idea: when the real data changes, the Cache copy must change too, or it will return old data. We will see *how* in later chapters.

---

## 6. Failure and Limits

- **Cache down** — the Backend can still ask the Database. Things get slower, but they still work.
- **Stale data** — the Cache has an old copy. The user sees outdated data.
- **Caching the wrong things** — wasted space, no speed gain.

A Cache is **optional**. The system keeps working without it — just slower.

---

## 7. System Position

The Cache is an **Infrastructure Component** — the temporary part of the Data Layer.

```text
Frontend → Backend → Cache → (Database on miss)
```

It is not the source of truth. The Database is.

---

## Common Technologies

These all implement the Cache concept — a fast, temporary store in front of the Database.

- **Redis** — the most common choice. It supports many capabilities beyond caching (we will explain those later).
- **Memcached** — a simple, focused caching system.
- **Hazelcast** — often used in distributed systems.

---

## 8. Key Takeaways

* A Cache is a **small, fast, temporary** store between the Backend and the Database.
* The Database is the **source of truth**. The Cache is a **shortcut**.
* **Cache Hit** = fast, no Database. **Cache Miss** = go to the Database, then fill the Cache.
* We cache only data that is **asked for often**.
* The Cache is **optional** — without it, things still work, just slower.

---

## 9. Next Block

```text
Current Solution
      ↓
The Cache makes reading data much faster.
      ↓
Remaining Problem
      ↓
Some tasks are still slow, but for a different
reason — they are heavy work:
- Sending emails
- Generating reports
- Processing images
- Calling AI models
The user does not need an immediate answer.
How can the system move these tasks
to the background?
      ↓
Next Block
      ↓
Message Queue
```

[Message Queue](./10_message_queue.md)