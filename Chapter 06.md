---
title: Chapter 06 – Robust API Infrastructure & Error Handling
---

# Chapter 06 – Robust API Infrastructure & Error Handling 🔒🚀

In this chapter, we’ll elevate our backend to production standards by:

1. **Abstracting database connectivity**  
2. **Standardizing API responses & errors**  
3. **Simplifying async route handling**  
4. **Configuring global middleware**  
5. **Bootstrapping the server**  

---

## 🚀 1. Database Connection Abstraction

Centralize your MongoDB connection logic in **`src/db/index.js`**:

```js
import mongoose from "mongoose";
import { DB_NAME } from "../config/constants.js";

const connectDB = async () => {
  try {
    const connectionInstance = await mongoose.connect(
      `${process.env.MONGODB_URI}/${DB_NAME}`
    );
    console.log({ connectionInstance });
    console.log(
      `\n✅ MongoDB connected! Host: ${connectionInstance.connection.host}`
    );
  } catch (error) {
    console.error("❌ MongoDB connection error:", error);
    process.exit(1);
  }
};

export default connectDB;
````

* **`DB_NAME`** is imported from your constants (see next).
* On failure, the process exits to avoid orphaned servers.

---

## 🎛 2. Configuration & Constants

Define reusable constants in **`src/config/constants.js`**:

```js
export const DB_NAME     = process.env.DB_NAME     || "videotube";
export const PORT        = process.env.PORT        || 8000;
export const CORS_ORIGIN = process.env.CORS_ORIGIN || "*";
```

* Store default values for database name, port, CORS origins, etc.
* Override via your `.env` file in development/production.

---

## ⚙️ 3. Global App Middleware

In **`src/app.js`**, configure Express and global middleware:

```js
import express from "express";
import cors from "cors";
import cookieParser from "cookie-parser";

const app = express();

// Enable CORS with credentials support
app.use(cors({
  origin: process.env.CORS_ORIGIN,
  credentials: true,
}));

// Parse JSON & URL-encoded bodies (limit 16 KB)
app.use(express.json({ limit: "16kb" }));
app.use(express.urlencoded({ extended: true, limit: "16kb" }));

// Serve static assets from /public
app.use(express.static("public"));

// Parse cookies
app.use(cookieParser());

export { app };
```

* **CORS**: Restrict origins and allow credentials.
* **Body parsers**: Limit payload size to prevent abuse.
* **Static**: Expose public files (images, client bundles).
* **Cookies**: For session/token storage.

---

## 📤 4. Standard API Responses & Errors

### 4.1 `ApiResponse` (src/utils/ApiResponse.js)

Uniform success responses:

```js
export class ApiResponse {
  constructor(statusCode, message, data) {
    this.statusCode = statusCode;
    this.success    = statusCode < 400;
    this.message    = message;
    this.data       = data;
  }
}
```

### 4.2 `ApiError` (src/utils/ApiError.js)

Rich error objects:

```js
export class ApiError extends Error {
  constructor(statusCode, message = "Something went wrong", errors = [], stack = "") {
    super(message);
    this.statusCode = statusCode;
    this.success    = false;
    this.errors     = errors;
    if (stack) this.stack = stack;
    else      Error.captureStackTrace(this, this.constructor);
  }
}
```

* **`statusCode`** drives HTTP response codes.
* **`errors`** is an optional array of validation or business errors.

---

## 🔁 5. Async Handler Utility

Avoid repetitive `try/catch` in each controller. Create **`src/utils/asyncHandler.js`**:

```js
export const asyncHandler = (fn) => 
  async (req, res, next) => {
    try {
      await fn(req, res, next);
    } catch (err) {
      next(err);
    }
  };
```

Use it when defining routes:

```js
import { asyncHandler } from "../utils/asyncHandler.js";
import { User } from "../models/user.js";

router.get(
  "/:id",
  asyncHandler(async (req, res) => {
    const user = await User.findById(req.params.id);
    res.json(new ApiResponse(200, "User fetched", user));
  })
);
```

---

## 🔌 6. Server Bootstrap

Finally, tie everything together in **`src/server.js`**:

```js
import dotenv from "dotenv";
dotenv.config({ path: ".env" });

import { app }      from "./app.js";
import connectDB    from "./db/index.js";
import { PORT }     from "./config/constants.js";

(async () => {
  try {
    await connectDB();
    app.listen(PORT, () => {
      console.log(`🚀 Server running on port ${PORT}`);
    });
  } catch (err) {
    console.error("❌ Failed to start server:", err);
    process.exit(1);
  }
})();
```

* **Load env vars** before anything else.
* **Connect to DB** then start listening.
* **Graceful exit** on startup failure.

---

## 🌱 7. What’s Next?

* **Centralized error middleware** to format `ApiError` and `ApiResponse`.
* **Authentication**: JWT & secure cookie setup.
* **Rate limiting & throttling** for public endpoints.
* **Input validation** with `express-validator` or Joi.
* **Logging enhancements** using Winston, Pino transports, and request tracing.

---

🎉 **Chapter 06 complete!** You now have a **scalable**, **maintainable**, and **consistent** backend foundation—ready for advanced features and real-world traffic.
