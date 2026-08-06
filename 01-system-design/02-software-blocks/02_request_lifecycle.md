# Request Lifecycle

> الملف ده هو تكملة لـ [01 - Software System Overview](./01_system_overview.md).
>
> السؤال اللي فضل معلق في الآخر كان: "لو الـSoftware System متقسم لـBlocks كتير، فكيف الـRequest بياخد طريقه عبر كل الـBlocks دي؟"
>
> هنا هنتبع **Request واحد** من أول نقرة لحد ما الـResponse يرجع للمستخدم.

---

## 1. Overview

### إيه هو الـRequest Lifecycle؟

الـRequest Lifecycle هي **رحلة واحدة** بيعملها الـRequest من لحظة ما الـUser يضغط على حاجة، لحد ما يشوف النتيجة على الشاشة.

```text
User click
   ↓
... رحلة عبر Blocks كتير ...
   ↓
User sees the result
```

### المشكلة اللي بتحلها

الـUser مش بيشوف الـBlocks دي. هو بيشوع حاجة: *دوس Login*. وبيقوله حاجة: *تم تسجيل الدخول*.

بين اللحظتين دي، الـRequest بيسافر عبر Blocks مختلفة، وكل Block بتعمل شغلها وتسيبه للـBlock اللي بعدها. من غير الفهم ده، بقية الكورس هيبقى سلسلة من الـTools من غير سبب.

### فين موجود جوه الـSystem؟

الـRequest Lifecycle مش Block لوحده. ده **الطريق** اللي الـRequest بيمشي فيه عبر الـBlocks. هو اللي بيربط الـSystem كله ببعضه.

---

## 2. Inside

الـRequest Lifecycle مش بيحتوي على Components. هو بيوصف **تسلسل** من المحطات. كل محطة هي Block من الـBlocks اللي اتكلمنا عنها.

```text
Login Request
├── User         (نقطة البداية)
├── Browser      (البيئة اللي شغالة فيها الواجهة)
├── Frontend     (اللي بيشكل الـRequest)
├── HTTP Request (الرسالة اللي ماشية في الشبكة)
├── Backend      (اللي بيستقبل وبيدير)
├── Authentication (اللي بيأكد مين الـUser)
├── Business Logic (اللي بياخذ القرار)
├── Database     (اللي بيخزن ويجيب البيانات)
└── Response     (الرد اللي بيرجع بنفس الطريق)
```

> كل Block هنا هنتعامل معاه كأنه **Black Box**. يعني مش هنشرح إيه جواه دلوقتي. كل اللي يهمنا هو: **الـRequest بيزورها ليه؟** والـBlock دي **بتطلعه ليه؟** باقي التفاصيل لكل Block في الـChapter بتاعه.

---

## 3. Connections

الـRequest ماشي في خط واحد متسلسل تقريباً، بس في محطات بيحصل فيها تفاعل مع Blocks تانية:

| From → To                     | Direction | Method | Sync/Async | Purpose                          |
| ----------------------------- | --------- | ------ | ---------- | -------------------------------- |
| User → Browser                | Outgoing  | Click  | Sync       | تبدأ الـRequest                    |
| Frontend → Backend             | Outgoing  | HTTP   | Sync       | تبعت الـLogin data                 |
| Backend → Authentication       | Internal  | Call   | Sync       | تتحقق من الـUser                   |
| Backend → Business Logic       | Internal  | Call   | Sync       | تـprocess الـLogin                |
| Backend → Database             | Outgoing  | Query  | Sync       | تقرأ بيانات الـUser                |
| Backend → Frontend             | Incoming  | HTTP   | Sync       | ترجع الـResponse                   |

مبدأ مهم: الـRequest ماشي **Synched** معظم الطريق. يعني كل Block بتستنى اللي بعدها تخلص الأول. عشان كده أي Block بتأخر، بيبطّي الـRequest كله. (دي مشكلة هنشوف حلها في الـChapters اللي بعدها).

---

## 4. Main Flow

تعالوا نتبع **Login Request** واحد خطوة بخطوة. نفس الـScenario هيفضل معانا طول الملف.

```text
User
  ↓
Browser
  ↓
Frontend
  ↓
HTTP Request
  ↓
Backend
  ↓
Authentication
  ↓
Business Logic
  ↓
Database
  ↓
Response
  ↓
Browser
  ↓
User
```

نشرح كل محطة في سطر-سطرين:

### 1. User

الـUser بيدوس على زرار **Login**. هو نقطة البداية. من غيره مفيش Request أصلاً.

### 2. Browser

الـBrowser هو البيئة اللي شغّالة فيها الـFrontend. هو اللي بيلتقط الـClick ويفضي الـFrontend إنه يشتغل.

### 3. Frontend

الـFrontend بياخد البيانات اللي الـUser كتبها (الـEmail والـPassword) وبيعملها format زي اللي الـBackend بيتوقعه. ده شغله الأول.

### 4. HTTP Request

الـFrontend بيبعت البيانات كـ**HTTP Request** على الشبكة. الـHTTP هو لغة الاتصال بين الـClient والـBackend. من غيره الـRequest مش هيوصل.

### 5. Backend

الـBackend بيستقبل الـHTTP Request. هو المسؤول عن إنه يقراه، يفهمه، ويبصله على الـBlocks اللي جواه (زي الـAuthentication والـBusiness Logic والـDatabase).

### 6. Authentication

الـAuthentication بيأكد **مين** الـUser ده. بيشوف الـEmail والـPassword بتوعه صح ولا لأ، ويقرر هو الـPerson الصح ولا لأ. ده الـBlock اللي بيتـcheck فيه الـIdentity.

### 7. Business Logic

الـBusiness Logic هو العقل. هو اللي بياخذ القرار: هل الـLogin ده مسموح بيه؟ هل لازم نعمل Session؟ هل لازم نـgenerate JWT؟ هو اللي بـapply قواعد الـBusiness.

### 8. Database

الـDatabase هي المكان اللي بيتخزن فيه بيانات الـUser. الـBackend بيبعت Query للـDatabase عشان يجيب الـUser record ويتأكد من الـPassword. هنا البيانات الحقيقية عايشة.

### 9. Response

بعد ما الـDatabase بترجع بالبيانات والـBackend بيخلص كل الـchecks، الـBackend بيـbuild الـ**Response** (زي Success أو Error) ويرجعه بنفس الطريق.

```text
Database
  ↓ query result
Business Logic
  ↓ builds result
Authentication
  ↓ attaches identity
Backend
  ↓ builds HTTP Response
HTTP
  ↓
Browser
  ↓
Frontend
  ↓ renders
User
  ↓ sees "Logged in"
```

لاحظ إن الـ**Response بيمشي بنفس الطريق بس بالعكس**. كل Block بتمشي للي قبلها لحد ما يوصل للـUser.

---

## 5. Practical Example

نطبق نفس الـScenario على Login حقيقي:

```text
الـUser بيكتب email + password ويدوس Login
   ↓
الـBrowser بيشغل الـFrontend
   ↓
الـFrontend بيجمع الـdata ويبعتها كـHTTP Request
   ↓
الـHTTP Request بيوصل للـBackend
   ↓
الـBackend بيبعت الـdata للـAuthentication
   ↓
الـAuthentication بيـquery الـDatabase على الـUser record
   ↓
الـDatabase بترجع الـUser record (فيه الـPassword hash)
   ↓
الـAuthentication بيقارن الـPassword ويرجع نجاح/فشل
   ↓
الـBusiness Logic بيـcreate Session / JWT
   ↓
الـBackend بيـbuild HTTP Response (200 OK + token)
   ↓
الـHTTP بيوصل للـBrowser
   ↓
الـFrontend بيـrender: "Welcome back"
   ↓
الـUser بيشوف: "تم تسجيل الدخول"
```

دي نفس الـFlow اللي حصل. لو أي Block في النص وقّع، الـRequest كله بيوقف. ده عيب طبيعي للـSynched flow.

---

## 6. Failure and Limits

* **إيه اللي ممكن يفشل؟** أي Block في الطريق ممكن يقع. الـBackend مش متاح، الـDatabase وقّعت، الـAuthentication رفض الـUser، الـHTTP timeout... أي حاجة من دي بتفشل الـLogin.
* **النظام بيتعامل معاها إزاي؟** كل Block بيرجع error بدل ما يفشل بصمت، والـResponse بيرجع يوضح للـUser إن في مشكلة.
* **متى مش محتاجين الـFlow ده؟** لو الـSystem صغير جداً أو الداتا كلها Client-side، ممكن الـRequest ميلمسش كل الـBlocks دي. بس في الغالب Login بيمشي بالطريق ده.
* **المشكلة اللي لسه موجودة:** لحد دلوقتي احنا عاملين كل Block كأنه **Black Box**. الـRequest وصل أول Block في الطريق — الـFrontend — بس إيه اللي بيحصل جوه الـFrontend قبل الـRequest ما يمشي في الشبكة؟

---

## 7. System Position

الـRequest Lifecycle ده مش Module، مش Service، ومش Infrastructure Component. هو **Concept** بيوصف الطريق اللي الـRequest بياخده عبر الـBlocks.

```text
Frontend
   ↓ HTTP
Backend
   ├── Authentication
   ├── Business Logic
   │      ↓ Query
   └── Database
   ↓ HTTP Response
Frontend
   ↓
User
```

كل Block في الرسم ده هنتعمق فيه في الـChapter بتاعه. هنا بس بنعمل map للطريق.

---

## 8. Key Takeaways

* الـRequest Lifecycle هي **رحلة واحدة** للـRequest من الـUser لحد ما الـResponse يرجع.
* كل Block ليها **مسؤولية واحدة**، والـRequest بيزورها عشان تـperform المسؤولية دي.
* الـResponse بيرجع **بنفس الطريق بالعكس**، من الـBackend لحد الـUser.
* معظم الـFlow ده **Sync**، فأي Block بتبطّي بتـpull الـRequest كله وراها.
* احنا لسه معاملين كل Block كـ**Black Box** — internals بتاعتها لكل Block في الـChapter بتاعه.

---

## 9. Next Block

```text
Current Solution
      ↓
اتبعنا Login Request من أول نقرة لحد الـResponse،
وعرفنا إزاي الـRequest بيمشي عبر الـBlocks.
      ↓
Remaining Problem
      ↓
الـRequest أول Block وصل له كان الـFrontend.
بس إيه اللي بيحصل جوه الـFrontend قبل ما
الـRequest يمشي في الشبكة أصلاً؟
جمع الـdata، الـvalidation، الـAPI call...
كل ده بيحصل قبل ما نغادر جهاز الـUser.
      ↓
Next Block
      ↓
Frontend
```

[Frontend](./03_frontend.md)