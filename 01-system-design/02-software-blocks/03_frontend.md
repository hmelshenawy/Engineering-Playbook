# Frontend

> This file continues from [02 - Request Lifecycle](./02_request_lifecycle.md).
>
> In the last chapter we followed a request from the first click to the final response.
> We treated every block as a black box.
>
> Now we open the first black box: the **Frontend**.
> We want to see what happens inside it *before* the request is sent.

---

## 1. Overview

### What is the Frontend?

The Frontend is the part of the system the user can **see and touch**.
It is the screen, the buttons, the forms, the text, the images.
It runs inside the user's device — usually inside a **Browser**.

```text
User
  ↓
Frontend  ← you are here
  ↓
Backend
```

The Frontend is the bridge between the human and the machine.
It takes human actions, like clicks and typing, and turns them into something the backend can understand.

### Why do we need it?

The backend speaks in data and rules.
The user speaks in clicks, forms, and screens.
They do not understand each other directly.

We need a translator in the middle.
That translator is the Frontend.

```text
Human language      Machine language
(clicks, forms)  →   (requests, data)
        \           /
         \         /
        Frontend
```

### Where does it sit?

The Frontend is the first block on the request path.
Every request starts here.
It is the entry point of the whole system from the user's side.

---

## 2. Inside

The Frontend is not one big thing.
It is made of a few smaller building blocks.
Each one has a clear job.

```text
Frontend
├── UI                  (what the user sees)
├── User Interaction    (catches clicks and typing)
├── Validation          (checks input before sending)
├── State               (remembers things on the client)
└── API Communication   (talks to the backend)
```

Let us look at each one at a high level.
We will not go deep — each of these gets its own chapter later.

### UI

UI means **User Interface**.
It is what appears on the screen: buttons, inputs, lists, menus.
Its only job is to **show** things to the user and **collect** the user's input.

### User Interaction

When the user clicks or types, something must catch that action.
This block listens for those events and decides what to do next.
A click on "Login" is meaningless until something turns it into a real action.

### Validation

Before we send data to the backend, we should check it.
Is the email empty? Is the password too short?
This is **client-side validation** — checks that happen on the user's device.

### State

The Frontend needs to **remember** things while the user is on the page.
Which tab is open. Is the user logged in. What is in the cart.
This memory is called **State**.
We keep it high level here. State management is a big topic on its own.

### API Communication

At some point the Frontend must talk to the backend.
This block builds the request, sends it, and waits for the response.
It is the door to the outside world.

---

## 3. Connections

The Frontend mostly connects to one thing: the Backend.
But inside, its own blocks connect to each other in a small flow.

| From → To                  | Direction | Method | Sync/Async | Purpose                  |
| -------------------------- | --------- | ------ | ---------- | ------------------------ |
| User → UI                   | Outgoing  | Click  | Sync       | Give input                |
| User Interaction → Validation | Internal | Call | Sync       | Check the input           |
| Validation → API Communication | Internal | Call | Sync     | Send if valid             |
| API Communication → Backend  | Outgoing  | HTTP   | Sync       | Send the request          |
| Backend → API Communication  | Incoming  | HTTP   | Sync       | Receive the response      |
| API Communication → State    | Internal  | Update | Sync       | Save the result           |
| State → UI                   | Internal  | Render | Sync       | Show the new screen       |

One rule to remember: the Frontend is **Sync** with the backend.
It sends a request and **waits** for the answer before it can continue.

---

## 4. Main Flow

Let us follow the Login scenario again — but this time we zoom **inside** the Frontend.
We stop the moment before the request leaves the device.

```text
User clicks Login
   ↓
UI catches the click
   ↓
User Interaction reads the form
   ↓
Validation checks email + password
   ↓
API Communication builds the Request
   ↓
Request leaves the device → Backend
```

Step by step:

### 1. The user clicks Login

The user fills the form and clicks the **Login** button.
This is the spark. Nothing happens without it.

### 2. The UI catches the click

The UI was listening.
It catches the click event and passes control to the next block.

### 3. User Interaction reads the form

This block reads the values the user typed: the **email** and the **password**.
It collects them in one place so the rest of the Frontend can use them.

### 4. Validation checks the input

Before we bother the backend, we check the basics:

- Is the email empty? → stop, show an error.
- Is the password too short? → stop, show an error.

This is **client-side validation**.
It is not for security. The backend will check again later.
It is for **speed and friendliness** — the user gets fast feedback without waiting for the network.

### 5. API Communication builds the Request

If the input is valid, this block builds a clean **Request**.
It puts the email and password inside.
It adds the information the backend needs, like where to send the answer.

### 6. The Request leaves the device

The request is now ready.
It leaves the user's device and travels to the backend.

Notice where we stopped.
We did not explain *how* the request travels.
That is the next chapter.

---

## 5. Practical Example

Let us trace the Login scenario inside the Frontend:

```text
The user types email + password
   ↓
Clicks "Login"
   ↓
UI catches the click
   ↓
User Interaction reads:
    email: "user@example.com"
    password: "1234"
   ↓
Validation:
    email empty?      No
    password too short? Yes (less than 6)
   ↓
Show error: "Password is too short"
   ↓
Stop. Do NOT send to the backend.
```

In this case the request never leaves the device.
The Frontend caught the problem early.
This saves a network trip and gives the user instant feedback.

Now the corrected case:

```text
The user types a valid email + a strong password
   ↓
Clicks "Login"
   ↓
Validation passes
   ↓
API Communication builds the Request
   ↓
Request sent to the Backend
   ↓
Wait for the Response...
   ↓
Response arrives: "200 OK + token"
   ↓
State saves: user is logged in
   ↓
UI shows: "Welcome back"
```

See the two parts clearly:
- **Before sending:** the Frontend prepares and checks.
- **After the response:** the Frontend saves State and renders the result.

---
## Common Technologies

This block can be implemented using different technologies.

Examples:

- Next.js
- React
- Angular
- Vue.js

## 6. Failure and Limits

### What should happen in the Frontend?

- Show the UI to the user.
- Collect user input.
- Do basic client-side validation.
- Build and send requests.
- Receive responses and render them.
- Keep simple State on the client.

### What should NOT happen in the Frontend?

- **No real security checks.** Anything on the user's device can be changed by the user. The backend must always check again.
- **No business rules.** Decisions like "can this user log in?" belong to the backend. The Frontend only shows the result.
- **No direct database access.** The Frontend never talks to the database. It only talks to the backend.
- **No secrets.** Passwords, keys, and private rules must not live in the Frontend.

### What can fail?

- The user might enter bad input. → Validation handles it.
- The backend might be slow or down. → The Frontend should show a loading state and an error, not freeze.
- The response might be rejected by the backend. → The Frontend shows the error message.

### When do we not need it?

Some systems have no user interface at all — for example, a service that only talks to other services.
Those systems have no Frontend in this sense.
But any system a human uses needs a Frontend.

### What problem is still left?

The Frontend has prepared the request.
But the request is still just data on the user's device.
How does it actually travel to another computer?
How do two applications talk to each other over the network?

---

## 7. System Position

The Frontend is a **Client-side block**.
It is not a Module inside the backend, not a Service, and not Infrastructure.
It is the user-facing entry point of the whole system.

```text
User
  ↓
Frontend  ← this block
  ↓ HTTP Request
Backend
  ↓
Database
```

Type:

- It is the **Client Layer**.
- It runs on the user's device.
- It talks to the backend over the network.

---

## 8. Key Takeaways

* The Frontend is the part the user **sees and touches**. It runs on the user's device.
* It translates human actions into clean **requests** for the backend.
* Its main blocks are: **UI, User Interaction, Validation, State, API Communication**.
* It does **basic validation** for speed, not for security — the backend always checks again.
* It must **not** hold business rules, secrets, or direct database access.
* It is **Sync** with the backend: it sends a request and waits for the answer.

---

## 9. Next Block

```text
Current Solution
      ↓
The Frontend collects the user input,
checks it, and builds a clean Request.
The Request is ready to go.
      ↓
Remaining Problem
      ↓
The Request is still just data on the user's device.
How does it actually travel to another computer?
How can two applications communicate
over the network?
      ↓
Next Block
      ↓
HTTP and API
```

[HTTP and API](./04_http_api.md)