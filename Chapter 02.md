---
title: Chapter 02 – Building & Integrating Your Jokes API
---

# Chapter 02 – Building & Integrating Your Jokes API 🎉

In this chapter, you'll learn how to create a playful Jokes API with **Node.js** and **Express**, serve a static frontend, and seamlessly connect both ends for a delightful user experience.

---

## 🚀 1. Project Structure

Organize your code for clarity and scalability:

```

my-app/
├── backend/
│   ├── dist/           ← Compiled frontend assets (HTML, CSS, JS)
│   ├── index.js        ← Express server entrypoint
│   └── package.json    ← Backend dependencies & scripts
└── frontend/
├── index.html      ← Main HTML page
├── styles.css      ← Presentational styles
└── app.js          ← Frontend logic (fetch & render)

````

---

## 🎯 2. Backend: Express Server Setup

Create an **`index.js`** inside `backend/`:

```js
import express from 'express';
const app = express();

// ─── 1) Serve Static Frontend ─────────────────────────────────────────────
app.use(express.static('dist'));

// ─── 2) Jokes Data Endpoint ───────────────────────────────────────────────
app.get('/api/jokes', (req, res) => {
  const jokes = [
    { id: 1, question: "Why did the JSON object break up with the key?", answer: "Because it couldn't find the value!" },
    { id: 2, question: "Why do JSON objects love nested structures?",    answer: "Because they love to get deep in relationships!" },
    { id: 3, question: "Why can't JSON make friends?",                  answer: "Because it always has syntax issues!" },
    { id: 4, question: "Why did the array feel lonely?",                answer: "Because it was always indexed, but never had a true connection!" },
    { id: 5, question: "Why did the string feel nervous?",               answer: "Because it was always being quoted!" }
  ];
  res.json(jokes);
});

// ─── 3) Start Listening ─────────────────────────────────────────────────
const port = process.env.PORT || 3000;
app.listen(port, () => {
  console.log(`🚀 Server running at http://localhost:${port}`);
});
````

> **Tip:**
>
> * `express.static('dist')` serves all files in `dist/` at the root URL.
> * Returning `res.json(...)` ensures valid JSON is sent with proper headers.

---

## 🎨 3. Frontend: Static Assets

Place these files in `frontend/`, then copy or bundle them into `backend/dist/`.

### **`index.html`**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Jokes App 🤣</title>
  <link rel="stylesheet" href="styles.css" />
</head>
<body>
  <div class="container">
    <h1>😆 Random Jokes</h1>
    <ul id="jokes-list"></ul>
    <button id="refresh-btn">🔄 Refresh</button>
  </div>
  <script src="app.js"></script>
</body>
</html>
```

### **`styles.css`**

```css
body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: #f0f4f8;
  margin: 0;
  padding: 2rem;
}

.container {
  max-width: 600px;
  margin: 0 auto;
  background: #fff;
  border-radius: 12px;
  padding: 2rem;
  box-shadow: 0 4px 12px rgba(0,0,0,0.05);
}

h1 {
  text-align: center;
  color: #333;
}

#jokes-list {
  list-style: none;
  padding: 0;
}

#jokes-list li {
  margin: 1rem 0;
  padding: 0.75rem;
  border-left: 4px solid #4caf50;
  background: #f9fff9;
}

button {
  display: block;
  margin: 1.5rem auto 0;
  padding: 0.75rem 1.5rem;
  font-size: 1rem;
  border: none;
  border-radius: 8px;
  background: #4caf50;
  color: #fff;
  cursor: pointer;
  transition: background 0.2s ease;
}
button:hover {
  background: #45a049;
}
```

### **`app.js`**

```js
const jokesList = document.getElementById('jokes-list');
const refreshBtn = document.getElementById('refresh-btn');

async function loadJokes() {
  jokesList.innerHTML = '<li>Loading…</li>';
  try {
    const res = await fetch('/api/jokes');
    const jokes = await res.json();
    jokesList.innerHTML = jokes
      .map(j => `
        <li>
          <strong>Q:</strong> ${j.question}<br>
          <em>A:</em> ${j.answer}
        </li>
      `).join('');
  } catch (err) {
    jokesList.innerHTML = '<li>❌ Failed to load jokes.</li>';
    console.error(err);
  }
}

refreshBtn.addEventListener('click', loadJokes);
loadJokes();
```

---

## 🛠 4. Build & Run

1. **Copy or Bundle Frontend**

   ```bash
   # Quick copy for development
   cp frontend/* backend/dist/
   # Or use Webpack/Rollup to output to backend/dist/
   ```

2. **Install & Start Server**

   ```bash
   cd backend
   npm init -y
   npm install express
   node index.js
   ```

3. **View in Browser**
   Open 👉 `http://localhost:3000`

---

## 🌱 5. Next Steps

* **Enable CORS** for cross-origin requests.
* **Add CRUD operations** (POST/DELETE jokes).
* **Integrate a database** (e.g., MongoDB, PostgreSQL).
* **Implement Authentication** (JWT, sessions).
* **Containerize** with Docker for seamless deployments.
* **Write Tests** (Jest, Mocha, Supertest) for reliability.

---

📚 **Summary**
You now have a fully functional Jokes API served alongside a static frontend. This pattern lays the groundwork for more complex full-stack applications, bridging server-side logic with client-side interactivity. Onward to persistent storage and advanced backend features in the next chapter!

```
```
```markdown
---
title: Chapter 02 – Building & Integrating Your Jokes API
---

# Chapter 02.1 – With React Project 🎉

In this chapter, you'll learn how to:

1. Create a playful Jokes API with **Node.js** and **Express**  
2. Serve static assets (HTML/CSS/JS)  
3. Integrate a modern **React** frontend for dynamic rendering  

---

## 🚀 1. Project Structure

```

my-app/
├── backend/
│   ├── dist/           ← Compiled frontend assets (HTML, CSS, JS)
│   ├── index.js        ← Express server entrypoint
│   └── package.json    ← Backend dependencies & scripts
└── frontend/
├── public/         ← Static HTML fallback (optional)
├── src/
│   ├── assets/     ← Logos, images, etc.
│   ├── App.jsx     ← React root component
│   ├── index.jsx   ← React entrypoint
│   ├── index.css   ← Global styles
│   └── App.css     ← Component styles
├── package.json    ← Frontend dependencies & scripts
└── vite.config.js  ← Vite config (for React)

````

---

## 🎯 2. Backend: Express Server (`backend/index.js`)

```js
import express from 'express';
const app = express();

// ─── 1) Serve Static Frontend ────────────────────────────────────────
app.use(express.static('dist'));

// ─── 2) Jokes Data Endpoint ─────────────────────────────────────────
app.get('/api/jokes', (req, res) => {
  const jokes = [
    { id: 1, question: "Why did the JSON object break up with the key?", answer: "Because it couldn't find the value!" },
    { id: 2, question: "Why do JSON objects love nested structures?",    answer: "Because they love to get deep in relationships!" },
    { id: 3, question: "Why can't JSON make friends?",                  answer: "Because it always has syntax issues!" },
    { id: 4, question: "Why did the array feel lonely?",                answer: "Because it was always indexed, but never had a true connection!" },
    { id: 5, question: "Why did the string feel nervous?",               answer: "Because it was always being quoted!" }
  ];
  res.json(jokes);
});

// ─── 3) Start Listening ─────────────────────────────────────────────
const port = process.env.PORT || 3000;
app.listen(port, () => {
  console.log(`🚀 Server running at http://localhost:${port}`);
});
````

---

## 🎨 3. React Frontend Integration

Instead of static HTML, let’s spin up a **Vite + React** project that fetches jokes dynamically.

### 3.1 Initialize Frontend

```bash
cd frontend
npm init vite@latest . -- --template react
npm install
npm install axios
```

### 3.2 Entry Point: `src/index.jsx`

```jsx
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './index.css';
import App from './App.jsx';

createRoot(document.getElementById('root')).render(
  <StrictMode>
    <App />
  </StrictMode>,
);
```

### 3.3 Root Component: `src/App.jsx`

```jsx
import { useEffect, useState } from 'react';
import './App.css';
import axios from 'axios';

function App() {
  const [jokes, setJokes] = useState([]);

  useEffect(() => {
    axios
      .get('/api/jokes')
      .then((response) => setJokes(response.data))
      .catch((error) => console.error(error));
  }, []); // Runs once on mount

  return (
    <div className="app-container">
      <h1>Chai Aur Backend ☕️🔥</h1>
      <p>Total Jokes: {jokes.length}</p>
      <div className="jokes-list">
        {jokes.map((joke) => (
          <div key={joke.id} className="joke-card">
            <h3>Q: {joke.question}</h3>
            <p>A: {joke.answer}</p>
          </div>
        ))}
      </div>
    </div>
  );
}

export default App;
```

### 3.4 Styles

* **`src/index.css`** — Global resets or utility classes
* **`src/App.css`** — Component styling

```css
/* index.css */
body {
  margin: 0;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: #fafafa;
}
#root {
  padding: 2rem;
}
```

```css
/* App.css */
.app-container {
  max-width: 800px;
  margin: 0 auto;
  background: #fff;
  border-radius: 12px;
  padding: 2rem;
  box-shadow: 0 4px 12px rgba(0,0,0,0.05);
}
h1 {
  text-align: center;
  color: #333;
}
.jokes-list {
  margin-top: 1.5rem;
}
.joke-card {
  background: #f9fff9;
  border-left: 4px solid #4caf50;
  padding: 1rem;
  margin-bottom: 1rem;
  border-radius: 6px;
}
```

---

## 🛠 4. Build & Deploy

1. **Build React App**

   ```bash
   cd frontend
   npm run build
   ```

   This outputs static files into `frontend/dist/`.

2. **Copy to Backend**

   ```bash
   rm -rf ../backend/dist/*
   cp -R dist/* ../backend/dist/
   ```

3. **Start Express Server**

   ```bash
   cd ../backend
   npm install express
   node index.js
   ```

4. **Browse**
   Visit 👉 `http://localhost:3000` to see your React app fetching jokes in real time!

---

## 🌱 5. Next Steps

* Introduce **CORS** middleware if frontend and backend live on different domains.
* Expand API with **CRUD** operations and **database** integration.
* Secure endpoints with **JWT** or **session** authentication.
* Containerize with **Docker**, orchestrate with **Kubernetes**.
* Write **unit** and **integration tests** (Jest, React Testing Library, Supertest).

---

🎉 **Congratulations!** You now have a modern full-stack setup: an **Express** backend serving a **React** frontend, connected seamlessly via a Jokes API. Onward to Chapter 03, where we’ll integrate a database for persistent storage!
