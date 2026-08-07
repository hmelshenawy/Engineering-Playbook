# HTTP and API

> This file continues from [03 - Frontend](./03_frontend.md).
>
> In the last chapter the Frontend prepared a clean Request.
> But the Request was still just data on the user's device.
>
> Now we answer the next question:
> **How does that Request actually travel to the Backend?**
> **How do two applications talk to each other over the network?**

---

## 1. Overview

### Why do applications need APIs?

The Frontend and the Backend are two separate programs.
They run on two different machines.
They do not share memory or files.
So they need a way to **send messages** to each other.

But they cannot just send any message in any shape.
They need a **shared language** and a **shared place** to send it to.
That shared agreement is called an **API**.

```text
Frontend  ──?──  Backend
            ↑
     they need rules to talk
```

### What is an API?

API means **Application Programming Interface**.
It is the list of things one program allows another program to ask for.

Think of a restaurant.
The menu is the API.
You cannot order something that is not on the menu.
The kitchen (the Backend) decides what it offers.
You (the Frontend) pick from the list.

```text
Menu (API)
├── Login
├── Get Profile
├── Update Profile
└── Logout
```

Each item on the menu is one **Endpoint**.

### What is HTTP?

HTTP is the set of rules for how these messages look.
It is the language the Frontend and Backend use to talk over the network.
That is all it is here — a language.

We will not go deep into the HTTP protocol.
We only need to understand the shape of a Request and a Response.

### Client and Server

- The **Client** is the one who asks. Here, the Frontend is the client.
- The **Server** is the one who answers. Here, the Backend is the server.

```text
Client (asks)  →  Server (answers)
 Frontend            Backend
```

The same machine can be a client in one moment and a server in another.
What matters is the role in this one conversation.

### Where does this sit in the system?

HTTP and API are not a block by themselves.
They are the **road** between the Frontend and the Backend.
They are how the Request crosses from the user's device to the server.

```text
Frontend
  ↓ HTTP (the road)
Backend
```

---

## 2. Inside

A conversation over HTTP has two parts: a **Request** and a **Response**.
Let us look at what is inside each, at a high level only.

example CRUD: create - read - update - delete
```text
HTTP Request
├── Method       (what to do)
├── URL          (where to do it)
├── Headers      (extra info)
└── Body         (the data)

HTTP Response
├── Status Code  (did it work?)
├── Headers      (extra info)
└── Body         (the result)
```

### The HTTP Request

#### Method

The Method tells the Backend **what kind of action** the client wants.
There are a few common ones:

| Method   | Meaning              | Login example?            |
| -------- | -------------------- | ------------------------- |
| GET      | Read something       | No — we send no password  |
| POST     | Create or send something | Yes — we send login data |
| PUT      | Replace something    | No                        |
| PATCH    | Update part of something | No                   |
| DELETE   | Remove something     | No                        |

For Login we use **POST**, because we are **sending** data to the Backend.

#### URL and Endpoint

The URL is the address.
It tells the Backend **where** the request should go.

An **Endpoint** is one specific address that does one specific job.
For Login, the endpoint is often `/login`.

```
https://api.example.com/login
                     └─────┘
                      endpoint
```

#### Headers

Headers are small pieces of extra information attached to the Request.
They describe things like: what kind of data is inside, or what language the user wants.
We keep this high level. We do not need every header type now.

#### Request Body

The Body is the **actual data** we are sending.
For Login, the body contains the email and password.

### The HTTP Response

#### Status Code

The Status Code is a short number that says **what happened**.
It is the Backend's first answer before the real data.

Rough groups:

| Range     | Meaning             | Example            |
| --------- | ------------------- | ------------------ |
| 2xx       | It worked           | 200 OK             |
| 4xx       | The client made a mistake | 400 Bad Request |
| 5xx       | The server had a problem | 500 Server Error |

For a successful login we expect **200**.

#### Response Body

The Response Body is the **result** the Backend sends back.
For Login it might be a success message and a token.

### JSON — the common data format

Both the Request Body and the Response Body are usually written in **JSON**.
JSON is just a simple way to write data as text, using keys and values.

```json
{
  "email": "user@example.com",
  "password": "123456"
}
```

JSON is popular because it is simple, and both the Frontend and the Backend can read it easily.
We will use it in every example.

---

## 3. Connections

| From → To            | Direction | Method | Sync/Async | Purpose                  |
| -------------------- | --------- | ------ | ---------- | ------------------------ |
| Frontend → Backend   | Outgoing  | HTTP   | Sync       | Send the Request          |
| Backend → Frontend   | Incoming  | HTTP   | Sync       | Send the Response         |

The communication here is **Sync**.
The Frontend sends a Request and **waits** for the Response.
It cannot continue until the answer comes back.

---

## 4. Main Flow

Let us follow the Login Request across the network.

```text
Frontend
      │
      │ HTTP Request  (POST /login + body)
      ▼
Backend
      │
      │ checks the credentials
      │
      ▼
HTTP Response  (200 OK + token)
      │
      ▼
Frontend
```

Step by step:

### 1. The Frontend builds the Request

The Frontend takes the email and password and puts them in JSON.
It chooses the Method: **POST**.
It chooses the Endpoint: **/login**.

### 2. The Request travels over HTTP

The Request leaves the user's device.
It crosses the network using HTTP rules.
It arrives at the Backend.

### 3. The Backend reads the Request

The Backend looks at the Method, the Endpoint, and the Body.
It understands: "the client wants to log in, and here is the data."

### 4. The Backend does its job

The Backend checks the credentials.
(We do not look inside the Backend here. That is the next chapter.)

### 5. The Backend builds the Response

The Backend creates a Response.
It picks a Status Code: **200** for success.
It puts the result in the Body as JSON — usually a token.

### 6. The Response travels back

The Response goes back over HTTP to the Frontend.
The Frontend reads the Status Code and the Body.

---

## 5. Practical Example

Here is the Login example written out fully, exactly as it looks over the wire.

### The Request

```text
POST /login
Headers:
  Content-Type: application/json

Body (JSON):
{
  "email": "user@example.com",
  "password": "123456"
}
```

Read it in plain words:
- **POST** → I am sending data.
- **/login** → to the login endpoint.
- **Body** → here is the email and password, in JSON.

### The journey

```text
Frontend
   │
   │  POST /login
   │  { email, password }
   ▼
Backend  →  validate credentials
   │
   │  200 OK
   │  { "token": "abc123" }
   ▼
Frontend
```

### The Response

```text
Status: 200 OK
Headers:
  Content-Type: application/json

Body (JSON):
{
  "success": true,
  "token": "abc123"
}
```

Read it in plain words:
- **200 OK** → it worked.
- **Body** → here is the proof, a token, in JSON.

### What if the login fails?

If the password is wrong, the Backend returns a different answer:

```text
Status: 401 Unauthorized
Body (JSON):
{
  "success": false,
  "error": "Wrong email or password"
}
```

The Frontend reads the **401**, knows it failed, and shows the error to the user.
No token is saved.

This is the whole pattern:
- The Frontend sends a Request.
- The Backend sends a Response with a Status Code.
- The Status Code tells the Frontend what to do next.

---

## 6. Failure and Limits

### What can fail?

- **Network problem:** the Request never reaches the Backend. The Frontend shows a timeout or network error.
- **Wrong endpoint:** the Backend does not know this address → 404 Not Found.
- **Bad data:** the Backend rejects the Request → 400 Bad Request.
- **Bad credentials:** the password is wrong → 401 Unauthorized.
- **Server crash:** the Backend is broken → 500 Server Error.

### How is it handled?

The Status Code is the key.
The Frontend checks the code before it reads the body.
A 2xx means success. A 4xx or 5xx means something went wrong.

### When do we not need HTTP?

HTTP is great for request-and-response conversations.
It is not the only way to talk.
Some systems need a continuous, two-way connection (like live chat).
For those, other styles are better — but those get their own chapters later.

### What problem is still left?

The Request reached the Backend.
The Backend read the Method, the Endpoint, and the Body.
But what happens **inside** the Backend after that?
How does it actually check the credentials?
How does it produce that Response?

---

## 7. System Position

HTTP and API are not a Module, not a Service, and not Infrastructure.
They are the **communication layer** between the Frontend and the Backend.

```text
Frontend
  ↓ HTTP / API  ← this chapter
Backend
  ↓
Database
```

Type:

- It is the **Communication Layer**.
- It is the rule set, not a block by itself.
- Both sides must agree on it, or they cannot talk.

---

## Common Technologies

These are common tools used to **design, document, and test** APIs.
We do not compare them. We just list what you will see in real projects.

- **REST APIs** — the most common style of building APIs over HTTP. The examples in this chapter are REST.
- **OpenAPI / Swagger** — a way to describe an API in a file, so the API is documented and predictable.
- **Postman** — a popular tool to send requests and see responses while you build and test an API.
- **Insomnia** — another tool for the same job: send a Request, check the Response.

These tools help humans work with APIs.
They do not change how the API works — they just make it easier to design, document, and test.

---

## 8. Key Takeaways

* Two applications need a **shared language** to talk. That language is HTTP, and the shared menu is the API.
* A **Request** has: Method, URL, Headers, Body. A **Response** has: Status Code, Headers, Body.
* Common Methods: **GET, POST, PUT, PATCH, DELETE**. Login uses **POST**.
* An **Endpoint** is one address that does one job. Login is often `/login`.
* **JSON** is the common data format for the Body, on both sides.
* The **Status Code** tells the Frontend what happened — 2xx success, 4xx client mistake, 5xx server problem.
* The communication is **Sync**: the Frontend waits for the Response.

---

## 9. Next Block

```text
Current Solution
      ↓
The Frontend built a clean Request,
sent it over HTTP to an Endpoint,
and received a Response with a Status Code.
      ↓
Remaining Problem
      ↓
The Request reached the Backend.
The Backend read the Method, the Endpoint,
and the Body.
But what happens inside the Backend
after it receives the Request?
How does it check the credentials and
produce that Response?
      ↓
Next Block
      ↓
Backend
```

[Backend](./05_backend.md)