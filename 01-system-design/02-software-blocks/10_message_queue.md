# Message Queue

> This file continues from [09 - Cache and Redis](./09_cache_redis.md).
>
> The Cache made reading data much faster.
> But some operations are still slow: sending emails, processing images, generating reports, calling AI models.
> These make the user wait — even when the user does not need an immediate answer.
>
> The fix is a **Message Queue**.

---

## 1. Overview

A Message Queue is a **waiting line for work**.
One part of the system drops a job into it.
Another part picks the job up later and does it.

```text
Backend
  ↓
Message Queue   (jobs wait here)
  ↓
Worker          (does the job later)
```

The Queue **stores** work. It does **not** do the work.

---

## 2. Why

Some operations are naturally slow.
If the Backend does them during the Request, the user **waits**.

```text
User → Backend → resize image → store → Response
                      ↑ user waits here
```

But the user often does not need the result right now.
They just need to know: "your image is being processed."

A Message Queue lets the Backend **return fast**, and do the slow work **later**.

---

## 3. Sync vs Async

There are two ways to handle work:

| Style   | Meaning                | User waits?       |
| ------- | ---------------------- | ----------------- |
| Sync    | Do the work now         | Yes, until done    |
| Async   | Put the work aside, do it later | No, return immediately |

A Message Queue turns slow **Sync** work into **Async** work.

---

## 4. The Three Roles

There are three roles in this pattern:

```text
Producer  →  Queue  →  Consumer
```

- **Producer** — the one who creates the job. Here, the Backend.
- **Queue** — the waiting line that stores the job.
- **Consumer** — the one who picks up the job and does it. (We keep this high level — it gets its own chapter.)

For our example:

- Producer = Backend
- Queue = Message Queue
- Consumer = Worker (next chapter)

---

## 5. Connections

| From → To          | Purpose                        |
| ------------------ | ------------------------------ |
| Backend → Queue     | Drop a job                     |
| Queue → Worker      | Hand the job to whoever is free |
| Worker → (the job)  | Do the slow work                |

The Queue never does the work.
It only **holds** the job until someone is ready to take it.

---

## 6. Simple Flow

Without a Queue — the user waits:

```text
User
  ↓
Backend
  ↓
Resize image
  ↓
Store image
  ↓
Response        (user waited the whole time)
```

With a Queue — the user does not wait:

```text
User
  ↓
Backend
  ↓
Save job in Queue
  ↓
Return Response immediately

Later...

Queue
  ↓
Worker
  ↓
Resize image
  ↓
Store image
```

The heavy work still happens — just **not during the Request**.

---

## 7. Practical Example

A user uploads a profile picture.

**Without a Queue:**

```text
User uploads picture
   ↓
Backend: resize image (slow)
   ↓
Backend: store image
   ↓
Response: "done"
   ↓
User waited the whole time
```

**With a Queue:**

```text
User uploads picture
   ↓
Backend: save job in Queue
   ↓
Response: "upload received, processing"  (instant)
   ↓
User goes on with their day

Later, in the background:

Queue
   ↓
Worker: resize image
   ↓
Worker: store image
   ↓
Done  (the user was not waiting)
```

The work is the same.
The difference is **when** it happens — and that the user does not wait for it.

---

## 8. Failure and Limits

- **Queue down** — the Backend cannot drop the job, so it must handle the failure (or fall back to doing the work Sync).
- **Worker slow or down** — jobs pile up in the Queue and wait. They are not lost, just delayed.
- **Job fails** — the work is not done. (How to retry failed jobs gets its own chapter.)

A Message Queue adds a little complexity.
Use it when work is slow and the user does not need the result right now.

---

## 9. System Position

A Message Queue is an **Infrastructure Component** — a temporary holding place between the Backend and the Worker.

```text
Frontend → Backend → Message Queue → Worker → (slow job)
```

It is not part of the Backend, and it does not do the work.
It just **connects** the one who has the job with the one who does the job.

---

## Common Technologies

These all implement the Message Queue concept — a place to store jobs until someone takes them.

- **RabbitMQ**
- **Apache Kafka**
- **AWS SQS**
- **Redis Streams**
- **NATS**

They differ in style, but the architectural role is the same: **move slow work to the background**.

---

## 10. Key Takeaways

* A Message Queue is a **waiting line for work**.
* It turns slow **Sync** work into **Async** work.
* Three roles: **Producer** (creates), **Queue** (stores), **Consumer** (does).
* The Queue **stores** work — it does **not** perform it.
* The user returns fast. The heavy work happens later in the background.

---

## 11. Next Block

```text
Current Solution
      ↓
A Message Queue can move slow work
to the background, so the user does not wait.
      ↓
Remaining Problem
      ↓
The Queue stores the work.
But who actually takes jobs from the Queue
and executes them?
The Queue does not perform the work —
something else must do it.
      ↓
Next Block
      ↓
Workers
```

[Workers](./11_workers.md)