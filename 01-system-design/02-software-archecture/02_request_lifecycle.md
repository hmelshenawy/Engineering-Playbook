# Request Lifecycle

> This file continues from [01 - Software System Overview](./01_system_overview.md).
>
> At the end of the last chapter we had an open question:
> "If a software system is split into many blocks, how does one request travel through all of them?"
>
> Here we follow **one request** from the first click to the final response.

---

## 1. Overview

### What is the Request Lifecycle?

The Request Lifecycle is **one trip** that a request makes.
It starts when the user does something.
It ends when the user sees the result.

```text
User click
   ↓
... a trip through many blocks ...
   ↓
User sees the result
```

### What problem does it solve?

The user does not see the blocks.
The user does one simple thing: *click Login*.
The user sees one simple thing: *Logged in*.

Between those two moments, the request travels through many blocks.
Each block does its job and passes the request forward.
Without understanding this trip, the rest of this course is just a list of tools with no reason.

### Where does it sit inside the system?

The Request Lifecycle is not a block by itself.
It is **the path** the request takes through the blocks.
It is what connects the whole system together.

---

## 2. Inside

The Request Lifecycle does not contain components.
It describes a **sequence of stops**.
Each stop is one of the blocks we talked about before.

```text
Login Request
├── User            (the starting point)
├── Browser         (where the frontend runs)
├── Frontend        (shapes the request)
├── HTTP Request    (the message on the network)
├── Backend         (receives and directs)
├── Authentication  (checks who the user is)
├── Business Logic  (makes the decision)
├── Database        (stores and returns data)
└── Response        (the answer going back)
```

> We treat every block here as a **black box**.
> A black box means: we know what goes in and what comes out, but we do not look inside.
> We will not explain what is inside each block now.
> Each block gets its own chapter later.
> The only thing we care about here is: **why does the request visit this block?**

---

## 3. Connections

The request moves in one mostly straight line.
But at some stops it talks to other blocks too.

| From → To                 | Direction | Method | Sync/Async | Purpose                  |
| ------------------------- | --------- | ------ | ---------- | ------------------------ |
| User → Browser            | Outgoing  | Click  | Sync       | Start the request         |
| Frontend → Backend         | Outgoing  | HTTP   | Sync       | Send the login data      |
| Backend → Authentication   | Internal  | Call   | Sync       | Check the user            |
| Backend → Business Logic   | Internal  | Call   | Sync       | Process the login         |
| Backend → Database         | Outgoing  | Query  | Sync       | Read the user data        |
| Backend → Frontend         | Incoming  | HTTP   | Sync       | Return the response       |

One important idea: the request is **Sync** for most of the trip.
Sync means each block waits for the next one to finish before it can continue.
So if one block is slow, the whole request waits.
(We will see how to fix this in later chapters.)

---

## 4. Main Flow

Let us follow one **Login request** step by step.
The same scenario stays with us for the whole file.

```text
User
  ↓
Browser
  ↓
Frontend
  ↓
HTTP Request
  ↓
Backend
  ↓
Authentication
  ↓
Business Logic
  ↓
Database
  ↓
Response
  ↓
Browser
  ↓
User
```

For each stop we answer three things:
- **Why** the request goes there.
- **What enters** that block.
- **What leaves** that block.

### 1. User

- **Why:** The user is the starting point. Without the user, there is no request.
- **What enters:** The user thinks "I want to log in" and clicks the **Login** button.
- **What leaves:** A click event. That click is the spark that starts everything.

### 2. Browser

- **Why:** The frontend lives inside the browser. The browser is what catches the click and wakes the frontend up.
- **What enters:** The click event from the user.
- **What leaves:** A call to the frontend code, telling it "the user clicked Login."

### 3. Frontend

- **Why:** The backend cannot read raw clicks. It needs clean, organized data. The frontend prepares that data.
- **What enters:** The form values the user typed — the **email** and the **password**.
- **What leaves:** A ready **HTTP Request** that contains those values in a format the backend can read.

### 4. HTTP Request

- **Why:** The frontend and the backend live in different places. The frontend runs on the user's device. The backend runs on a server far away. They need a way to send messages. That way is **HTTP**.
- HTTP is just a set of rules for how two machines talk over the network.
- **What enters:** The request the frontend just built.
- **What leaves:** The same request, now traveling over the network toward the backend.

### 5. Backend

- **Why:** The backend is the manager. It receives the request, understands it, and sends it to the right inner blocks.
- **What enters:** The HTTP Request that arrived from the network.
- **What leaves:** A clean internal call to the next block — usually Authentication.

### 6. Authentication

- **Why:** Before we do anything, we must answer one question: *who is this user?* Authentication is the block that checks identity.
- **What enters:** The email and password from the request.
- **What leaves:** A clear answer — "yes, this is the right user" or "no, rejected."

### 7. Business Logic

- **Why:** Authentication only says *who* the user is. It does not decide *what should happen*. The Business Logic is the brain that makes the real decision: should this login be allowed? Should we start a Session? Should we create a JWT?
- **What enters:** The "yes, valid user" answer from Authentication.
- **What leaves:** A decision — "login is allowed, here is the proof" (for example, a token).

### 8. Database

- **Why:** The user's real data lives in the database. To check the password, the backend must compare what the user typed with what we stored. That stored data is in the database.
- **What enters:** A **query** — a question asked to the database, like "give me the user with this email."
- **What leaves:** The **user record** — the stored data for that user (including the saved password hash).

### 9. Response

- **Why:** The user did not ask to know about blocks. The user asked to log in. So the backend builds one clean answer and sends it back.
- **What enters:** The final result from the Business Logic (success, plus a token).
- **What leaves:** An **HTTP Response** — a simple message like `200 OK` with the token, ready to travel back.

The response goes back the same way, but in reverse:

```text
Database
  ↓ query result
Business Logic
  ↓ builds the result
Authentication
  ↓ adds identity info
Backend
  ↓ builds HTTP Response
HTTP
  ↓
Browser
  ↓
Frontend
  ↓ renders the result
User
  ↓ sees "Logged in"
```

Notice something important.
The **response walks the same path backwards**.
Each block hands the result to the one before it, until it reaches the user.

---

## 5. Practical Example

Let us trace the same scenario on a real login:

```text
The user types email + password and clicks Login
   ↓
The Browser wakes the Frontend
   ↓
The Frontend collects the data and sends an HTTP Request
   ↓
The HTTP Request reaches the Backend
   ↓
The Backend passes the data to Authentication
   ↓
Authentication asks the Database for the user record
   ↓
The Database returns the user record (with the password hash)
   ↓
Authentication compares the passwords and returns success/fail
   ↓
Business Logic starts a Session and creates a JWT
   ↓
The Backend builds the HTTP Response (200 OK + token)
   ↓
The HTTP Response reaches the Browser
   ↓
The Frontend shows: "Welcome back"
   ↓
The user sees: "Logged in"
```

This is the same flow we described.
If any block in the middle fails, the whole request stops.
That is the natural weak point of a Sync flow.

---

## 6. Failure and Limits

* **What can fail?** Any block on the path can fail. The backend might be down. The database might crash. Authentication might reject the user. The HTTP request might time out. Any of these breaks the login.
* **How does the system handle it?** Each block returns an error instead of failing silently. The response then tells the user that something went wrong.
* **When do we not need this flow?** If the system is very small, or all the data lives on the client side, the request might not touch all these blocks. But in most real systems, login follows this path.
* **What problem is still left?** So far we treated every block as a **black box**. The request reached the first block on the path — the Frontend. But what happens *inside* the Frontend before the request even leaves the user's device?

---

## 7. System Position

The Request Lifecycle is not a Module, not a Service, and not an Infrastructure Component.
It is a **Concept** that describes the path the request takes through the blocks.

```text
Frontend
   ↓ HTTP
Backend
   ├── Authentication
   ├── Business Logic
   │      ↓ Query
   └── Database
   ↓ HTTP Response
Frontend
   ↓
User
```

We will go deep into each block in this diagram in its own chapter.
Here we only draw the map of the path.

---

## 8. Key Takeaways

* The Request Lifecycle is **one trip** for a request, from the user to the response and back.
* Each block has **one responsibility**, and the request visits it to use that responsibility.
* The **response goes back the same way, in reverse**, from the backend to the user.
* Most of this flow is **Sync**, so one slow block slows down the whole request.
* We still treat every block as a **black box** — the inside of each one comes in its own chapter.

---

## 9. Next Block

```text
Current Solution
      ↓
We followed the Login request from the first click
to the final response, and we saw how the request
travels through the blocks.
      ↓
Remaining Problem
      ↓
The first block the request reached was the Frontend.
But what happens inside the Frontend before the request
even leaves the user's device?
Collecting the data, validation, the API call...
all of that happens before we leave the user's machine.
      ↓
Next Block
      ↓
Frontend
```

[Frontend](./03_frontend.md)