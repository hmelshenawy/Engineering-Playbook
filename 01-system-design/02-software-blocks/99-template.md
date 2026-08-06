# Block Name

## What?

## Why?

## Inside

## Communication

## Flow

## Example

## Notes


___________________________________________-

# Architecture Block Template

> اشرح Block واحد فقط، واربطه بالـBlock السابق والتالي دون الخروج عن الموضوع.

# [Block Name]

## 1. Overview

* ما هو؟
* ما المشكلة التي يحلها؟
* أين يقع داخل النظام؟

## 2. Inside

اشرح مكوّناته الأساسية فقط.

```text
Block
├── Component A
├── Component B
└── Component C
```

## 3. Connections

| Connects To | Direction | Method | Sync/Async | Purpose    |
| ----------- | --------- | ------ | ---------- | ---------- |
| Example     | Outgoing  | HTTP   | Sync       | Fetch data |

وضّح من يبدأ الاتصال وماذا يحدث عند الفشل.

## 4. Main Flow

اشرح رحلة البيانات خطوة بخطوة.

```text
Input
  ↓
Processing
  ↓
Dependency
  ↓
Output
```

## 5. Practical Example

استخدم مثالًا عمليًا واحدًا يوضح دور هذا الـBlock داخل النظام.

## 6. Failure and Limits

* ما الذي قد يفشل؟
* كيف يتعامل النظام معه؟
* متى لا نحتاج هذا الـBlock؟
* ما المشكلة التي ما زالت موجودة بعد استخدامه؟

## 7. System Position

```text
Frontend
   ↓
[CURRENT BLOCK]
   ↓
Database / Service / Infrastructure
```

حدّد هل هو:

* Module
* Service
* Infrastructure Component
* External Service

## 8. Key Takeaways

* من 3 إلى 5 نقاط فقط.

## 9. Next Block

```text
Current Solution
      ↓
Remaining Problem
      ↓
Next Block
```

[Next Block Name](./next_block.md)

---

# Writing Rules

* ملف واحد لكل Block.
* الحجم المستهدف: 500–1000 كلمة.
* اشرح الـConcept قبل الأدوات.
* استخدم Diagrams بسيطة.
* لا تكرر شرح Blocks أخرى.
* لا تضف Technology متقدمة دون مشكلة حقيقية تبررها.
* اختم دائمًا بمشكلة تقود للـBlock التالي.
