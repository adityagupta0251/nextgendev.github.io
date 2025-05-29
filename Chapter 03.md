---
title: Chapter 03 – Adding Persistence with MongoDB & Mongoose
---

# Chapter 03 – Adding Persistence with MongoDB & Mongoose 🗄️

In this chapter, we’ll transform our simple Express server into a data-driven API by:

1. **Serving static HTML** alongside dynamic endpoints  
2. **Connecting to MongoDB** with Mongoose  
3. **Defining rich data models** (Category, Product, Order, User, Todo, SubTodo)  
4. **Exporting reusable models** for use in your routes  

---

## 🚀 1. Express Server Boilerplate

Create **`index.js`** at your project root:

```js
const express = require('express');
const { resolve } = require('path');

const app = express();
const port = process.env.PORT || 3010;

// 1) Serve static assets from /static
app.use(express.static('static'));

// 2) Send pages/index.html on GET /
app.get('/', (req, res) => {
  res.sendFile(resolve(__dirname, 'pages/index.html'));
});

// 3) Start listening
app.listen(port, () => {
  console.log(`🚀 Server listening at http://localhost:${port}`);
});
````

* **`express.static('static')`** exposes your CSS, JS, images, etc., from the `static/` folder.
* **`sendFile`** delivers `pages/index.html` for the root route.

---

## 🎨 2. Static HTML Example

Place **`pages/index.html`** alongside:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Hello Express!</title>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width" />
    <link rel="stylesheet" href="./style.css" />
  </head>
  <body>
    <h1>Hello Express!</h1>
    <p>Showing <code>pages/index.html</code></p>
  </body>
</html>
```

Your **`static/style.css`** might contain basic styles:

```css
body { font-family: sans-serif; padding: 2rem; background: #f4f4f4; }
h1 { color: #333; }
```

---

## 🛠 3. Install & Connect Mongoose

```bash
npm install mongoose
```

In **`index.js`** (before routes), add:

```js
const mongoose = require('mongoose');

mongoose
  .connect(process.env.MONGODB_URI || 'mongodb://localhost:27017/nextgendev', {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => console.log('✅ MongoDB connected'))
  .catch(err => console.error('❌ MongoDB connection error:', err));
```

---

## 📦 4. Define Your Schemas & Models

Create a folder **`models/`**, then:

### 4.1 Category Model (`models/category.js`)

```js
import mongoose from 'mongoose';

const categorySchema = new mongoose.Schema({
  name: { type: String, required: true }
}, { timestamps: true });

export const Category = mongoose.model('Category', categorySchema);
```

---

### 4.2 Product Model (`models/product.js`)

```js
import mongoose from 'mongoose';

const productSchema = new mongoose.Schema({
  name:        { type: String,  required: true },
  description: { type: String,  required: true },
  price:       { type: Number,  default: 0 },
  stock:       { type: Number,  default: 0 },
  productImage:{ type: String },
  category:    { type: mongoose.Schema.Types.ObjectId, ref: 'Category' },
  owner:       { type: mongoose.Schema.Types.ObjectId, ref: 'User' }
}, { timestamps: true });

export const Product = mongoose.model('Product', productSchema);
```

---

### 4.3 Order Model (`models/order.js`)

```js
import mongoose from 'mongoose';

const orderItemSchema = new mongoose.Schema({
  productId: { type: mongoose.Schema.Types.ObjectId, ref: 'Product' },
  quantity:  { type: Number, required: true }
});

const orderSchema = new mongoose.Schema({
  buyerInfo: { type: mongoose.Schema.Types.ObjectId, ref: 'User', required: true },
  orderPrice:{ type: Number, default: 0, required: true },
  orderItems:[orderItemSchema],
  address:   { type: String, required: true },
  status:    { type: String, enum: ['Pending','Cancelled','Delivered'], default: 'Pending' }
}, { timestamps: true });

export const Order = mongoose.model('Order', orderSchema);
```

---

### 4.4 User Model (`models/user.js`)

```js
import mongoose from 'mongoose';

const userSchema = new mongoose.Schema({
  username: { type: String, required: true, unique: true, lowercase: true },
  email:    { type: String, required: true, unique: true, lowercase: true },
  password: {
    type: String,
    required: [true, 'Password is required'],
    minlength: [6, 'Minimum 6 characters'],
    maxlength: [12, 'Maximum 12 characters']
  }
}, { timestamps: true });

export const User = mongoose.model('User', userSchema);
```

---

### 4.5 SubTodo & Todo Models (`models/todo.js`)

```js
import mongoose from 'mongoose';

const subTodoSchema = new mongoose.Schema({
  content:   { type: String, required: true },
  complete:  { type: Boolean, default: false },
  createdBy: { type: mongoose.Schema.Types.ObjectId, ref: 'User' }
}, { timestamps: true });

export const SubTodo = mongoose.model('SubTodo', subTodoSchema);

const todoSchema = new mongoose.Schema({
  content:   { type: String, required: true },
  complete:  { type: Boolean, default: false },
  createdBy: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
  subTodos:  [{ type: mongoose.Schema.Types.ObjectId, ref: 'SubTodo' }]
}, { timestamps: true });

export const Todo = mongoose.model('Todo', todoSchema);
```

---

## 🔍 5. Next Steps

* **Create CRUD routes** in Express to manage Categories, Products, Orders, Users, Todos.
* **Validate inputs** with middleware (e.g., Joi, express-validator).
* **Handle errors** centrally and return consistent API responses.
* **Protect sensitive routes**—add authentication & authorization.
* **Write unit & integration tests** for models and routes.

---

🎉 With MongoDB and Mongoose in place, your backend can now **persist data**, **enforce schemas**, and **scale** as you build out fully featured APIs. Onward to Chapter 04, where we’ll secure and optimize your endpoints!

```
```
