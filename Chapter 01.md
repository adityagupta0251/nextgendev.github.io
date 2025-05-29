📘 **Chapter 01: Basic Backend Setup with Express**
This chapter walks you through creating a minimal Express.js server, covering basic routing, serving dynamic data, and loading configuration via dotenv.

---

## 📂 Project Structure

```
project-root/
│
├── .env               # Environment variables
├── index.js           # Main server file (entry point)
└── README.md          # Documentation
```

---

## 📦 Install Dependencies

```bash
npm install express dotenv
```

---

## 📄 Environment Configuration

Create a `.env` file at project root:

```env
PORT=3499
```

> **Note:** Keeping sensitive or varying config in `.env` ensures you never commit secrets to source control.

---

## 📝 index.js

```js
require('dotenv').config(); // Load .env variables

const express = require('express');
const app = express();

// Fallback port if .env is missing
const port = process.env.PORT || 3499;

// Dummy GitHub user data
const githubData = {
  login: "adityagupta0251",
  id: 166922118,
  avatar_url: "https://avatars.githubusercontent.com/u/166922118?v=4",
  html_url: "https://github.com/adityagupta0251",
  blog: "https://adik0.nextgendev.space/",
  location: "Pakur, Jharkhand",
  bio: "✨ **Aditya Kr | 16**\n> *Full Stack Developer | Indie Coder*\n\n🔧 Firebase & Firestore wizard\n🚀 Cybersecurity & space science explorer",
  twitter_username: "AdiK0_dev",
  public_repos: 13,
  followers: 3,
  following: 6
};

// Basic routes
app.get('/',        (req, res) => res.send('Hello world!'));
app.get('/twitter', (req, res) => res.send('Adik0.dev'));
app.get('/githubData', (req, res) => res.json(githubData));
app.get('/login',   (req, res) => res.send('<h1>Please login at Adik0.dev</h1>'));
app.get('/youtube', (req, res) => res.send('<h2>NextGen Dev</h2>'));

// Start server
app.listen(port, () => {
  console.log(`Server is listening on port ${port}`);
});
```

---

## 📌 Routes Summary

| Method | Endpoint      | Response                             | Description                         |
| ------ | ------------- | ------------------------------------ | ----------------------------------- |
| GET    | `/`           | `Hello world!`                       | Root route                          |
| GET    | `/twitter`    | `Adik0.dev`                          | Simulated Twitter handle            |
| GET    | `/githubData` | JSON with GitHub profile data        | Returns dummy user profile as JSON  |
| GET    | `/login`      | `<h1>Please login at Adik0.dev</h1>` | Simple login page simulation        |
| GET    | `/youtube`    | `<h2>NextGen Dev</h2>`               | Simulated YouTube branding endpoint |

---

## 🚀 Running the Server

1. Ensure `.env` is in place and contains `PORT=3499`.

2. Start the app:

   ```bash
   node index.js
   ```

3. Visit in your browser:

   ```
   http://localhost:3499/
   ```

---

## 📎 Notes

* **dotenv** keeps environment-specific config out of code.
* The `githubData` object simulates API data—handy for frontend testing or learning.

---

## 🛠 Author

**Aditya Kr (X.Adi0251)**
✨ Full Stack Developer | Indie Coder
🐙 GitHub • 🐦 Twitter: @AdiK0\_dev
🌐 Portfolio: [https://adik0.nextgendev.space/](https://adik0.nextgendev.space/)

---
