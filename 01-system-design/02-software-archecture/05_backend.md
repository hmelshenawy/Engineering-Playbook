# Backend

> This file continues from [04 - HTTP and API](./04_http_api.md).
>
> In the last chapter the Frontend sent a clean HTTP Request to the Backend.
> The Request crossed the network and arrived.
>
> Now we open the **Backend** black box.
> We want to see what happens inside it **after** it receives the Request and **before** it sends the Response.

---

## 1. Overview

### What is the Backend?

The Backend is the part of the system the user **does not see**.
It runs on a server, far from the user's device.
It receives requests, does the real work, and returns answers.

```text
Frontend
  ↓ HTTP
Backend  ← you are here
  ↓
Database
```

### Why do we need a Backend?

The Frontend is on the user's device.
Anything on the user's device can be changed or broken by the user.
So we cannot trust it with important things.

The Backend is the **trusted side**.
It is the place where we put the rules, the secrets, and the real decisions.
It is the brain of the system.

```text
Frontend (untrusted)  →  Backend (trusted)  →  Database
```

### What are its responsibilities?

The Backend has five main jobs:

```text
Backend
├── Routing           (where should this go?)
├── Validation        (is the data valid?)
├── Business Logic    (what should happen?)
├── Data Access       (read or store data)
└── Response Generation (build the answer)
```

We will look at each one at a high level.
None of them get a deep explanation here — each gets its own chapter later.

### Where does it sit?

The Backend sits between the Frontend and the Database.
It is the middle of the system.
It receives a Request from one side and asks the other side for data.

---

## 2. Inside

Let us look at the internal blocks of the Backend.
We keep this high level. We only care about what each block does in the journey.

```text
HTTP Request
      ↓
Routing
      ↓
Validation
      ↓
Business Logic
      ↓
Data Access
      ↓
Build Response
      ↓
HTTP Response
```

### Routing

A Backend has many endpoints: `/login`, `/profile`, `/orders`, and so on.
When a Request arrives, something must decide **which handler** should process it.
That decision is **Routing**.

Routing looks at the Method and the URL, then sends the Request to the right place.

### Validation

The Backend must not trust input.
A Request might be missing fields, or have bad data.
**Validation** checks the input before any real work starts.

For Login, it checks: is the email present? is the password present? do they look right?
If not, the Backend stops and returns an error.

### Business Logic

Validation only checks the *shape* of the data.
**Business Logic** is the real brain — it decides *what should happen*.

For Login, the Business Logic asks:
- Does this user exist?
- Is the password correct?
- Should we allow this login?
- Should we create a token?

These are **rules of the business**, not rules of the data shape.

### Data Access

The Backend does not store data in its memory for long.
Memory disappears when the server restarts.
So the Backend must talk to a **Database** to read and store data.

**Data Access** is the block that talks to the Database.
The Business Logic tells it: "give me the user with this email."
Data Access runs the query and returns the result.

### Response Generation

After the work is done, the Backend must build a clean answer.
**Response Generation** picks the Status Code and puts the result in JSON.
It returns the Response to the Frontend.

---

## 3. Connections

| From → To                    | Direction | Method | Sync/Async | Purpose                  |
| ---------------------------- | --------- | ------ | ---------- | ------------------------ |
| Frontend → Routing            | Incoming  | HTTP   | Sync       | Receive the Request       |
| Routing → Validation          | Internal  | Call   | Sync       | Send to the right handler |
| Validation → Business Logic   | Internal  | Call   | Sync       | Continue if valid         |
| Business Logic → Data Access  | Internal  | Call   | Sync       | Read or store data        |
| Data Access → Database        | Outgoing  | Query  | Sync       | Get or save information   |
| Business Logic → Response     | Internal  | Call   | Sync       | Build the answer          |
| Backend → Frontend            | Outgoing  | HTTP   | Sync       | Send the Response         |

The Backend flow is mostly **Sync**.
Each step waits for the one before it.
If Data Access is slow, the whole Request waits.

---

## 4. Main Flow

Let us follow the Login Request **inside** the Backend, step by step.

```text
HTTP Request  (POST /login)
      ↓
Routing       → /login handler
      ↓
Validation    → check email + password
      ↓
Business Logic → ask for the user
      ↓
Data Access    → read user from Database
      ↓
Business Logic → compare passwords
      ↓
Build Response → 200 OK + token
      ↓
HTTP Response
```

Step by step:

### 1. The Request arrives

The Backend receives `POST /login` with the email and password in the Body.

### 2. Routing decides where to go

Routing looks at the Method (`POST`) and the URL (`/login`).
It sends the Request to the Login handler.

### 3. Validation checks the input

Validation checks that the email and password are present and look right.
If something is wrong, the Backend stops here and returns an error — for example, `400 Bad Request`.

### 4. Business Logic starts the real work

The Business Logic says: "I need the user with this email."
It asks Data Access to fetch that user.

### 5. Data Access reads from the Database

Data Access asks the Database for the user record.
The Database returns the stored data — including the saved password hash.

### 6. Business Logic makes the decision

The Business Logic compares the password from the Request with the saved password hash.
If they match → the login is allowed.
If they do not match → the login is rejected.

### 7. Build Response

If the login is allowed, the Backend builds a Response:
- Status Code: `200 OK`
- Body: a token in JSON.

If the login is rejected, it builds:
- Status Code: `401 Unauthorized`
- Body: an error message.

### 8. The Response goes back

The Response leaves the Backend and travels back to the Frontend over HTTP.

---

## 5. Practical Example

Here is the Login example traced through the whole Backend:

```text
POST /login  { email, password }
   ↓
Routing       → matches /login
   ↓
Validation    → email present? Yes
                 password present? Yes
   ↓
Business Logic → "get user with this email"
   ↓
Data Access    → query Database
   ↓
Database       → returns user record (with password hash)
   ↓
Business Logic → compare password with hash
                 → match? Yes
   ↓
Build Response → 200 OK
                 { "success": true, "token": "abc123" }
   ↓
HTTP Response sent to Frontend
```

And the failure case:

```text
POST /login  { email, wrong password }
   ↓
Routing       → matches /login
   ↓
Validation    → both fields present? Yes
   ↓
Business Logic → "get user with this email"
   ↓
Data Access    → query Database
   ↓
Database       → returns user record
   ↓
Business Logic → compare password with hash
                 → match? No
   ↓
Build Response → 401 Unauthorized
                 { "success": false, "error": "Wrong password" }
   ↓
HTTP Response sent to Frontend
```

Notice the difference:
- Validation checks the **shape** of the data.
- Business Logic checks the **meaning** of the data.

Both can stop the Request, but for different reasons.

---

## 6. Failure and Limits

### What should happen inside the Backend?

- Receive Requests and route them to the right place.
- Validate every input.
- Run the Business Logic.
- Read and store data through Data Access.
- Build and return clean Responses.
- Keep the rules and secrets on the server side.

### What should NOT happen inside the Backend?

- **No storing data in memory only.** Memory is lost on restart. Permanent data belongs in the Database.
- **No trusting the Frontend.** Even if the Frontend validated the data, the Backend must validate again.
- **No mixing Business Logic with the database directly.** The Business Logic should ask Data Access, not talk to the Database itself.
- **No sending secrets back to the Frontend.** The Response must only contain what the user is allowed to see.

### What can fail?

- **Bad input** → Validation stops it → 400.
- **Wrong credentials** → Business Logic rejects it → 401.
- **Database is down** → Data Access fails → 500.
- **Bug in Business Logic** → the Backend might crash → 500.

### How is it handled?

Every failure should become a clean Response with a Status Code.
The Frontend never sees a raw crash — it sees a proper error message.

### When do we not need a Backend?

For a tiny system with no important rules and no shared data, you might not need a Backend.
But any system with users, rules, or real data needs one.

### What problem is still left?

The Backend can process a Request.
Business Logic can make decisions.
But where does it store and read the data?
It cannot remember everything in memory.
It needs a **permanent place** for information.

---

## 7. System Position

The Backend is a **Service**.
It is not the Client, and it is not the Database.
It is the trusted middle that holds the rules.

```text
Frontend
  ↓ HTTP
Backend  ← this block
  ↓ Query
Database
```

Type:

- It is the **Application Layer**.
- It runs on a server.
- It is trusted: the rules and secrets live here, not in the Frontend.

---

## Common Technologies

These are common tools used to build a Backend.
We do not compare them. Each one implements the same Backend role, but with a different programming language and a different architectural style.

- **NestJS** — a Backend framework for Node.js, using TypeScript.
- **Express.js** — a small, flexible Backend framework for Node.js.
- **FastAPI** — a modern Backend framework for Python.
- **Django** — a full Backend framework for Python.
- **Spring Boot** — a Backend framework for Java.
- **ASP.NET Core** — a Backend framework for C#.
- **Laravel** — a Backend framework for PHP.

They all do the same job we described in this chapter.
They receive Requests, route them, validate, run Business Logic, access data, and return Responses.
The difference is only in language and style — the responsibility is the same.

---

## 8. Key Takeaways

* The Backend is the **trusted, hidden side** of the system. It runs on a server.
* It has five main jobs: **Routing, Validation, Business Logic, Data Access, Response Generation**.
- A Request flows through these blocks in order: route → validate → decide → read/write → respond.
- **Validation** checks the shape of the data. **Business Logic** checks the meaning.
- The Backend must **never trust the Frontend** and must **never store permanent data only in memory**.
- Every failure becomes a clean Response with a Status Code.

---

## 9. Next Block

```text
Current Solution
      ↓
The Backend receives the Request,
routes it, validates it, runs the Business Logic,
and reads data through Data Access.
      ↓
Remaining Problem
      ↓
The Backend can process a Request.
But where does it store and read the data?
It cannot remember everything in memory.
Memory disappears when the server restarts.
It needs a permanent place to keep information.
      ↓
Next Block
      ↓
Database
```

[Database](./06_database.md)