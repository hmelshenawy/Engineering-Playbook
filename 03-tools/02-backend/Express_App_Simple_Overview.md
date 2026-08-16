# Express App — Simple Overview

## What is the Express App?

When you write:

```js
const express = require('express');

const app = express();
```

`app` is your **Express application**.

You can think of it as the main manager of your HTTP server.

Every request coming to your backend passes through the Express app, and Express decides which middleware, route, or handler should process it.

```text
Client
  |
  | HTTP Request
  v
Express App
  |
  +--> Middleware
  +--> Routes
  +--> Controllers
  +--> Error Handler
         |
         v
     HTTP Response
```

---

## 1. Middleware

Middleware is code that runs while the request is moving through the Express application.

Example:

```js
app.use(express.json());
```

This tells Express:

> If the incoming request contains JSON, parse it and put it inside `req.body`.

Example request:

```json
{
  "name": "Electronics"
}
```

Then inside your controller:

```js
req.body.name
```

will contain:

```text
Electronics
```

---

## 2. Mounting Routes

You can connect a router to your Express app:

```js
app.use('/api/categories', categoryRouter);
```

This means:

> Any request starting with `/api/categories` should go to `categoryRouter`.

Inside `categoryRouter`:

```js
router.get('/', getCategories);

router.post('/', createCategory);

router.get('/:id', getCategoryById);
```

So these requests become:

```text
GET  /api/categories
POST /api/categories
GET  /api/categories/:id
```

Example:

```text
GET /api/categories/123
```

Flow:

```text
Express App
    |
    v
/api/categories
    |
    v
categoryRouter
    |
    v
GET /:id
    |
    v
getCategoryById
```

---

## 3. Route Methods

Express provides methods for the common HTTP methods:

```js
app.get()
app.post()
app.put()
app.patch()
app.delete()
```

Example:

```js
app.get('/hello', (req, res) => {
  res.json({
    message: 'Hello'
  });
});
```

This means:

```text
GET /hello
```

will run that function.

---

## 4. `app.all()`

`app.all()` runs a route handler for **all HTTP methods**.

For example:

```js
app.all('/{*splat}', (req, res, next) => {
  const error = new ApiError(
    `Can't find ${req.originalUrl} on this server!`,
    404
  );

  next(error);
});
```

Because this code is placed after all normal routes, it acts as a catch-all for undefined routes.

For example:

```text
GET    /banana
POST   /banana
DELETE /banana
```

If no route handled them earlier, they reach this handler.

---

## 5. `app.use()`

`app.use()` is mainly used to add middleware or mount routers into the request pipeline.

Examples:

```js
app.use(express.json());
```

Add middleware.

```js
app.use('/api/categories', categoryRouter);
```

Mount a router.

```js
app.use(globalErrorHandler);
```

Add the global error-handling middleware.

A useful mental model is:

```text
app.get()    -> GET requests for a route
app.post()   -> POST requests for a route
app.put()    -> PUT requests for a route
app.delete() -> DELETE requests for a route
app.all()    -> all HTTP methods for a route
app.use()    -> add middleware/router to the pipeline
```

---

## 6. Controllers

A route usually calls a controller function.

Example:

```js
router.get('/:id', getCategoryById);
```

The controller:

```js
exports.getCategoryById = asyncHandler(async (req, res) => {
  const category = await CategoryModel.findById(req.params.id);

  res.status(200).json({
    status: 'success',
    data: category
  });
});
```

The flow becomes:

```text
Request
   |
   v
Route
   |
   v
Controller
   |
   v
Model
   |
   v
MongoDB
   |
   v
Response
```

---

## 7. Error Handling

If something goes wrong, you can create an error:

```js
const error = new ApiError(
  'Category not found',
  404
);
```

Then pass it to Express:

```js
next(error);
```

`next(error)` tells Express:

> Stop the normal middleware flow and send this error to the error-handling middleware.

Example:

```js
const globalErrorHandler = (err, req, res, next) => {
  err.statusCode = err.statusCode || 500;
  err.status = err.status || 'error';

  res.status(err.statusCode).json({
    status: err.status,
    message: err.message,
    stack: err.stack
  });
};
```

And register it:

```js
app.use(globalErrorHandler);
```

Error flow:

```text
Controller / Route
       |
       v
   ApiError
       |
       v
 next(error)
       |
       v
    Express
       |
       v
globalErrorHandler
       |
       v
 HTTP Response
```

---

## 8. Why Does Order Matter?

Express processes middleware and routes **from top to bottom**.

A typical application should look like this:

```js
const express = require('express');

const app = express();

// 1. Middleware
app.use(express.json());

// 2. Routes
app.use('/api/categories', categoryRouter);
app.use('/api/users', userRouter);

// 3. Undefined routes
app.all('/{*splat}', (req, res, next) => {
  next(
    new ApiError(
      `Can't find ${req.originalUrl} on this server!`,
      404
    )
  );
});

// 4. Global error handler
app.use(globalErrorHandler);

// 5. Start server
app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
```

The order is important:

```text
REQUEST
   |
   v
Middleware
   |
   v
Routes
   |
   +---- Route found ----> Controller ----> Response
   |
   +---- No route found
             |
             v
       Catch-all route
             |
             v
        next(error)
             |
             v
     Global Error Handler
             |
             v
          Response
```

---

## Full Mental Model

The easiest way to understand Express is to think of it as a **request pipeline**:

```text
                  HTTP REQUEST
                       |
                       v
                +---------------+
                |  Express App  |
                +---------------+
                       |
                       v
                  Middleware
                       |
                       v
                    Routes
                 /           \
                v             v
        Category Router    User Router
                |
                v
            Controller
                |
                v
              Model
                |
                v
             MongoDB
                |
                v
          HTTP RESPONSE
```

If an error happens:

```text
Route / Controller / Model
          |
          v
        Error
          |
          v
     next(error)
          |
          v
 globalErrorHandler
          |
          v
      Response
```

---

## Short Summary

**Express `app` is the main HTTP application that receives requests and moves them through middleware, routes, controllers, and error handlers until a response is returned.**

The most important idea is:

```text
Request
   ->
Middleware
   ->
Route
   ->
Controller
   ->
Model / Database
   ->
Response
```

And for errors:

```text
Error
  ->
next(error)
  ->
globalErrorHandler
  ->
Response
```
