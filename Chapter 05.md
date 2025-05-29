---
title: Chapter 05 – Professional Backend Setup & Configuration
---

# Chapter 05 – Professional Backend Setup & Configuration 🛠️

In this chapter, we’ll transform our basic Express/Mongoose boilerplate into a **production-ready** backend by covering:

1. Project structure and configuration  
2. Environment variables management  
3. Database connection abstraction  
4. Centralized error handling & logging  
5. Server startup & resilience  
6. Useful npm scripts & dependencies  

---

## 🚀 1. Project Structure

Organize code for maintainability, clarity, and separation of concerns:

```

my-app/
├── src/
│   ├── config/
│   │   ├── constants.js
│   │   └── database.js
│   ├── controllers/
│   ├── middlewares/
│   │   ├── errorHandler.js
│   │   └── notFound.js
│   ├── models/
│   ├── routes/
│   ├── utils/
│   │   └── logger.js
│   ├── app.js
│   └── server.js
├── env                 ← Example environment variables
├── .env                ← Local environment variables (gitignored)
├── .gitignore
└── package.json

````

- **`src/app.js`** – Express app: middleware, routes.  
- **`src/server.js`** – Entrypoint: load config, connect DB, start HTTP server.  
- **`src/config/database.js`** – Exports `connectDB()` function.  
- **`src/config/constants.js`** – Export DB names, port defaults, etc.  
- **`src/middlewares`** – Reusable error & 404 handlers.  
- **`src/utils/logger.js`** – Centralized logger (e.g., with Winston or Pino).  

---

## 🎯 2. Environment Variables

Keep secrets and environment-specific settings out of code:

1. **`.env`** (Git-ignored)
   ```dotenv
   NODE_ENV=development
   PORT=4000
   MONGODB_URI=mongodb://localhost:27017
   DB_NAME=hms
   LOG_LEVEL=debug
````

2. **`env.example`** (committed)

   ```dotenv
   NODE_ENV=
   PORT=
   MONGODB_URI=
   DB_NAME=
   LOG_LEVEL=
   ```
3. **Loading `.env`** in **`src/server.js`**:

   ```js
   import dotenv from 'dotenv';
   dotenv.config({ path: '.env' });
   ```

---

## 🔌 3. Database Connection Abstraction

Centralize MongoDB startup and error handling.

### `src/config/database.js`

```js
import mongoose from 'mongoose';
import { DB_NAME } from './constants.js';
import logger from '../utils/logger.js';

const connectDB = async () => {
  try {
    const conn = await mongoose.connect(`${process.env.MONGODB_URI}/${DB_NAME}`, {
      useNewUrlParser: true,
      useUnifiedTopology: true,
    });
    logger.info(`MongoDB connected @ ${conn.connection.host}/${DB_NAME}`);
  } catch (err) {
    logger.error('MongoDB connection error:', err);
    process.exit(1);
  }
};

export default connectDB;
```

### `src/config/constants.js`

```js
export const DB_NAME = process.env.DB_NAME || 'myapp';
export const PORT    = process.env.PORT    || 3000;
export const NODE_ENV = process.env.NODE_ENV || 'production';
```

---

## 📦 4. Centralized Error Handling & Logging

### 4.1 Logger Utility (`src/utils/logger.js`)

Use a structured logger for development & production:

```js
import pino from 'pino';

const logger = pino({
  level: process.env.LOG_LEVEL || 'info',
  transport: process.env.NODE_ENV === 'development' 
    ? { target: 'pino-pretty' } 
    : undefined
});

export default logger;
```

### 4.2 Error Middleware (`src/middlewares/errorHandler.js`)

```js
import logger from '../utils/logger.js';

function errorHandler(err, req, res, next) {
  logger.error(err);
  const status = err.statusCode || 500;
  res.status(status).json({
    success: false,
    error: err.message || 'Internal Server Error',
  });
}

export default errorHandler;
```

### 4.3 404 Middleware (`src/middlewares/notFound.js`)

```js
function notFound(req, res, next) {
  res.status(404).json({ success: false, error: 'Resource not found' });
}

export default notFound;
```

---

## 🏗️ 5. App & Server Entrypoints

### `src/app.js`

```js
import express from 'express';
import hospitalRoutes from './routes/hospital.js';
import doctorRoutes  from './routes/doctor.js';
import notFound      from './middlewares/notFound.js';
import errorHandler  from './middlewares/errorHandler.js';

const app = express();

app.use(express.json());

// API routes
app.use('/api/hospitals', hospitalRoutes);
app.use('/api/doctors',    doctorRoutes);

// 404 & Error handlers
app.use(notFound);
app.use(errorHandler);

export default app;
```

### `src/server.js`

```js
import dotenv from 'dotenv';
dotenv.config({ path: '.env' });

import connectDB from './config/database.js';
import { PORT, NODE_ENV } from './config/constants.js';
import logger from './utils/logger.js';
import app from './app.js';

(async () => {
  try {
    await connectDB();

    const server = app.listen(PORT, () => {
      logger.info(`Server running in ${NODE_ENV} mode on port ${PORT}`);
    });

    // Graceful shutdown
    process.on('SIGINT',  () => server.close(() => logger.info('Server stopped (SIGINT)')));
    process.on('SIGTERM', () => server.close(() => logger.info('Server stopped (SIGTERM)')));
  } catch (err) {
    logger.error('Failed to start server:', err);
    process.exit(1);
  }
})();
```

---

## 🛠️ 6. npm Scripts & Dependencies

### `package.json` Scripts

```jsonc
{
  "scripts": {
    "start": "node src/server.js",
    "dev":   "nodemon --watch 'src/**/*.js' src/server.js",
    "lint":  "eslint 'src/**/*.js'",
    "test":  "jest --coverage"
  }
}
```

### Key Dependencies

* **`express`** – Web framework
* **`mongoose`** – MongoDB ODM
* **`dotenv`** – Env var loader
* **`pino`** / **`winston`** – Logger
* **`nodemon`** (dev) – Auto‐reload
* **`eslint`** (dev) – Linting
* **`jest`**, **`supertest`** (dev) – Testing

---

## 📈 Summary

You’ve now set up a **professional** backend foundation with:

* **Clean project structure** and separation of concerns
* **Secure** environment variable management
* **Robust** database connection handling
* **Centralized** logging and error management
* **Resilient** server startup and graceful shutdown
* **Automated** dev workflow with scripts, linting, and testing

With this groundwork, you’re ready to build large‐scale, maintainable, and production‐grade APIs. On to Chapter 07: **Authentication & Authorization**!

```
```
