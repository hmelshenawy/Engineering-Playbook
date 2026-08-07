# Database

> This file continues from [05 - Backend](./05_backend.md).
>
> In the last chapter the Backend processed the Request.
> The Business Logic made decisions, and Data Access asked for the user record.
>
> But we never asked: **where does that record come from?**
> **How does the Backend remember users, orders, and products?**
>
> The answer is the **Database**. We open this box now.

---

## 1. Overview

### What is a Database?

A Database is the part of the system that **stores information permanently**.
It keeps data even when the server is stopped or restarted.

```text
Frontend
  ↓ HTTP
Backend
  ↓
Database  ← you are here
```

The Backend can read from it, write to it, and ask it questions.
But the Backend does not keep the data inside itself.
The Database is a separate block, with one job: **remember things**.

### Why do applications need a Database?

The Backend runs in **memory**.
Memory is fast, but it has two problems:

- It is **small**. It cannot hold all the users, orders, and products.
- It is **temporary**. When the server restarts, everything in memory disappears.

If the Backend kept users only in memory, every restart would log everyone out and lose every order.
That is not acceptable for a real system.

So we need a **permanent** place to keep data.
That place is the Database.

```text
Memory           Database
─────            ────────
fast             slower
small            large
lost on restart  kept forever
```

### Where does it sit?

The Database sits at the bottom of the request path.
It is the last block the Request reaches on the way in, and the first block it meets on the way back.

```text
Frontend → Backend → Database
                          │
                          └── permanent storage
```

---

## 2. Inside

A Database is not one big bucket.
It usually holds different kinds of data for the application.

```text
Database
├── Users      (who can log in)
├── Products   (what we sell)
├── Orders     (what people bought)
└── Payments   (who paid, and how much)
```

We will not explain how this data is organized inside (tables, rows, keys).
That comes in later chapters.
Here we only care about the Database as the place where the application's information lives.

### The four basic operations

Every Database, no matter the type, supports four basic operations.
You will see them in every chapter that touches data.

| Operation | Meaning          | Login example?                |
| --------- | ---------------- | ----------------------------- |
| Create    | Add new data     | Register a new user            |
| Read      | Get data         | Find the user by email         |
| Update    | Change data      | Update last login time         |
| Delete    | Remove data      | Delete an old account          |

These four are often called **CRUD**: Create, Read, Update, Delete.
You do not need to remember the letters yet — just the four actions.

For our Login example, the Backend mostly does one thing: **Read**.
It reads the user record to check the password.

---

## 3. Connections

| From → To          | Direction | Method | Sync/Async | Purpose                  |
| ------------------ | --------- | ------ | ---------- | ------------------------ |
| Backend → Database  | Outgoing  | Query  | Sync       | Read or write data        |
| Database → Backend  | Incoming  | Result | Sync       | Return the requested data |

A few important things to notice:

- The Database **does not call the Backend**. It only answers.
- The Backend always **starts** the conversation.
- The communication is **Sync**. The Backend waits for the answer before it continues.

```text
Backend
  ↓ "give me user with this email"
Database
  ↑ returns the user record
Backend
```

---

## 4. Main Flow

Let us follow the Login Request all the way to the Database and back.

```text
HTTP Request  (POST /login)
      ↓
Backend  →  Business Logic says: "I need the user"
      ↓
Data Access  →  ask the Database
      ↓
Database  →  find the user record
      ↓
Database  →  return the user record
      ↓
Backend  →  compare the password
      ↓
Build Response
      ↓
HTTP Response
```

Step by step:

### 1. The Backend needs data

After Validation passes, the Business Logic says: "I need the user with this email."
It asks the Data Access block to fetch that user.

### 2. Data Access asks the Database

The Data Access block turns this request into a question the Database understands.
It sends that question to the Database.

### 3. The Database searches

The Database looks for the stored user record that matches this email.

### 4. The Database returns the record

If the user exists, the Database returns the record — including the saved password hash.
If the user does not exist, it returns nothing.

### 5. The Backend uses the data

The Business Logic compares the password from the Request with the saved password hash.
Then it decides: allow the login, or reject it.

### 6. The Response is built

The Backend builds the Response and sends it back to the Frontend.
The Database's job is already done — it only answers when asked.

---

## 5. Practical Example

Here is the Login example traced to the Database and back:

```text
POST /login  { email: "user@example.com", password: "123456" }
   ↓
Backend: "find the user with email = user@example.com"
   ↓
Database: searching...
   ↓
Database returns:
   {
     id: 42,
     email: "user@example.com",
     passwordHash: "$2b$10$..."
   }
   ↓
Backend: compare password "123456" with passwordHash
   ↓
Match? Yes
   ↓
Build Response: 200 OK  { "token": "abc123" }
   ↓
HTTP Response sent to Frontend
```

And the case where the user does not exist:

```text
POST /login  { email: "nobody@example.com", password: "123456" }
   ↓
Backend: "find the user with email = nobody@example.com"
   ↓
Database: searching...
   ↓
Database returns: nothing (no such user)
   ↓
Backend: no user found → reject login
   ↓
Build Response: 401 Unauthorized  { "error": "Wrong email or password" }
   ↓
HTTP Response sent to Frontend
```

Notice something important:
The Database does **not** decide if the login is allowed.
It only **gives the data**.
The decision belongs to the Backend.

### Why multiple requests need shared data

Think about what happens after login.
The user opens their profile page. That is a second Request.
The user places an order. That is a third Request.

Each Request is separate.
The Backend does not remember one Request while handling the next.
But the data must stay consistent across all of them.

```text
Request 1 (login)    → reads user
Request 2 (profile)   → reads the same user
Request 3 (place order) → updates the same user
```

All three Requests talk to the **same Database**.
The Database is what makes the data **shared and persistent** across many Requests and many users.

---

## 6. Failure and Limits

### What should the Database do?

- Store data **permanently**, even across restarts.
- Let the Backend **read, write, update, and delete** data.
- Keep data **consistent** across many Requests and many users.
- Stay the **single source of truth** for the application.

### What should NOT live only in memory?

- User accounts.
- Orders and payments.
- Anything the business cannot afford to lose.

If it matters, it belongs in the Database — not in the Backend's memory.

### What can fail?

- **The Database is down** → the Backend cannot read or write → the Request fails (often a 500 error).
- **The data is wrong** → the Backend trusts bad data and makes a wrong decision.
- **The Database is slow** → every Request that needs data becomes slow.

### How is it handled?

The Backend should not crash when the Database is slow or down.
It should return a clean error and, if possible, try again later.
We will see stronger patterns for this in later chapters (retries, caches, backups).

### When do we not need a Database?

For a tiny tool with no data to keep, you might not need one.
But any system with users, orders, or shared information needs a Database.

### What problem is still left?

The Backend can now store and read data.
The Database remembers the users and their passwords.
But here is a new question:
**Can anyone send a Request and read that data?**
**How does the system know who the user really is?**

---

## 7. System Position

The Database is an **Infrastructure Component**.
It is not the Client, and it is not the Application.
It is the storage layer that the Backend depends on.

```text
Frontend
  ↓ HTTP
Backend
  ↓ Query
Database  ← this block
  (permanent storage)
```

Type:

- It is the **Data Layer**.
- It runs as a separate service the Backend talks to.
- It is the **source of truth** for the application's information.

---

## Common Technologies

Different databases exist for different needs.
We do not compare them here.
They all solve the same architectural problem: **storing and retrieving application data**.

### Relational Databases

- **PostgreSQL**
- **MySQL**
- **SQL Server**
- **Oracle Database**

### NoSQL Databases

- **MongoDB**
- **Cassandra**
- **DynamoDB**

The two groups organize data differently, and each one fits certain kinds of applications better.
We will study those differences in later chapters.
For now, remember the role: **store data permanently, and let the Backend read and write it**.

---

## 8. Key Takeaways

* A Database is the **permanent storage** of the system. It survives restarts.
* The Backend keeps data in **memory** (fast but temporary). The Database keeps it **permanently** (slower but safe).
- Every Database supports four basic operations: **Create, Read, Update, Delete**.
- The Backend **always starts** the conversation. The Database only answers.
- The Database is the **single source of truth** shared across many Requests and many users.
- The Database does **not** make business decisions — it only gives data.

---

## 9. Next Block

```text
Current Solution
      ↓
The Backend can now store and read data.
The Database keeps the users and their passwords
in a permanent place.
      ↓
Remaining Problem
      ↓
The data is stored. But can anyone
send a Request and access that data?
How does the system know who the user is?
How do we stop a stranger from reading
someone else's account?
      ↓
Next Block
      ↓
Authentication
```

[Authentication](./07_authentication.md)