# Authentication

> This file continues from [06 - Database](./06_database.md).
>
> In the last chapter the Backend could read and write data.
> The Database remembers the users and their passwords.
>
> But a new problem appeared:
> **Can anyone send a Request and access that data?**
> **How does the system know who the user really is?**
>
> The answer is **Authentication**. We open this box now.

---

## 1. Overview

### What is Authentication?

Authentication is the process of **proving who the user is**.

It answers one single question:

> **Who are you?**

```text
Request comes in
      ↓
Authentication
      ↓
Who is this? → user@example.com
```

If the answer is "we know this person," the Request is **authenticated**.
If the answer is "we do not know you," the Request is **rejected**.

### Why do we need Authentication?

The Backend receives Requests from the network.
Anyone on the network can send a Request.
A stranger can send a Request that says "delete user number 42."
The Backend cannot just trust every Request.

```text
Stranger  →  "I am user 42, give me my account"
Backend   →  "How do I know that?"
```

The Backend needs proof.
That proof is what Authentication provides.

### Identity vs Authentication

These two words are easy to mix up.

- **Identity** is *who* the user is. Example: "this is user number 42."
- **Authentication** is *proving* the identity. Example: "this person really is user 42, here is the proof."

```text
Identity:        "I am Alice."
Authentication:  "Prove it."  →  proof shown  →  "OK, I believe you."
```

A user can claim any identity.
Authentication is what makes the claim believable.

### Anonymous vs Authenticated

A Request can be in one of two states:

| State          | Meaning                                | Login example                |
| -------------- | -------------------------------------- | ---------------------------- |
| Anonymous      | We do not know who sent it              | First login attempt          |
| Authenticated  | We have proven who sent it              | After a successful login     |

An **anonymous** Request is not necessarily bad.
A login Request is anonymous — the user is *trying* to prove who they are.
But most other endpoints (like "get my profile") require an **authenticated** Request.

### Where does it sit?

Authentication sits inside the Backend, right at the entry.
It is one of the first things the Backend checks after Routing.

```text
HTTP Request
  ↓
Routing
  ↓
Authentication  ← you are here
  ↓
Business Logic
```

---

## 2. Inside

Authentication is not one step.
It has a small flow of its own.

```text
Login Request
├── Credentials      (what the user sends to prove identity)
├── Check            (compare with stored data)
├── Result           (success or failure)
└── Proof            (something to use in future Requests)
```

### Credentials

Credentials are the data the user sends to prove who they are.
For Login, the credentials are usually an **email** and a **password**.

### Check

The Backend compares the credentials with what is stored in the Database.
- Does this user exist?
- Does the password match?

### Result

The Check gives one of two answers:
- **Success** → the identity is proven.
- **Failure** → the identity is not proven.

### Proof

After a successful login, the Backend gives the user **proof**.
This proof is sent with every future Request.
We will not explain the proof mechanism here (things like tokens or sessions get their own chapters).
We only need to know: the user carries something that says "I already proved who I am."

---

## 3. Connections

| From → To                    | Direction | Method | Sync/Async | Purpose                  |
| ---------------------------- | --------- | ------ | ---------- | ------------------------ |
| Frontend → Backend            | Incoming  | HTTP   | Sync       | Send credentials          |
| Backend → Database            | Outgoing  | Query  | Sync       | Read stored user data     |
| Database → Backend            | Incoming  | Result | Sync       | Return the user record    |
| Backend → Frontend            | Outgoing  | HTTP   | Sync       | Return proof on success   |

A key point: Authentication is **Sync**.
The Backend must know the result before it can continue.

```text
Frontend  →  credentials
Backend   →  check against Database
Database  →  returns stored data
Backend   →  success + proof
Frontend
```

---

## 4. Main Flow

Let us follow the Login example all the way through Authentication.

```text
User
  ↓
Frontend
  ↓
POST /login  (email + password)
  ↓
Backend
  ↓
Check credentials
  ↓
Database  → find the user
  ↓
User found
  ↓
Authentication succeeds
  ↓
Backend gives proof to the user
  ↓
Future Requests are authenticated
```

Step by step:

### 1. The user sends credentials

The user types their email and password.
The Frontend sends them to the Backend as `POST /login`.

### 2. The Backend receives the Request

Routing sends the Request to the login handler.
Validation checks that the email and password are present.

### 3. The Backend checks the Database

The Backend asks the Database: "do you have a user with this email?"
The Database returns the stored user record — including the saved password.

### 4. The Backend compares

The Backend compares the password from the Request with the stored password.
We will not explain *how* the comparison works (password hashing gets its own chapter).
We only care about the result: **match** or **no match**.

### 5. Authentication succeeds or fails

```text
       Valid User?
       ├── Yes → Continue
       └── No  → Reject Request
```

- If they match → Authentication succeeds.
- If they do not match → Authentication fails.

### 6. On success, the user becomes authenticated

After success, the Backend gives the user a **proof**.
From this moment on, the user is no longer anonymous.
They are an **authenticated user**.

### 7. Future Requests use the proof

For every later Request, the Frontend sends the proof along with the Request.
The Backend checks the proof instead of asking for the password again.

---

## 5. Practical Example

Here is the Login example traced through Authentication:

```text
POST /login  { email: "user@example.com", password: "123456" }
   ↓
Backend: "do I know this user?"
   ↓
Database: find user with email = user@example.com
   ↓
Database returns:
   {
     id: 42,
     email: "user@example.com",
     passwordHash: "$2b$10$..."
   }
   ↓
Backend: does password "123456" match the stored password?
   ↓
Match? Yes
   ↓
Authentication succeeds
   ↓
Backend returns proof (for example, a token)
   ↓
From now on, Requests carry this proof → authenticated
```

And the failure case:

```text
POST /login  { email: "user@example.com", password: "wrong" }
   ↓
Backend: "do I know this user?"
   ↓
Database: find user with email = user@example.com
   ↓
Database returns the user record
   ↓
Backend: does password "wrong" match the stored password?
   ↓
Match? No
   ↓
Authentication fails
   ↓
Backend returns 401 Unauthorized
   ↓
The user stays anonymous
```

Notice the difference:
- A **missing user** and a **wrong password** can both fail.
- From the outside, the Backend often returns the same error ("wrong email or password") so strangers cannot guess which one is wrong.

### What happens after a successful login

After success, the user is authenticated.
Their future Requests look different:

```text
Before login:  Request → "who are you?"
After login:   Request → "I am user 42, here is my proof"
```

The Backend no longer asks for the password.
It only checks the proof.
This is how one login protects many future Requests.

---

## 6. Failure and Limits

### Why the Backend cannot trust every Request

A Request is just a message on a network.
Anyone can send one.
A stranger can put any email in a Request.
Without Authentication, the Backend would have to believe every claim.

That is why Authentication is mandatory for anything sensitive.

### Why users should prove their identity

- To protect their own account from strangers.
- To let the system connect actions to the right person.
- To make the data answerable to a real user.

### What can fail?

- **Wrong credentials** → Authentication fails → 401.
- **User does not exist** → Authentication fails → 401.
- **Database is down** → the Backend cannot check → the Request fails (often 500).
- **Proof is missing or fake** → future Requests are rejected as anonymous.

### How is it handled?

Every failure becomes a clean Response with a Status Code.
The user is told "not allowed" without the system leaking *why*.

### When do we not need Authentication?

Public content — like a public home page or a public product list — does not need Authentication.
Anything personal or sensitive does.

### What Authentication does NOT do

Authentication only answers **"who are you?"**
It does **not** answer **"what are you allowed to do?"**
A logged-in user is known — but that does not mean they can do everything.

This is the next problem.

---

## 7. System Position

Authentication is a **block inside the Backend**, not a separate service by itself.
It sits at the entry of the request flow, before the Business Logic.

```text
HTTP Request
  ↓
Routing
  ↓
Authentication  ← this block
  ↓
Business Logic
  ↓
Data Access
```

Type:

- It is an **Application-side block**.
- It runs inside the Backend.
- It protects every endpoint that needs to know the user.

---

## Common Technologies

Authentication can be implemented using different technologies.
We do not compare them here.
They all solve the same architectural problem: **verifying user identity**.

- **Auth0** — an external authentication service.
- **Keycloak** — an open-source authentication and identity server.
- **Clerk** — a hosted authentication service.
- **Firebase Authentication** — Google's authentication service.
- **AWS Cognito** — Amazon's authentication service.
- **Custom Authentication Systems** — built by the team inside the Backend itself.

Some teams build their own Authentication.
Some teams use a ready service.
In both cases, the role is the same: answer the question **"who are you?"**

---

## 8. Key Takeaways

* Authentication answers one question: **Who are you?**
* **Identity** is the claim. **Authentication** is the proof of the claim.
- A Request is either **anonymous** (we do not know the user) or **authenticated** (we have proof).
- The Backend **cannot trust every Request**. Users must prove who they are.
- On success, the user gets a **proof** that protects future Requests.
- Authentication does **not** decide what the user is allowed to do — it only identifies them.

---

## 9. Next Block

```text
Current Solution
      ↓
The system now knows who the user is.
Authentication proves the identity on login,
and gives the user proof for future Requests.
      ↓
Remaining Problem
      ↓
Now the system knows who you are.
But should every authenticated user
be allowed to perform every action?
Can a normal user delete another account?
How does the system decide what
a user is allowed to do?
      ↓
Next Block
      ↓
Authorization
```

[Authorization](./08_authorization.md)