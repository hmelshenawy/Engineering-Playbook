# Monitoring

> This file continues from [17 - Logging](./17_logging.md).
>
> Logs tell us **what happened** in the past.
> But what about **right now**?
> Is the app running? Is the CPU overloaded? Is memory running out? Are responses getting slower?
>
> We need a way to **continuously watch** the health of the system.
> The fix is **Monitoring**.

---

## 1. Overview

Monitoring is the practice of **watching the system while it runs**.
It collects numbers about the system and shows them in real time.

```text
Application
   ↓
Monitoring
├── CPU
├── Memory
├── Response Time
├── Errors
└── Availability
```

Logs look back. Monitoring looks **now**.

---

## 2. Why

Logs answer: "what happened?"
But some questions need a **live** answer:

- Is the application running?
- Is the CPU overloaded?
- Is memory running out?
- Are users getting many errors?
- Is the response time becoming slower?

```text
Logs:       "yesterday at 10:14, login failed"
Monitoring: "right now, CPU is at 95%"
```

Without monitoring, problems are noticed **only after users complain**.
With monitoring, problems are noticed **before** users complain.

---

## 3. System Health

System health is the **current state** of the application.

```text
Healthy:    fast responses, low errors, CPU fine
Unhealthy:  slow responses, high errors, CPU overloaded
```

Monitoring keeps an eye on this state, all the time.

---

## 4. Metrics

A **metric** is a single number that describes the system at a moment in time.

```text
CPU usage:        95%
Memory usage:     80%
Response time:    3000 ms
Error rate:       5%
Availability:     99.9%
```

One metric = one number.
Monitoring collects many metrics, over and over, so we can see **how the system changes**.

---

## 5. Dashboards

A dashboard is a **screen of charts** that shows the metrics.

```text
Metrics
   ↓
Dashboard
   ├── CPU chart
   ├── Memory chart
   ├── Response time chart
   └── Error rate chart
```

Instead of reading numbers, developers look at the dashboard and **see** the health at a glance.

---

## 6. Alerts

An **alert** is a message that fires when something looks wrong.

```text
CPU > 90%  →  alert!
Response time > 2000 ms  →  alert!
Error rate > 1%  →  alert!
```

```text
Application
   │
   ▼
Metrics
   │
   ▼
Dashboard
   │
   ▼
Alert  →  developer
```

The alert tells the developer: **"look now, before it gets worse."**

---

## 7. Detecting Problems Early

This is the main value of monitoring.

```text
Without monitoring:  users complain  →  then developers investigate
With monitoring:     alert fires      →  developers investigate before users notice
```

Monitoring turns surprises into **early warnings**.

---

## 8. Monitoring vs Logging

These two are easy to mix up. They work together, but they are different.

| Aspect      | Logging                  | Monitoring                  |
| ----------- | ------------------------ | --------------------------- |
| Time        | Past                     | Present (now)               |
| Form        | Text lines               | Numbers (metrics)           |
| Question    | What happened?           | What is happening?          |
| Use         | Debug a specific event   | Watch overall health        |

```text
Logs:       "login failed for user 42 at 10:14"
Monitoring: "error rate is 5% right now"
```

Logs explain a **single event**.
Monitoring shows the **big picture**.

---

## 9. Connections

| From → To            | Purpose                        |
| -------------------- | ------------------------------ |
| Application → Monitoring | Send metrics continuously      |
| Monitoring → Dashboard   | Show the numbers as charts      |
| Monitoring → Alert       | Notify when something is wrong  |
| Alert → Developer        | Wake someone up                 |

Monitoring is a **one-way** flow out of the application.
The app is watched. It does not get answers back.

```text
Application → metrics → Monitoring → { Dashboard , Alert }
```

---

## 10. Practical Example

A web application normally responds in **200 ms**.

Later, something changes.
Monitoring detects:

```text
CPU usage:        95%      (high)
Memory usage:     80%      (high)
Response time:    3000 ms  (very slow)
```

```text
Application
   │
   ▼
Metrics  →  CPU 95%, Memory 80%, Response 3000ms
   │
   ▼
Dashboard  →  charts show the spike
   │
   ▼
Alert  →  "Response time too high!"
   │
   ▼
Developer investigates
```

Because of monitoring, the developer sees the spike **while it is happening** — and fixes it **before users complain**.

---

## 11. Failure and Limits

- **No monitoring** — problems are found only after users report them.
- **Too many alerts** — developers stop paying attention (alert fatigue).
- **Wrong metrics** — watching numbers that do not matter.
- **Monitoring down** — the system runs, but nobody is watching it.

Monitoring is needed in **every** serious system.
The art is watching the **right** numbers and alerting only on what matters.

---

## 12. System Position

Monitoring is a **cross-cutting concern** — it watches every block, but it is not one block by itself.

```text
Frontend → Backend → Database
   ↓          ↓          ↓
        Monitoring
```

Type:

- It is the **live watch** of the system.
- It runs **alongside** every component, not inside one.

---

## Common Technologies

These all implement the Monitoring concept — collecting metrics and watching the system in real time.

- **Prometheus**
- **Grafana**
- **Datadog**
- **New Relic**
- **Amazon CloudWatch**
- **Azure Monitor**

They differ in style, but the architectural role is the same: **watch the system while it runs**.

---

## 13. Key Takeaways

* Monitoring is **watching the system while it runs**.
* Logs tell us **what happened**. Monitoring tells us **what is happening right now**.
* A **metric** is a single number about the system (CPU, memory, response time...).
* A **dashboard** shows metrics as charts. An **alert** fires when something looks wrong.
* Monitoring helps detect problems **before users complain**.
* Logs explain a single event. Monitoring shows the big picture.

---

## 14. Next Block

```text
Current Solution
      ↓
The application works correctly.
It has logging, monitoring, and many
architectural components working together.
      ↓
Remaining Problem
      ↓
How can we package the entire application
so it runs the same way on every developer's
machine, testing environment, and production
server?
      ↓
Next Block
      ↓
Docker
```

[Docker](./19_docker.md)