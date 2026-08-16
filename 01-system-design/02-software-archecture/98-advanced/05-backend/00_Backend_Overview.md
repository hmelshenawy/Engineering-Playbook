# Backend Overview — Opening the Box

> This is the start of the **Backend Advanced** section.
>
> You already know what a Backend is, why it exists, and where it fits in the system.
> Now we open the Backend **black box** and look at the parts inside.
>
> This file is a **map**, not a tutorial.
> Every part here gets its own file later.

---

## What This File Is For

The Backend is not one big thing.
It is a group of smaller parts, each with one job.
A Request enters the Backend, passes through these parts, and leaves as a Response.

```text
HTTP Request  →  [ many parts inside the Backend ]  →  HTTP Response
```

This file shows you those parts, in order, so you have a mental model before we go deeper.

---

## The Main Mental Model

Here is a simplified view of what happens inside the Backend when a Request arrives:

```text
HTTP Request
      │
      ▼
Routing
      │
      ▼
Controller
      │
      ▼
Validation
      │
      ▼
Authentication
      │
      ▼
Authorization
      │
      ▼
Business Logic / Service
      │
      ▼
Repository / Data Access
      │
      ▼
Database
      │
      ▼
Build Response
      │
      ▼
HTTP Response
```

> This is a **learning model**.
> Real Backends may change the exact order or structure.
> Some steps run as middleware before the Controller. Some steps are combined. Some are skipped.
> The goal here is the **shape**, not the exact rule.

---

## The Parts, In Order

### Request Flow

Request Flow is the **overall path** a Request takes through the Backend.
It is the idea that ties all the other parts together.
This file is the map of that flow.

### Routing

Routing decides **which part of the Backend handles this Request**.
It looks at the URL and the method, and sends the Request to the right place.

### Controller

The Controller is the **entry point** for a specific endpoint.
It receives the Request, asks the other parts to do their jobs, and returns the Response.
It coordinates — it does not do the real business work itself.

### Validation

Validation checks that the **input is correct** before any real work starts.
Is the email present? Is the number actually a number?
If the input is bad, the Backend stops here and returns an error.

### Authentication

Authentication answers: **who is the user?**
It checks the proof the user sent and confirms the identity.
It does not decide what the user is allowed to do.

### Authorization

Authorization answers: **what is this user allowed to do?**
It checks permissions against the action the user is trying to perform.
A logged-in user can still be denied.

### Business Logic

Business Logic is the **brain** of the Backend.
It applies the rules of the business: who can place an order, what the price is, what happens next.
This is the part that makes the system *useful*.

### Services

A Service is a block of code that **holds one piece of business logic**.
Instead of one huge pile of rules, the Backend splits them into services — a UserService, an OrderService, and so on.
Controllers call services. Services hold the rules.

### Repository / Data Access

The Repository is the part that **talks to the database** so the business logic does not have to.
It turns "give me user 42" into a database query and returns clean data.
It hides the database details from the rest of the Backend.

### Database Communication

Database Communication is the **actual exchange** between the Backend and the database — sending queries and receiving results.
This is where the Backend meets the Data Layer.

### Error Handling

Error Handling decides **what happens when something fails**.
Bad input, a missing user, a database timeout — each becomes a clean, useful error Response instead of a crash.

### Logging

Logging records **what happened** inside the Backend.
Each important event becomes a log line that developers can read later to find problems.

### Background Jobs

Background Jobs are pieces of **slow work moved out of the Request path** — sending emails, generating reports, processing images.
The Request returns fast. The job runs later.

### Response

The Response is the **final output** the Backend sends back.
It takes the result of the business logic, shapes it (often as JSON), picks a status code, and sends it to the client.

---

## Not Everything Sits In the Main Path

Some Backend responsibilities are **not** a step in the main Request flow.
They run **alongside** it, around it, or after it.

```text
Backend
├── Main Request Flow   (Routing → ... → Response)
├── Error Handling       (jumps in when anything fails)
├── Logging              (records events across the whole flow)
└── Background Jobs      (runs after the Response is sent)
```

- **Error Handling** wraps the flow — it can catch a failure at any step.
- **Logging** watches every step — it is not one step itself.
- **Background Jobs** leave the flow — the Request returns, and the job continues later.

These are **cross-cutting**: they touch the whole Backend, not just one point in the line.

---

## Roadmap — Backend Advanced Section

The next files will open each part, in this order:

| Order | Topic | Purpose |
|------:|-------|---------|
| 01 | Request Flow | Follow one Request through the Backend |
| 02 | Routing | Send each Request to the right handler |
| 03 | Controllers | Receive the Request and coordinate the work |
| 04 | Validation | Check input before any real work |
| 05 | Authentication | Confirm who the user is |
| 06 | Authorization | Check what the user is allowed to do |
| 07 | Business Logic | Apply the rules of the business |
| 08 | Services | Group business logic into clear units |
| 09 | Repository | Talk to the database in a clean way |
| 10 | Database Communication | Send queries and receive data |
| 11 | Error Handling | Turn failures into clean responses |
| 12 | Logging | Record what happened inside the Backend |
| 13 | Background Jobs | Move slow work out of the Request |
| 14 | Response | Build and return the final answer |

Each file opens one box. By the end, you will understand the Backend from the inside.

---

## What's Next

Now you know **what exists inside the Backend** and roughly **how the parts fit together**.

The next step is to follow **one Request** through these parts, from the moment it arrives to the moment the Response leaves.

Next:

[01_Request_Flow.md](./01_Request_Flow.md)