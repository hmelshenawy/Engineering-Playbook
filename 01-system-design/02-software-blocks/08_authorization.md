# Authorization

> This file continues from [07 - Authentication](./07_authentication.md).
>
> In the last chapter the system learned who the user is.
> Authentication answers: **"Who are you?"**
>
> But knowing who the user is is not enough.
> A normal user and an admin are both *known* — but they should not be able to do the same things.
>
> The system must decide **what this user is allowed to do**.
> That decision is **Authorization**. We open this box now.

---

## 1. Overview

### What is Authorization?

Authorization is the process of **deciding what an authenticated user is allowed to do**.

It answers one single question:

> **What are you allowed to do?**

```text
Authenticated Request
      ↓
Authorization
      ↓
Can this user do this? → Yes / No
```

If the answer is "yes," the Request continues.
If the answer is "no," the Request is rejected — even though the user is logged in.

### Why do we need Authorization?

After login, every user is authenticated.
But users are not the same.
A normal user should read their own profile.
An admin should see all users.
A stranger should not delete someone else's account.

```text
Normal user  →  "I want to see all users"   →  should be denied
Admin user   →  "I want to see all users"   →  should be allowed
```

Both are logged in. Both are known.
The difference is **permissions**, not identity.

Without Authorization, any logged-in user could do anything.
That is dangerous.

### Authentication vs Authorization

These two words are easy to mix up.
They are two separate steps that happen one after the other.

| Step             | Question                | When          | Result                       |
| ---------------- | ----------------------- | ------------- | ---------------------------- |
| Authentication   | Who are you?            | At login      | Known user or stranger       |
| Authorization    | What can you do?        | On every Request | Allowed or denied         |

```text
Request
  ↓
Authentication  → "Who are you?"
  ↓
Authorization  → "What are you allowed to do?"
  ↓
Business Logic
```

One user can pass Authentication but fail Authorization.
"I know who you are — but you still cannot do this."

### Identity vs Permissions

- **Identity** is *who* the user is. Example: "user number 42."
- **Permissions** are *what* the user is allowed to do. Example: "can delete users."

```text
Identity:     "I am user 42."
Permission:   "Can user 42 delete users? → No."
```

A user can be known without being allowed.
Identity is about **knowing**.
Permissions are about **allowing**.

### Where does it sit?

Authorization sits inside the Backend, **after** Authentication and **before** the Business Logic.
It is the gate the Request must pass before any real work starts.

```text
HTTP Request
  ↓
Routing
  ↓
Authentication
  ↓
Authorization  ← you are here
  ↓
Business Logic
```

---

## 2. Inside

Authorization is a small decision flow.
It has three main parts.

```text
Authorization
├── Protected Resource   (the thing being accessed)
├── Required Permission  (what is needed to access it)
└── Access Decision      (granted or denied)
```

### Protected Resource

A **protected resource** is anything not everyone can touch.
Examples: the list of all users, an order, a payment, an admin panel.

Not every resource is protected.
A public product list is not protected.
The list of all users is protected.

### Required Permission

Each protected resource has a **required permission**.
To access it, the user must have that permission.

```text
Resource: GET /admin/users
Required: "can view all users"
```

### Access Decision

The Authorization block compares the user's permissions with the required permission.
It gives one answer:

- **Granted** → the user has the permission → continue.
- **Denied** → the user does not have it → reject.

We will not explain how permissions are stored or modeled here (RBAC, ABAC, policies — all get their own chapters).
We only care about the decision: **yes or no**.

---

## 3. Connections

| From → To                    | Direction | Method | Sync/Async | Purpose                  |
| ---------------------------- | --------- | ------ | ---------- | ------------------------ |
| Authentication → Authorization | Internal | Call   | Sync       | Pass the known identity   |
| Authorization → Business Logic | Internal | Call   | Sync       | Continue if granted       |
| Authorization → Response       | Internal | Call   | Sync       | Reject if denied          |
| Backend → Frontend            | Outgoing | HTTP   | Sync       | Send the result           |

A key point: Authorization is **Sync**.
The Backend must know the decision before it can continue.

```text
Authenticated Request
  ↓
Authorization: does user have permission?
  ├── Yes → Business Logic
  └── No  → 403 Forbidden
```

---

## 4. Main Flow

Let us follow our scenario.
The user already logged in successfully in the previous chapter.
Now they send a new Request.

```text
User logs in successfully
  ↓
Authentication confirms identity
  ↓
User requests:  GET /admin/users
  ↓
Backend checks Authorization
  ↓
Permission granted?
  ├── Yes → Continue
  └── No  → Return "Access Denied"
```

Step by step:

### 1. The user is already authenticated

From the previous chapter, the login succeeded.
The user carries proof of who they are.
They are no longer anonymous.

### 2. The user sends a new Request

Now the user asks for something more powerful:
`GET /admin/users` — the list of all users in the system.

### 3. Authentication runs first

The Backend checks the proof.
The user is confirmed as "user number 42."
Authentication passes.

### 4. Authorization runs next

The Backend looks at the Request: this is `GET /admin/users`.
This is a **protected resource**.
The required permission is "can view all users."

### 5. The access decision

```text
       Has Permission?
       ├── Yes → Continue
       └── No  → Reject Request
```

The Backend compares:
- Required: "can view all users"
- User has: only "can view own profile"

They do not match → the decision is **Denied**.

### 6. The result

```text
Denied → 403 Forbidden
Granted → continue to Business Logic
```

In our scenario, the user is **denied**.
The Backend returns `403 Forbidden`.
The user is logged in (Authentication passed) but not allowed (Authorization failed).

---

## 5. Practical Example

Here is the scenario traced fully:

```text
User logs in successfully  →  user 42, proof given
   ↓
User sends:  GET /admin/users
   ↓
Backend: Authentication
   proof valid?  Yes  →  user is "42"
   ↓
Backend: Authorization
   resource:    GET /admin/users
   required:   "can view all users"
   user has:   "can view own profile"
   match?  No
   ↓
Decision: Denied
   ↓
Response: 403 Forbidden
   {
     "error": "Access denied"
   }
   ↓
Frontend shows: "You do not have access"
```

Now the admin case:

```text
Admin logs in successfully  →  admin 1, proof given
   ↓
Admin sends:  GET /admin/users
   ↓
Backend: Authentication
   proof valid?  Yes  →  user is "1"
   ↓
Backend: Authorization
   resource:    GET /admin/users
   required:   "can view all users"
   user has:   "can view all users"
   match?  Yes
   ↓
Decision: Granted
   ↓
Backend runs Business Logic
   ↓
Database returns the list of all users
   ↓
Response: 200 OK  [ list of users ]
```

Notice the important point:
Both users passed **Authentication**.
Only the admin passed **Authorization**.
Same endpoint, different results — because of permissions, not identity.

---

## 6. Failure and Limits

### Why not every user should access everything

- To protect other users' private data.
- To keep dangerous actions (delete, update, change roles) in the right hands.
- To let the system behave differently for different roles.

### What should Authorization do?

- Run **after** Authentication.
- Check permissions on every protected Request.
- Return a clear **denied** answer when the user is not allowed.
- Stay the single place where "can I do this?" is decided.

### What should NOT happen?

- **Do not skip Authorization because the user is logged in.** Authenticated is not the same as allowed.
- **Do not hide Authorization inside the Business Logic.** Keep it as a separate, clear gate.
- **Do not return data first and check permissions later.** Check first, then act.

### What can fail?

- **Permission missing** → denied → 403 Forbidden.
- **Permission check is wrong** → a user can do something they should not. This is a security bug.
- **Authorization is skipped** → any logged-in user can do everything.
- **Authorization is too slow** → every Request pays a cost. (We will see how to speed this up later.)

### Status codes — know the difference

| Code | Meaning           | When                              |
| ---- | ----------------- | --------------------------------- |
| 401  | Unauthorized      | Authentication failed — *who are you?* |
| 403  | Forbidden         | Authorization failed — *you cannot do this* |

A common mistake is to mix these two up:
- **401** = "I do not know you."
- **403** = "I know you, but you may not."

### When do we not need Authorization?

For public content, there is nothing to protect, so there is no Authorization step.
For anything personal or sensitive, it is required.

### What Authorization does NOT solve

Authorization decides what a user **can** do.
It does **not** make the system **fast**.
Every Request still goes to the Database.
As users grow, the Database does more and more repeated work.
That is the next problem.

---

## 7. System Position

Authorization is a **block inside the Backend**.
It sits right after Authentication, before the Business Logic.

```text
HTTP Request
  ↓
Routing
  ↓
Authentication
  ↓
Authorization  ← this block
  ↓
Business Logic
  ↓
Data Access
```

Type:

- It is an **Application-side block**.
- It runs inside the Backend.
- It protects every endpoint that needs a permission.

---

## Common Technologies

Authorization is commonly implemented in different ways.
We do not compare them here.
They all solve the same architectural problem: **deciding whether a user can perform a specific action**.

- **Built-in framework authorization** — the tools that come inside Backend frameworks.
- **RBAC systems** — role-based access control.
- **ABAC systems** — attribute-based access control.
- **Policy-based authorization** — rules written as policies.
- **OpenFGA** — an external service for authorization decisions.
- **Oso** — a policy-based authorization engine.

Some teams build their own checks.
Some teams use a ready service.
In both cases, the role is the same: answer the question **"What are you allowed to do?"**

---

## 8. Key Takeaways

* Authorization answers one question: **What are you allowed to do?**
* **Authentication** is *who you are*. **Authorization** is *what you can do*.
- A user can pass Authentication but fail Authorization. Logged in does not mean allowed.
- **Identity** is knowing the user. **Permissions** are what the user is allowed to do.
- Authorization runs **after** Authentication and **before** the Business Logic.
- **401** = unknown user. **403** = known but not allowed.

---

## 9. Next Block

```text
Current Solution
      ↓
The Backend can now:
- Receive Requests
- Read and write data
- Identify users (Authentication)
- Check permissions (Authorization)
Everything works correctly.
      ↓
Remaining Problem
      ↓
Every Request still reads from the Database.
As the number of users grows, the Database
becomes slower and gets more repeated queries.
How can we reduce unnecessary Database access
and improve performance?
      ↓
Next Block
      ↓
Cache and Redis
```

[Cache and Redis](./09_cache_redis.md)