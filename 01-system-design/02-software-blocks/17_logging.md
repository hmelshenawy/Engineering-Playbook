# Logging

> This file continues from [16 - Search](./16_search.md).
>
> The system now has many components working together.
> But sometimes things go wrong: a request fails, a user reports an error, a service crashes, a job does not finish.
>
> Without records, developers do not know **what happened**.
> The fix is **Logging**.

---

## 1. Overview

Logging is the practice of **writing down what happens inside the system**.
Each important event becomes a small line of text — a **log**.

```text
Request
   ↓
Backend
   ├── Log: "request received"
   ├── ... work ...
   └── Log: "response sent"
```

Logs are the system's **memory of its own actions**.

---

## 2. Why

When something fails, the first question is always:

> "What happened?"

Without logs, the answer is: "we do not know."
With logs, the answer is: "let us look at the records."

```text
No logs  →  guess blindly
Logs     →  read what actually happened
```

Logs let developers **see the past** of the system.

---

## 3. What Information Is Usually Logged

A good log line is **short but meaningful**. It usually contains:

- **what** happened (the event)
- **when** it happened (a timestamp)
- **who** was involved (a user id, a request id)
- sometimes **why** it failed (an error message)

```text
2026-08-07 10:14:22  Login request received  user=42
2026-08-07 10:14:22  User found              user=42
2026-08-07 10:14:23  Response sent           user=42
```

We keep this high level. The exact format gets its own chapter.

---

## 4. Following the Journey of a Request

A request travels through many blocks.
Logs let us **follow that journey** later.

```text
Frontend → Backend → Database → Backend → Frontend
            ↑         ↑          ↑
          logs here  logs here  logs here
```

Later, a developer can read the logs in order and **replay** what the request did — step by step.

---

## 5. Error Logs vs Information Logs

Not all logs are the same. Two common kinds:

| Kind         | Meaning                        | Example                       |
| ------------ | ------------------------------ | ----------------------------- |
| Information  | Normal events, things working  | "Login request received"      |
| Error        | Something went wrong            | "Login failed: wrong password" |

```text
Info:  "user found"          (everything fine)
Error: "database timeout"    (something broke)
```

Information logs show the system is **healthy**.
Error logs show **where it broke**.

---

## 6. Why Logs Should Be Meaningful

A log is only useful if it **says something real**.

Bad logs:

```text
"error"
"here"
"done"
```

These tell the developer nothing.

Good logs:

```text
"Login failed for user 42: wrong password"
"Database query took 3000ms: timeout"
```

These point straight to the problem.

A simple rule: **a log should answer "what happened and to whom."**

---

## 7. Connections

| From → To          | Purpose                        |
| ------------------ | ------------------------------ |
| Backend → Logs      | Record each important event     |
| Developer → Logs    | Read them later to find problems |

Logs are a **one-way** flow.
The system writes them. Developers read them.
Logs do **not** change how the system behaves.

```text
Backend → writes → Logs
Developer ← reads ← Logs
```

---

## 8. Practical Example

A user clicks Login. Let us trace it with logs.

**Success case:**

```text
Frontend: user clicks Login
   ↓
Backend receives request
   ↓
Log: "Login request received  user=42"
   ↓
Database queried
   ↓
Log: "User found  user=42"
   ↓
Response sent
   ↓
Log: "Response sent  user=42"
```

**Failure case:**

```text
Frontend: user clicks Login
   ↓
Backend receives request
   ↓
Log: "Login request received  user=42"
   ↓
Database queried
   ↓
Database returns: no match
   ↓
Log: "Login failed  user=42  reason=wrong password"
   ↓
Response sent: 401 Unauthorized
```

Notice the difference:
- In the success case, the logs say **what worked**.
- In the failure case, the logs say **what went wrong and why**.

When a user later reports "I could not log in," the developer searches the logs and finds the exact line.

---

## 9. Failure and Limits

- **No logs** — failures become invisible. Developers guess.
- **Too many logs** — important events drown in noise. Log what matters, not everything.
- **Meaningless logs** — the line exists but says nothing useful.
- **Logs without context** — "error" with no user, no time, no request. Useless.

Logging is needed in **every** serious system.
The art is logging **enough**, but not too much.

---

## 10. System Position

Logging is a **cross-cutting concern** — it touches every block, but it is not one block by itself.

```text
Frontend → Backend → Database
             ↓ logs
          Logs
```

Type:

- It is the **record of what happened**.
- It runs **alongside** every component, not inside one.

---

## Common Technologies

These tools help applications create and manage logs.

- **Winston**
- **Pino**
- **Serilog**
- **Logback**
- **Bunyan**

They differ in language and style, but the role is the same: **turn system events into log lines**.

---

## 11. Key Takeaways

* Logging is **writing down what happens** inside the system.
* Logs answer the question: **"What happened?"**
* A good log says **what**, **when**, and **who**.
* Two common kinds: **Information** logs (healthy) and **Error** logs (broken).
* Logs let developers **follow the journey of a request** and find problems.
* Logs tell us what happened inside the system.

---

## 12. Next Block

```text
Current Solution
      ↓
Logs tell us what happened.
When something fails, developers can read
the logs and find the cause.
      ↓
Remaining Problem
      ↓
But logs only show the past.
How do we know the health of the system
while it is running?
How can we monitor performance, errors,
CPU usage, memory usage, and availability
in real time?
      ↓
Next Block
      ↓
Monitoring
```

[Monitoring](./18_monitoring.md)