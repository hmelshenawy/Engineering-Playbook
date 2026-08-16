# Kubernetes

> This file continues from [19 - Docker](./19_docker.md).
>
> Docker packages applications into containers.
> But our system now has **many** containers: Frontend, Backend, Database, Cache, Workers.
>
> Running one container is easy. Running **hundreds** across many servers is hard.
> The fix is **Kubernetes**.

---

## 1. Overview

Kubernetes is a tool that **manages many containers across many servers**.
You tell it what you want running. It keeps it running.

```text
Kubernetes
├── Backend Container
├── Backend Container
├── Worker Container
└── Cache Container
```

It does not replace Docker.
Docker runs **one** container. Kubernetes manages **many**.

---

## 2. Why

With many containers, hard questions appear:

- Which server should run each container?
- What happens if a container crashes?
- How do we add more containers when traffic grows?
- How do we update the app without downtime?

```text
1 container   →  easy, do it by hand
100 containers →  impossible by hand
```

Doing this manually is almost impossible.
Kubernetes automates it.

---

## 3. Container Orchestration

This idea has a name: **container orchestration**.
Orchestration means **coordinating many containers automatically**.

```text
Orchestration = decide + place + monitor + fix + scale
```

- **Decide** what should run.
- **Place** each container on a server.
- **Monitor** them.
- **Fix** them when they fail.
- **Scale** them when traffic changes.

Kubernetes is the most common container orchestrator.

---

## 4. Managing Many Containers

A real system is not one container.
It is a group of containers that must work together.

```text
Frontend container
Backend container
Database container
Cache container
Worker container
```

Kubernetes manages the **whole group** as one system.
You describe the group. Kubernetes keeps it alive.

---

## 5. Automatic Deployment

Without Kubernetes, deploying means:
- Pick a server.
- Start the container.
- Connect it to the others.
- Repeat for every container.

With Kubernetes, you describe what you want, and it **places and starts** the containers for you.

```text
You:      "I want 3 Backend containers running"
Kubernetes: starts them on the right servers
```

---

## 6. Automatic Restart (Self-Healing)

Containers crash. It is normal.
The question is: **how fast does the system recover?**

```text
Container crashes
   ↓
Kubernetes notices
   ↓
Starts a replacement automatically
   ↓
Application continues running
```

This is called **self-healing**.
The system fixes itself, without a human.

---

## 7. Automatic Scaling

Traffic changes during the day.
More users → more work. Fewer users → less work.

```text
Traffic increases
   ↓
Kubernetes creates more containers
   ↓
Traffic decreases
   ↓
Kubernetes removes extra containers
```

The system grows and shrinks **by itself**, based on load.

---

## 8. High Availability

With Kubernetes, no single container is critical.
If one dies, another takes its place.

```text
Container A crashes → Kubernetes starts Container A'
Container B crashes → Kubernetes starts Container B'
```

The application stays **available** even when pieces fail.
This is **High Availability** — the system keeps running.

---

## 9. Connections

| From → To                  | Purpose                        |
| -------------------------- | ------------------------------ |
| Load Balancer → Kubernetes  | Send traffic to the cluster     |
| Kubernetes → Containers     | Run and manage them             |
| Kubernetes → (replace)      | Restart crashed containers      |
| Kubernetes → (scale)        | Add or remove containers        |

Kubernetes is the **manager** standing in front of all the containers.

```text
Users
   ↓
Load Balancer
   ↓
Kubernetes
├── Backend Container
├── Backend Container
├── Worker Container
└── Cache Container
```

---

## 10. Practical Example

An online store runs: Frontend, Backend, Database, Cache, Workers.

**Without Kubernetes:**

```text
Traffic spikes
   ↓
Developers manually start more containers
   ↓
A container crashes
   ↓
Someone must notice and restart it
```

Everything depends on a human watching.

**With Kubernetes:**

```text
Traffic spikes
   ↓
Kubernetes creates more containers automatically
   ↓
A container crashes
   ↓
Kubernetes detects the failure
   ↓
Starts a replacement automatically
   ↓
The store keeps running
```

The same store, but it now **runs itself** — deploys, restarts, and scales without a human.

---

## 11. Failure and Limits

- **Kubernetes itself down** — the manager is gone, so containers stop being managed. (In real systems, the manager is duplicated.)
- **Too complex for small apps** — Kubernetes adds overhead. For a tiny app, it may be overkill.
- **Wrong settings** — a bad description can cause Kubernetes to do the wrong thing automatically — and fast.

Kubernetes is useful when there are **many containers** and **many servers**.
For one or two containers, Docker alone is enough.

---

## 12. System Position

Kubernetes is an **Infrastructure Component** — the **orchestration layer** on top of containers.

```text
Application → Docker (package) → Kubernetes (manage many) → Servers
```

Type:

- It is the **management layer** of containers.
- It does **not** run the business logic — it **keeps the containers alive**.

---

## Common Technologies

These platforms are all based on the Kubernetes ecosystem.

- **Kubernetes (K8s)**
- **Amazon EKS**
- **Azure Kubernetes Service (AKS)**
- **Google Kubernetes Engine (GKE)**
- **Red Hat OpenShift**

They differ in hosting and extras, but the architectural role is the same: **orchestrate many containers automatically**.

---

## 13. Key Takeaways

* Kubernetes **manages many containers across many servers**.
* This idea is called **container orchestration**.
* It handles **automatic deployment, automatic restart, and automatic scaling**.
* **Self-healing** means it replaces crashed containers by itself.
* It gives **High Availability** — the system keeps running when pieces fail.
* **Docker runs one container. Kubernetes manages many.**

---

## 14. Bringing It All Together

This chapter completes the first stage of the journey.
You now know the major building blocks of a modern software system.

Here is how they fit together — from a user click to a running, self-healing system:

```text
User
  ↓
Frontend              (collects input, sends Request)
  ↓ HTTP
API Gateway           (single entry point)
  ↓
Load Balancer         (spreads traffic)
  ↓
Backend               (routes, validates, runs Business Logic)
  ├── Authentication   (who are you?)
  ├── Authorization    (what can you do?)
  ├── Database         (permanent data)
  ├── Cache             (fast reads)
  ├── File Storage      (large files)
  └── Search            (fast finding)
  ↓
Message Queue         (slow work goes to background)
  ↓
Workers               (do the heavy jobs)
  ↓
Event Bus             (broadcast "something happened")
  ↓
Logging + Monitoring  (what happened + what is happening now)
  ↓
Docker                (package each block)
  ↓
Kubernetes            (run and manage all containers)
```

One **Request** travels through these blocks.
Each block has **one responsibility**.
Together, they build a complete **production system** — fast, reliable, secure, and able to grow.

You now have the **map**.
In the chapters that follow, we will open each box and go deeper — the concepts, the patterns, and the real tools behind every block.

The journey does not end here. It just **begins**.