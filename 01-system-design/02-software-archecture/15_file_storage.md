# File Storage

> This file continues from [14 - Load Balancer](./14_load_balancer.md).
>
> The system can now spread traffic across many servers.
> But applications often need to store **large files**: images, documents, videos, reports, backups.
>
> These files should not live inside the Backend server.
> The fix is **File Storage**.

---

## 1. Overview

File Storage is a **separate place to keep large files**.
It sits beside the Backend, not inside it.

```text
Backend
   ↓
File Storage  (images, documents, videos...)
```

The Backend puts files there.
The Backend reads files from there.
The files live there permanently.

---

## 2. Why

The Backend server is built to **process requests**, not to keep files.
Storing large files inside it causes problems:

- Files take up a lot of **disk space**.
- Files fill up the server's **memory** when handled.
- If the server crashes or restarts, files can be **lost**.
- Multiple Backend servers would each need their **own copy**.

```text
Backend server + big files = trouble
```

So large files get their own home: **File Storage**.

---

## 3. Files vs Database Records

This is the key idea of the chapter.

| What        | Where it lives     | What it is              |
| ----------- | ------------------ | ----------------------- |
| The file    | File Storage        | The actual image/video  |
| The record  | Database            | Information about the file |

```text
Database       →  "there is a file called avatar.png, owned by user 42"
File Storage   →  the actual avatar.png bytes
```

They are **two different things**, kept in **two different places**.

---

## 4. Why the Database Stores Metadata

The Database is great at **small, structured** data.
It is bad at **huge binary blobs**.

So we store **metadata** in the Database — small facts about the file:

- file name
- storage location
- owner
- size
- upload date

And we store the **actual file** in File Storage.

```text
Frontend
   │
   ▼
Backend
   │
   ├── Database
   │     └── File metadata (small)
   │
   └── File Storage
         └── Actual file (large)
```

The Database tells the Backend **where** the file is.
File Storage holds the **file itself**.

---

## 5. Uploading

When a user uploads a file, the Backend does two things:

1. Send the **file** to File Storage.
2. Save the **metadata** in the Database.

```text
Frontend → Backend → File Storage → file saved
                  → Database     → metadata saved
```

Both happen on the same upload.
The file goes one place, the info goes another.

---

## 6. Downloading

When a user later needs the file, the order reverses:

1. The Backend reads the **metadata** from the Database — to find where the file is.
2. The Backend gets the **file** from File Storage.
3. The Backend returns the file to the user.

```text
Frontend → Backend → Database → "the file is at location X"
                  → File Storage → gets the file
                  → returns the file to Frontend
```

The Database points the way.
File Storage provides the bytes.

---

## 7. Connections

| From → To                | Purpose                        |
| ------------------------ | ------------------------------ |
| Frontend → Backend        | Upload or download a file       |
| Backend → File Storage    | Save or read the actual file     |
| Backend → Database         | Save or read the file metadata   |

The Backend is the **middle** between the user, the Database, and File Storage.

```text
Frontend → Backend → { Database (info) , File Storage (file) }
```

---

## 8. Practical Example

A user uploads a profile image.

**Upload:**

```text
Frontend: user picks avatar.png
   ↓
Backend: send file to File Storage
   ↓
File Storage: file saved
   ↓
Backend: save metadata in Database
   {
     fileName: "avatar.png",
     location: "storage/users/42/avatar.png",
     owner: 42
   }
   ↓
Response: "upload done"
```

**Later — the user opens their profile:**

```text
Frontend: requests profile
   ↓
Backend: read metadata from Database
   → finds: location = "storage/users/42/avatar.png"
   ↓
Backend: get the file from File Storage
   ↓
User gets the image
```

The Database knew **about** the file.
File Storage had **the** file.
Together they make the profile work.

---

## 9. Failure and Limits

- **File Storage down** — uploads and downloads fail, but the Database still works.
- **Database down** — the Backend cannot find where files are, even if the files still exist.
- **Metadata points to a missing file** — the record says the file is there, but it is not. The two got out of sync.

File Storage is needed when the system keeps **large files**.
For tiny data (like a setting), the Database alone is enough.

---

## 10. System Position

File Storage is an **Infrastructure Component** — the part of the Data Layer that holds **large files**.

```text
Frontend → Backend → { Database (metadata) , File Storage (files) }
```

Type:

- It is the **storage for big files**.
- It is **not** the Database, and it does **not** process requests.

---

## Common Technologies

These all implement the File Storage concept — a place to keep large files outside the application.

- **Amazon S3**
- **Azure Blob Storage**
- **Google Cloud Storage**
- **Cloudflare R2**
- **MinIO**
- **Supabase Storage**

They differ in style, but the architectural role is the same: **store the file, not the metadata**.

---

## 11. Key Takeaways

* File Storage is a **separate place** for large files.
* Large files should **not** live inside the Backend server.
* The **Database stores information about the file** (metadata).
* **File Storage stores the actual file.**
* On upload: the file goes to File Storage, the metadata goes to the Database.
* On download: the Backend reads the metadata, then gets the file.

---

## 12. Next Block

```text
Current Solution
      ↓
The system can now store large files correctly.
The Database keeps the metadata.
File Storage keeps the actual file.
      ↓
Remaining Problem
      ↓
As the amount of data grows, users need a
fast way to search through large amounts
of information.
Searching directly through the Database
may not always be enough.
How can the system provide fast and
powerful search?
      ↓
Next Block
      ↓
Search
```

[Search](./16_search.md)