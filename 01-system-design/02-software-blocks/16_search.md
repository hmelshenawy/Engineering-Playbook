# Search

> This file continues from [15 - File Storage](./15_file_storage.md).
>
> The system can now store files. But as the app grows, the **amount of data** grows too.
> Users want to **find** information fast: products, documents, posts, users.
>
> Searching the Database directly can work for a while — but for large apps it gets slow and limited.
> The fix is a dedicated **Search** component.

---

## 1. Overview

A Search component is a **specialized store built for finding data fast**.
It sits beside the Database, not inside it.

```text
Backend
   ↓
Search   (finds data quickly)
```

The Database keeps the data.
The Search component helps users **find** that data.

---

## 2. Why

The Database is great at **storing** data and keeping it correct.
It is not built for **searching big text** across many rows.

Imagine a store with 100,000 products.
A user types "wireless keyboard".
If the Database scans every product row by row, it is **slow**.

```text
Database: scan 100,000 rows → slow
Search:   look up an index → fast
```

A Search component is built for this one job: **find matching data quickly**, even when there is a lot of it.

---

## 3. Database Search vs Dedicated Search

| Task           | Database          | Search component      |
| -------------- | ----------------- | --------------------- |
| Store data      | Great             | Okay                   |
| Keep it correct | Great             | Not its main job       |
| Find by exact id | Fast             | Fast                   |
| Find by words   | Slow on large data | Fast                   |
| Rank by relevance | Hard            | Built for it           |

```text
Database  →  "give me product with id = 42"      (fast)
Search    →  "give me products like 'keyboard'"  (fast, on big data)
```

They do different jobs. That is why both can exist.

---

## 4. Searching Large Amounts of Data

The Database grows with the application.
More products, more posts, more users.
Searching all of it row by row gets slower over time.

A Search component is designed to stay **fast even when the data is large**.
It does not scan everything. It uses a **shortcut**.

---

## 5. Search Indexes (High Level)

The shortcut is a **search index**.
Think of it like the index at the back of a book.

```text
Book index:
  "keyboard"  →  pages 12, 45, 88
  "wireless"  →  pages 8, 45, 102
```

Instead of reading the whole book, you check the index and jump to the right pages.

A Search component builds an index of the data **ahead of time**.
When the user searches, it checks the index — not all the data.

```text
User: "wireless keyboard"
   ↓
Search checks index
   ↓
Index says: products 7, 19, 42
   ↓
Return those products fast
```

We keep this high level. How the index is built inside gets its own chapter.

---

## 6. Returning Search Results

The Search component returns a **list of matches** — usually ranked by how well they match.

```text
Query: "wireless keyboard"
   ↓
Results:
  1. Wireless Keyboard Pro   (best match)
  2. Wireless Combo Set      (good match)
  3. Mechanical Keyboard     (weaker match)
```

The Backend takes this list and returns it to the Frontend.
The user sees the results, ordered by relevance.

---

## 7. Connections

| From → To          | Purpose                        |
| ------------------ | ------------------------------ |
| Backend → Search     | Send a search query             |
| Search → Backend     | Return matching results         |
| Database → Search     | Keep the index in sync (usually in the background) |

The Search component does **not** replace the Database.
The Database is still the source of truth.
The Search component is a **fast copy built for finding**.

```text
Database (source of truth) → keeps data in sync → Search (fast lookup)
```

---

## 8. Practical Example

An online store has thousands of products.
A user searches for "Wireless Keyboard".

```text
Frontend: user types "Wireless Keyboard"
   ↓
Backend: send query to Search
   ↓
Search: check the index
   ↓
Search: find matching products
   ↓
Backend: receive results
   ↓
Frontend: show the product list
```

Full picture:

```text
Frontend
   │
   ▼
Backend
   │
   ▼
Search  →  matching products
   │
   ▼
Backend
   │
   ▼
Frontend  →  user sees results
```

The Database still owns the products.
But the **Search** component is the one that finds them fast.

---

## 9. Failure and Limits

- **Search down** — search stops working, but the Database is fine. The app can fall back to the Database (slower) or show "search unavailable."
- **Index out of sync** — new products are in the Database but not yet in the Search index. Users see old results for a while.
- **Wrong index for the job** — searching the wrong fields gives bad results.

A Search component is useful when there is **a lot of data** and users need to **find by words**.
For tiny data, the Database alone is enough.

---

## 10. System Position

A Search component is an **Infrastructure Component** — a specialized read-optimized store beside the Database.

```text
Frontend → Backend → { Database (source of truth) , Search (fast find) }
```

Type:

- It is the **find-it-fast** part of the Data Layer.
- It is **not** the source of truth — the Database is.

---

## Common Technologies

These all implement the Search concept — a fast way to find data by words.

- **Elasticsearch**
- **OpenSearch**
- **Meilisearch**
- **Typesense**
- **Algolia**

They differ in style, but the architectural role is the same: **store an index, return matches fast**.

---

## 11. Key Takeaways

* A Search component is a **specialized store for finding data fast**.
* The **Database stores data**. The **Search component helps users find it quickly**.
* Searching the Database works for small data, but gets slow on large data.
* A Search component uses an **index** — like the index at the back of a book.
* It returns **ranked results**, best match first.
* It is **not** the source of truth — it stays in sync with the Database.

---

## 12. Next Block

```text
Current Solution
      ↓
The system now has many components working together:
Frontend, Backend, Database, Cache, Queue,
Workers, Event Bus, Gateway, File Storage, Search.
      ↓
Remaining Problem
      ↓
When something goes wrong, developers need to
know what happened.
How can we record important events and errors
inside the system?
      ↓
Next Block
      ↓
Logging
```

[Logging](./17_logging.md)