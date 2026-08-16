# Workers

> This file continues from [10 - Message Queue](./10_message_queue.md).
>
> The Queue can move slow work to the background.
> But the Queue only **stores** jobs — it does not do them.
>
> So who actually performs the work?
> That component is the **Worker**.

---

## 1. Overview

A Worker is a program that **takes jobs from the Queue and executes them**.

```text
Message Queue
  ↓
Worker   (does the actual work)
  ↓
Result
```

The Queue holds the job.
The Worker does the job.

---

## 2. Why

In the last chapter, the Backend dropped a "resize image" job into the Queue and returned fast.
That job does not run by itself.
Something must pick it up, do the slow work, and store the result.

That something is the Worker.
Without a Worker, jobs sit in the Queue **forever**.

---

## 3. Queue vs Worker

These two are easy to mix up. They are different roles:

| Role   | What it does            |
| ------ | ----------------------- |
| Queue  | Stores the job           |
| Worker | Performs the job         |

```text
Queue   →  "here is a job"
Worker  →  "I will do it"
```

One keeps the work. The other does the work.

---

## 4. Worker Responsibilities

A Worker has a simple list of jobs:

- Take the next job from the Queue.
- Execute it.
- Store the result.
- Move to the next job.

```text
Queue
├── Job 1
├── Job 2
└── Job 3
   ↓
Worker
   ↓
Execute Job 1
   ↓
Execute Job 2
   ↓
Execute Job 3
```

The Worker does **not** talk to the user.
It runs quietly, in the background, far from the Request.

---

## 5. Independent Execution

The Worker runs **separately** from the Backend.

```text
Backend  →  handles user Requests
Worker   →  handles background jobs
```

They are different programs.
If the Worker is slow, the user does not wait — the Backend already answered.
If the Backend is busy, the Worker keeps working.

This separation is the whole point. The slow work is **fully independent** of the user's Request.

---

## 6. Multiple Workers

One Worker is just the start.
When many jobs pile up, one Worker is not enough.
The system can run **several Workers** at the same time, all reading from the same Queue.

```text
        Queue
   ┌─────┼─────┐
   ↓     ↓     ↓
Worker Worker Worker
```

Each Worker takes one job.
More Workers means more jobs done at the same time.

We will not go into scaling Workers here — that gets its own chapter.

---

## 7. Connections

| From → To          | Purpose                       |
| ------------------ | ----------------------------- |
| Queue → Worker      | Hand over a job                |
| Worker → (the job)  | Execute it                     |
| Worker → Storage    | Store the result               |

The Worker is the **last** step in the background flow.
After it finishes, the result is ready for the user to pick up later.

---

## 8. Practical Example

A user uploads a profile picture. This continues the example from the last chapter.

```text
User uploads an image
   ↓
Backend creates a job
   ↓
Message Queue stores the job
   ↓
Worker takes the job
   ↓
Worker resizes the image
   ↓
Worker stores the result
   ↓
User can later access the processed image
```

Full picture, end to end:

```text
User
  ↓
Backend  ──→  returns immediately: "upload received"
  │
  ▼
Message Queue
  │
  ▼
Worker
  ├── resize image
  └── store result
  │
  ▼
Processed image ready (later)
```

The user did not wait.
The Worker did the heavy work quietly in the background.

---

## 9. Failure and Limits

- **Job fails** — the work is not done. (How to retry gets its own chapter.)
- **Worker down** — jobs wait in the Queue until the Worker comes back. They are not lost.
- **Queue empty** — the Worker simply waits for the next job.

A Worker is needed only when work is slow and the user does not need the result right now.
For fast work, the Backend can just do it directly.

---

## 10. System Position

A Worker is a **separate process** in the Application Layer.
It is not the Backend and not the Queue.
It is the one that **does** the background work.

```text
Frontend → Backend → Queue → Worker → result
```

Type:

- It is the **execution side** of background processing.
- It runs independently from the user's Request.

---

## Common Technologies

These all implement the Worker concept — a program that takes jobs and performs them.

- **BullMQ Workers**
- **Celery**
- **Sidekiq**
- **Hangfire**
- **Custom Worker Services**

They differ in language and framework, but the role is the same: **read from the Queue, do the work, store the result**.

---

## 11. Key Takeaways

* A Worker **takes jobs from the Queue and executes them**.
* The **Queue stores** work. The **Worker performs** work.
* The Worker runs **independently** from the Backend and the user's Request.
* One Queue can feed **many Workers** at the same time.
* Without a Worker, jobs just sit in the Queue.

---

## 12. Next Block

```text
Current Solution
      ↓
Workers take jobs from the Queue and
perform the slow work in the background.
The user does not wait.
      ↓
Remaining Problem
      ↓
But what if multiple parts of the system
need to know that something happened?
For example:
- Send an email
- Update analytics
- Notify another service
How can one event notify many
different components at once?
      ↓
Next Block
      ↓
Event Bus
```

[Event Bus](./12_event_bus.md)