# Docker

> This file continues from [18 - Monitoring](./18_monitoring.md).
>
> The application works perfectly on the developer's computer.
> But when it is deployed to another machine, problems appear: different operating systems, different library versions, missing dependencies, different runtimes.
>
> Developers often say: **"It works on my machine."**
>
> The fix is **Docker**.

---

## 1. Overview

Docker is a tool that **packages an application together with everything it needs to run**.

```text
Application + its dependencies  →  one package
```

That package can then run the **same way** on any machine — a laptop, a test server, or production.

---

## 2. Why

The classic problem is called **"works on my machine."**

- The developer builds the app. It works on their computer.
- It is moved to another machine. It fails.
- Why? A different OS, a missing library, a different runtime version.

```text
Developer PC:   works
Test server:    fails
Production:     fails differently
```

The app was fine. The **environment** was different.

Docker fixes this by shipping the **environment together with the app**.

---

## 3. Packaging an Application

Without Docker, the app is just code.
To run it, each machine must install the right language, the right libraries, and the right settings — by hand.

With Docker, the app is packaged **with** all of that.

```text
Package = code + libraries + runtime + settings
```

The machine no longer needs to be set up manually.
It only needs to run the package.

---

## 4. Application + Dependencies

A real application does not run alone.
It depends on many things:

- a specific language version
- system libraries
- configuration files
- environment settings

Docker puts the application **and** all these dependencies into one package.

```text
Application
   +
Dependencies
   ↓
Docker package
```

If the app needs library Y, the package carries library Y.
The machine does not need to have it.

---

## 5. Running the Same Application Everywhere

Once the package is built, it runs the same way **everywhere**.

```text
Developer PC
      │
Docker Image
      │
      ├── Test Server
      ├── Production Server
      └── Cloud
```

Same package. Same behavior. No surprises.

```text
Developer laptop  →  same package
Test server        →  same package
Production         →  same package
```

---

## 6. Containers (High Level)

The package has two forms, and the words are easy to mix up:

| Term       | What it is                       |
| ---------- | -------------------------------- |
| Image      | A saved package (a template)     |
| Container  | A running instance of an image   |

```text
Application
   │
   ▼
Docker Image    (stored, not running)
   │ run
   ▼
Container       (running, alive)
```

Think of an **image** as a recipe, and a **container** as the cooked dish.
You can cook the same dish many times from one recipe.

We keep this high level. How images are built gets its own chapter.

---

## 7. Why Docker Simplifies Deployment

Before Docker, deploying meant:

- Install the right OS.
- Install the right language.
- Install the right libraries.
- Configure the settings.
- Hope it matches the developer's machine.

After Docker, deploying means:

- Run the image.

```text
Before:  set up the machine to fit the app
After:   the app already fits, just run it
```

That is why Docker makes deployment **simple and predictable**.

---

## 8. Connections

| From → To            | Purpose                        |
| -------------------- | ------------------------------ |
| Developer → Image     | Build the package once          |
| Image → Container     | Run it anywhere                 |
| Container → (the app) | The app runs inside it          |

The container is the **new home** of the application.
The app no longer runs "on the machine" — it runs **inside** the container.

```text
machine → container → application
```

---

## 9. Simple Flow

```text
Write code
   ↓
Build Image  (code + dependencies + settings)
   ↓
Run Container  (on developer's PC)
   ↓
Same Image  →  Run Container  (on test server)
   ↓
Same Image  →  Run Container  (in production)
```

One image. Many places. Same behavior.

---

## 10. Practical Example

A developer builds a Backend application.

**Without Docker:**

```text
Developer PC
   ↓
Works

Production Server
   ↓
Fails — missing dependencies, different OS, different runtime
```

Each machine is set up by hand. Each one is slightly different.

**With Docker:**

```text
Developer builds one Docker image
   (Backend code + dependencies + settings)
   ↓
The same image runs on:
   ├── Developer machine   → works
   ├── Test server         → works
   └── Production server   → works
```

The environment travels **with** the app.
"It works on my machine" becomes "it works on **every** machine."

---

## 11. Failure and Limits

- **Container crashes** — the app inside stops. (Restarting it automatically is a later topic.)
- **Image too large** — slow to build and slow to move around.
- **Secrets inside the image** — never put passwords or keys in the image. Anyone with the image can read them.
- **Slower hardware still runs slower** — the container is consistent, but a weak machine is still weak.

Docker solves the **environment** problem.
It does not, by itself, manage many containers across many servers — that comes next.

---

## 12. System Position

Docker is an **Infrastructure Component** — it packages the application so it runs consistently.

```text
Application  →  packaged in  →  Image  →  runs as  →  Container  →  on  →  Server
```

Type:

- It is the **packaging layer** of the system.
- It does **not** run the business logic — it **carries** the app that does.

---

## Common Technologies

These tools allow applications to run inside containers.

- **Docker**
- **Docker Desktop**
- **Podman**

They differ in style, but the architectural role is the same: **package the app with its environment, run it consistently everywhere**.

---

## 13. Key Takeaways

* Docker packages an application **with everything it needs to run**.
* It solves the **"works on my machine"** problem — the environment travels with the app.
* The package is an **image**. A running instance is a **container**.
* The same image runs the same way on a laptop, a test server, and production.
* Deployment becomes simple: **run the image**, instead of setting up the machine.
* Docker packages the app. It does not, by itself, manage many containers across many servers.

---

## 14. Next Block

```text
Current Solution
      ↓
Docker makes it easy to package one
application and run it consistently
on any machine.
      ↓
Remaining Problem
      ↓
But what if we have:
- Hundreds of containers
- Multiple servers
- Automatic scaling
- Automatic recovery after failures
- Zero-downtime deployments
Managing all these containers manually
becomes difficult.
How can we automate container management?
      ↓
Next Block
      ↓
Kubernetes
```

[Kubernetes](./20_kubernetes.md)