# Software System Overview

> This is the first file in the Software Architecture learning path.
> Before we talk about any technology, we need to understand what a *software system* actually is.

---

## 1. Overview

### What is a software system?

A software system is a group of parts that work together to serve one purpose for its users.

It is not a single program. It is not a single file. It is the **whole thing** that turns a user action into a useful result.

```text
User wants something
      ↓
Software System
      ↓
User gets the result
```

Example: when a customer clicks **"Place Order"** on a phone, a lot of hidden work happens before the screen says *Order Confirmed*. All that hidden work, plus the screen the customer sees, is the software system.

### What problem does it solve?

A software system solves a real business need:

- A shop needs to sell products.
- A hospital needs to manage patients.
- A bank needs to move money safely.

The system exists to serve that need, not to show off technologies.

### Where does it sit?

The software system lives between the **user** and the **business result**.

```text
User (human)
   ↓
Software System
   ↓
Business Outcome (order placed, email sent, payment done)
```

---

## 2. Inside

A software system is built from smaller parts called **blocks**. Each block has **one responsibility** — it does one job well and does not try to do everything.

```text
Software System
├── Client Layer        (what the user sees and touches)
├── Application Layer   (the rules and decisions)
├── Data Layer          (where information is stored)
└── Infrastructure Layer (what runs and connects everything)
```

These are the **main architecture layers**. We will not explain them deeply here — each layer gets its own file later. For now, just know they exist.

### Why does software split into blocks?

It does not start split. Most systems begin as **one small application**. Then they grow.

```text
Small app
   ↓ grows
Bigger app
   ↓ grows more
One huge application
   ↓ becomes painful
Hard to change, hard to scale, hard to understand
```

When one application tries to do everything, it becomes a problem:

- A change in one part breaks another part.
- One team cannot work without blocking another team.
- One slow feature slows down the whole system.
- You cannot scale a busy part without scaling everything.

So the system **naturally evolves** into multiple blocks, where each block owns one responsibility and can be changed, scaled, and understood on its own.

> Software does not split into blocks because it is "modern".
> It splits because one huge application stops working well at some point.

---

## 3. Connections

A software system is not just a list of blocks. The blocks **talk to each other** to finish one request.

| Connects To        | Direction | Purpose                       |
| ------------------ | --------- | ----------------------------- |
| Client → Backend   | Outgoing  | Send a user action             |
| Backend → Database | Outgoing  | Read or write data             |
| Backend → Cache    | Outgoing  | Read fast temporary data       |
| Backend → Worker   | Outgoing  | Hand off slow background work  |

No block works alone. A single user request usually passes through **several blocks** before it is done.

---

## 4. Main Flow

At a high level, one request travels like this:

```text
User clicks
   ↓
Client Layer        (build and send the request)
   ↓
Application Layer   (apply rules, take decisions)
   ↓
Data Layer          (read or store information)
   ↓
Application Layer   (build the result)
   ↓
Client Layer        (show the result to the user)
   ↓
User sees the result
```

Notice that some layers are visited twice. The application layer is the brain — it sits between what the user asked for and where the data lives.

---

## 5. Practical Example

A customer wants to **view their profile**.

```text
Customer opens profile page
   ↓
Client Layer  →  sends "get my profile" request
   ↓
Application Layer  →  checks who the user is
   ↓
Data Layer  →  reads the user record from storage
   ↓
Application Layer  →  prepares the profile data
   ↓
Client Layer  →  shows the profile on screen
   ↓
Customer sees their profile
```

One simple action. Multiple blocks. One business request served.

---

## 6. Failure and Limits

A software system made of blocks is stronger than one huge application, but it is not magic.

* **What can fail?** If one block fails, the request can fail too — the chain is only as strong as its links.
* **How does the system handle it?** Each block can be replaced, retried, or isolated so one failure does not bring down everything.
* **When do we not need this?** For a very small or experimental project, one application is fine. Splitting too early adds complexity without benefit.
* **What problem still remains?** Once you have many blocks, a new question appears: **how do they actually talk to each other for a single request?**

---

## 7. System Position

The software system is **the whole thing**. It is not a module, not a service, not an infrastructure component — it *contains* all of them.

```text
Software System
   ↓
┌─────────────────────────────────────────────┐
│ Block          → a generic named piece       │
│ Module         → code grouped inside the app │
│ Service        → a block that runs on its own │
│ Infrastructure → the base that runs services  │
└─────────────────────────────────────────────┘
```

Quick differences, in simple words:

* **Block** — a generic name for any part with one responsibility.
* **Module** — code that is grouped together *inside* one application. It does not run on its own.
* **Service** — a block that runs *on its own* process and is called by other blocks.
* **Infrastructure Component** — the base layer that services run on (servers, networks, storage). It does not contain business logic.

---

## 8. Key Takeaways

* A software system is a group of parts that work together to serve one purpose.
* Software splits into blocks because one huge application stops being manageable.
* Each block has **one responsibility** and does it well.
* Blocks are not isolated — they communicate to finish one business request.
* The main layers are **Client, Application, Data, and Infrastructure** — we will explore each later.

---

## 9. Next Block

```text
Current Solution
      ↓
A software system is divided into multiple blocks,
each with one responsibility.
      ↓
Remaining Problem
      ↓
We know there are blocks, but a request must pass
through several of them to get done.
How exactly does one user request travel across
all these blocks?
      ↓
Next Block
      ↓
Request Lifecycle
```

[Request Lifecycle](./02_request_lifecycle.md)