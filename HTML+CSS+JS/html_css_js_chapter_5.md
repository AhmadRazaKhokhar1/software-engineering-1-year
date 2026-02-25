

# Chapter 5 — Data Handling & Browser Storage

> **Goal:** Persist user data — Learn how to work with arrays, objects, JSON, and browser storage so your app remembers things even after refresh.

---

## 1 — Arrays & Objects (Real Use Cases)

### Why This Matters

In real apps, you don't just store a single name or number. You store **lists** (arrays) and **structured records** (objects). Think of a contact list, a shopping cart, or a list of tasks.

---

### Arrays — Ordered Lists

An array holds multiple values in a single variable.

```javascript
// Creating an array
const fruits = ["Apple", "Banana", "Mango"];

// Accessing items (index starts at 0)
console.log(fruits[0]); // "Apple"
console.log(fruits[2]); // "Mango"

// Length
console.log(fruits.length); // 3
```

### Essential Array Methods

```javascript
const tasks = ["Buy milk", "Study JS", "Exercise"];

// ✅ Add to end
tasks.push("Read book");
console.log(tasks); // ["Buy milk", "Study JS", "Exercise", "Read book"]

// ✅ Remove from end
tasks.pop();
console.log(tasks); // ["Buy milk", "Study JS", "Exercise"]

// ✅ Remove from beginning
tasks.shift();
console.log(tasks); // ["Study JS", "Exercise"]

// ✅ Add to beginning
tasks.unshift("Wake up early");
console.log(tasks); // ["Wake up early", "Study JS", "Exercise"]

// ✅ Find index
console.log(tasks.indexOf("Study JS")); // 1

// ✅ Remove specific item (splice)
tasks.splice(1, 1); // At index 1, remove 1 item
console.log(tasks); // ["Wake up early", "Exercise"]

// ✅ Check if item exists
console.log(tasks.includes("Exercise")); // true
```

### Looping Through Arrays

```javascript
const colors = ["Red", "Green", "Blue"];

// forEach — most common
colors.forEach(function (color, index) {
  console.log(`${index}: ${color}`);
});
// 0: Red
// 1: Green
// 2: Blue

// Arrow function version
colors.forEach((color) => console.log(color));
```

### map() — Transform Every Item

```javascript
const numbers = [1, 2, 3, 4];
const doubled = numbers.map((num) => num * 2);
console.log(doubled); // [2, 4, 6, 8]
```

> 💡 `map()` **returns a new array**. The original stays untouched.

### filter() — Keep Only What Matches

```javascript
const scores = [45, 78, 92, 33, 88];
const passed = scores.filter((score) => score >= 50);
console.log(passed); // [78, 92, 88]
```

---

### Objects — Structured Data

An object stores data as **key-value pairs**.

```javascript
const student = {
  name: "Ali",
  age: 20,
  course: "Web Development",
  isActive: true,
};

// Accessing values
console.log(student.name); // "Ali"
console.log(student["course"]); // "Web Development"

// Updating
student.age = 21;

// Adding new property
student.grade = "A";

// Deleting
delete student.isActive;

console.log(student);
// { name: "Ali", age: 21, course: "Web Development", grade: "A" }
```

### Looping Through Objects

```javascript
const car = { brand: "Toyota", model: "Corolla", year: 2022 };

// for...in loop
for (let key in car) {
  console.log(`${key}: ${car[key]}`);
}
// brand: Toyota
// model: Corolla
// year: 2022

// Object.keys() — get all keys as array
console.log(Object.keys(car)); // ["brand", "model", "year"]

// Object.values() — get all values
console.log(Object.values(car)); // ["Toyota", "Corolla", 2022]
```

### 🔥 Real Use Case: Array of Objects

This is how **real data** looks in apps:

```javascript
const products = [
  { id: 1, name: "Laptop", price: 999, inStock: true },
  { id: 2, name: "Phone", price: 699, inStock: false },
  { id: 3, name: "Tablet", price: 499, inStock: true },
];

// Get names of in-stock products
const available = products
  .filter((product) => product.inStock)
  .map((product) => product.name);

console.log(available); // ["Laptop", "Tablet"]

// Find a specific product
const phone = products.find((p) => p.id === 2);
console.log(phone.name); // "Phone"
```

---

## 2 — JSON Basics

### What is JSON?

**JSON** (JavaScript Object Notation) is a **text format** for storing and exchanging data. It looks almost like a JavaScript object — but it's a **string**.

> 🧠 Think of JSON as the **universal language** that browsers, servers, and APIs all understand.

### JSON vs JavaScript Object

```javascript
// JavaScript Object (in memory)
const user = { name: "Sara", age: 25 };

// JSON String (text format)
const jsonString = '{"name": "Sara", "age": 25}';
```

**Key Differences:**
| Feature | JS Object | JSON |
|---------|-----------|------|
| Keys | Can be unquoted | Must be **double-quoted** |
| Values | Any JS type | Strings, numbers, booleans, arrays, objects, null |
| Functions | Allowed | ❌ Not allowed |
| Usage | In your code | Data transfer & storage |

### Converting Between JS Objects and JSON

```javascript
const student = { name: "Ali", age: 20, skills: ["HTML", "CSS", "JS"] };

// ✅ Object → JSON string (Stringify)
const jsonStr = JSON.stringify(student);
console.log(jsonStr);
// '{"name":"Ali","age":20,"skills":["HTML","CSS","JS"]}'
console.log(typeof jsonStr); // "string"

// ✅ JSON string → Object (Parse)
const parsed = JSON.parse(jsonStr);
console.log(parsed.name); // "Ali"
console.log(typeof parsed); // "object"
```

### Why JSON Matters

```
┌──────────┐    JSON string    ┌──────────┐
│  Browser  │ ───────────────► │  Server  │
│ (Your JS) │ ◄─────────────── │  (API)   │
└──────────┘    JSON string    └──────────┘
        ▼
  localStorage  ← also stores JSON strings
```

- **APIs** send and receive JSON
- **localStorage** can only store strings → you must use JSON
- **Config files** (package.json) use JSON

---

## 3 — localStorage & sessionStorage

### The Problem

By default, when you **refresh the page**, all your JavaScript variables are gone. The data dies.

### The Solution — Browser Storage

The browser gives you two storage boxes:

| Feature | `localStorage` | `sessionStorage` |
|---------|----------------|-------------------|
| Data persists | ✅ Even after browser closes | ❌ Only until tab closes |
| Shared across tabs | ✅ Yes | ❌ No |
| Storage limit | ~5-10 MB | ~5-10 MB |
| Use case | User preferences, saved data | Temporary form data |

### localStorage — The Basics

```javascript
// ✅ Save data
localStorage.setItem("username", "Ali");

// ✅ Read data
const name = localStorage.getItem("username");
console.log(name); // "Ali"

// ✅ Remove one item
localStorage.removeItem("username");

// ✅ Clear everything
localStorage.clear();
```

> ⚠️ **localStorage only stores STRINGS!**

### The String Problem & JSON Fix

```javascript
// ❌ WRONG — Storing an array directly
localStorage.setItem("colors", ["red", "green", "blue"]);
console.log(localStorage.getItem("colors")); // "red,green,blue" — BROKEN!

// ✅ CORRECT — Convert to JSON first
const colors = ["red", "green", "blue"];
localStorage.setItem("colors", JSON.stringify(colors));

// Reading it back
const stored = localStorage.getItem("colors");
const parsed = JSON.parse(stored);
console.log(parsed); // ["red", "green", "blue"] ✅
console.log(parsed[0]); // "red" ✅
```

### Storing Objects

```javascript
const user = { name: "Sara", theme: "dark", lang: "en" };

// Save
localStorage.setItem("user", JSON.stringify(user));

// Load
const savedUser = JSON.parse(localStorage.getItem("user"));
console.log(savedUser.theme); // "dark"
```

### sessionStorage — Same API

```javascript
// Works exactly the same way
sessionStorage.setItem("tempData", "hello");
console.log(sessionStorage.getItem("tempData")); // "hello"
// Dies when you close the tab
```

### Checking if Data Exists

```javascript
const data = localStorage.getItem("user");

if (data) {
  const user = JSON.parse(data);
  console.log("Welcome back, " + user.name);
} else {
  console.log("No saved data found.");
}
```

---

## 4 — Saving UI State

### What is "UI State"?

UI state = **what the user sees and has selected** on the page. Examples:

- Is dark mode on?
- Which tab is active?
- Is the sidebar open?
- What has the user typed?

### Example: Remember Dark Mode Preference

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <style>
    body { transition: all 0.3s ease; padding: 20px; font-family: Arial; }
    .dark { background: #1a1a2e; color: #e0e0e0; }
    .dark button { background: #e94560; color: white; border: none; }
    button { padding: 10px 20px; cursor: pointer; font-size: 16px; border-radius: 5px; }
  </style>
</head>
<body>
  <h1>UI State Demo</h1>
  <button id="themeBtn">Toggle Dark Mode</button>

  <script>
    const body = document.body;
    const btn = document.getElementById("themeBtn");

    // ✅ Load saved theme on page load
    const savedTheme = localStorage.getItem("theme");
    if (savedTheme === "dark") {
      body.classList.add("dark");
    }

    // ✅ Toggle and save
    btn.addEventListener("click", () => {
      body.classList.toggle("dark");

      if (body.classList.contains("dark")) {
        localStorage.setItem("theme", "dark");
      } else {
        localStorage.setItem("theme", "light");
      }
    });
  </script>
</body>
</html>
```

> 🎯 **Result:** User switches to dark mode → refreshes page → dark mode is still on!

### Example: Remember User's Name

```html
<input type="text" id="nameInput" placeholder="Enter your name" />
<p id="greeting"></p>

<script>
  const input = document.getElementById("nameInput");
  const greeting = document.getElementById("greeting");

  // Load saved name
  const savedName = localStorage.getItem("userName");
  if (savedName) {
    input.value = savedName;
    greeting.textContent = `Welcome back, ${savedName}!`;
  }

  // Save as user types
  input.addEventListener("input", () => {
    localStorage.setItem("userName", input.value);
    greeting.textContent = `Hello, ${input.value}!`;
  });
</script>
```

---

## 5 — Simple Data-Driven UI

### What Does "Data-Driven" Mean?

Instead of manually writing HTML for every item, you **store data in an array** and **generate HTML from it using JavaScript**.

```
Data (Array) ──► JavaScript ──► HTML on screen
```

### Example: Render a Product List from Data

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <style>
    .product-card {
      border: 1px solid #ddd;
      padding: 15px;
      margin: 10px 0;
      border-radius: 8px;
    }
    .product-card h3 { margin: 0 0 5px; }
    .price { color: green; font-weight: bold; }
    .out-of-stock { opacity: 0.5; }
  </style>
</head>
<body>
  <h1>🛒 Product List</h1>
  <div id="productContainer"></div>

  <script>
    const products = [
      { id: 1, name: "Wireless Mouse", price: 29.99, inStock: true },
      { id: 2, name: "Mechanical Keyboard", price: 79.99, inStock: true },
      { id: 3, name: "USB-C Hub", price: 45.00, inStock: false },
      { id: 4, name: "Monitor Stand", price: 34.99, inStock: true },
    ];

    const container = document.getElementById("productContainer");

    // ✅ Function to render all products
    function renderProducts(productList) {
      container.innerHTML = ""; // Clear first

      productList.forEach((product) => {
        const card = document.createElement("div");
        card.classList.add("product-card");
        if (!product.inStock) card.classList.add("out-of-stock");

        card.innerHTML = `
          <h3>${product.name}</h3>
          <p class="price">$${product.price.toFixed(2)}</p>
          <p>${product.inStock ? "✅ In Stock" : "❌ Out of Stock"}</p>
        `;

        container.appendChild(card);
      });
    }

    renderProducts(products);
  </script>
</body>
</html>
```

### The Power of Data-Driven UI

Now you can easily:

```javascript
// Show only in-stock items
const available = products.filter((p) => p.inStock);
renderProducts(available);

// Sort by price
const sorted = [...products].sort((a, b) => a.price - b.price);
renderProducts(sorted);

// Search
function search(keyword) {
  const results = products.filter((p) =>
    p.name.toLowerCase().includes(keyword.toLowerCase())
  );
  renderProducts(results);
}
search("mouse"); // Only shows Wireless Mouse
```

> 🧠 **This is exactly how React works** — data changes → UI re-renders. You're learning the concept before the framework!

---

## 6 — Mini-Project: To-Do App with Persistence

### The Complete Working App

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>To-Do App</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      font-family: 'Segoe UI', sans-serif;
      background: #f0f2f5;
      display: flex;
      justify-content: center;
      padding-top: 50px;
      min-height: 100vh;
    }
    .app {
      background: white;
      width: 400px;
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 4px 20px rgba(0,0,0,0.1);
      height: fit-content;
    }
    h1 { text-align: center; margin-bottom: 20px; color: #333; }

    /* Input area */
    .input-area {
      display: flex;
      gap: 10px;
      margin-bottom: 20px;
    }
    .input-area input {
      flex: 1;
      padding: 10px 15px;
      border: 2px solid #ddd;
      border-radius: 8px;
      font-size: 14px;
      outline: none;
      transition: border-color 0.3s;
    }
    .input-area input:focus { border-color: #4a90d9; }
    .input-area button {
      padding: 10px 20px;
      background: #4a90d9;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-size: 14px;
      transition: background 0.3s;
    }
    .input-area button:hover { background: #357abd; }

    /* Stats */
    .stats {
      display: flex;
      justify-content: space-between;
      margin-bottom: 15px;
      font-size: 13px;
      color: #888;
    }

    /* Task list */
    .task-list { list-style: none; }
    .task-item {
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 12px;
      border-bottom: 1px solid #eee;
      transition: background 0.2s;
    }
    .task-item:hover { background: #f9f9f9; }
    .task-item.completed span {
      text-decoration: line-through;
      color: #aaa;
    }
    .task-item input[type="checkbox"] {
      width: 18px;
      height: 18px;
      cursor: pointer;
    }
    .task-item span { flex: 1; font-size: 15px; }
    .delete-btn {
      background: none;
      border: none;
      color: #e74c3c;
      font-size: 18px;
      cursor: pointer;
      opacity: 0;
      transition: opacity 0.2s;
    }
    .task-item:hover .delete-btn { opacity: 1; }

    /* Empty state */
    .empty {
      text-align: center;
      color: #bbb;
      padding: 30px;
      font-style: italic;
    }

    /* Clear button */
    .clear-btn {
      display: block;
      width: 100%;
      margin-top: 15px;
      padding: 10px;
      background: #e74c3c;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-size: 14px;
    }
    .clear-btn:hover { background: #c0392b; }
  </style>
</head>
<body>

  <div class="app">
    <h1>📝 To-Do App</h1>

    <!-- Input Area -->
    <div class="input-area">
      <input type="text" id="taskInput" placeholder="Add a new task..." />
      <button id="addBtn">Add</button>
    </div>

    <!-- Stats -->
    <div class="stats">
      <span id="totalTasks">Total: 0</span>
      <span id="completedTasks">Completed: 0</span>
    </div>

    <!-- Task List -->
    <ul class="task-list" id="taskList"></ul>

    <!-- Clear All -->
    <button class="clear-btn" id="clearBtn">🗑️ Clear All Tasks</button>
  </div>

  <script>
    // ========== DOM Elements ==========
    const taskInput = document.getElementById("taskInput");
    const addBtn = document.getElementById("addBtn");
    const taskList = document.getElementById("taskList");
    const clearBtn = document.getElementById("clearBtn");
    const totalDisplay = document.getElementById("totalTasks");
    const completedDisplay = document.getElementById("completedTasks");

    // ========== Load tasks from localStorage ==========
    let tasks = JSON.parse(localStorage.getItem("tasks")) || [];

    // ========== Save tasks to localStorage ==========
    function saveTasks() {
      localStorage.setItem("tasks", JSON.stringify(tasks));
    }

    // ========== Update stats ==========
    function updateStats() {
      totalDisplay.textContent = `Total: ${tasks.length}`;
      const done = tasks.filter((t) => t.completed).length;
      completedDisplay.textContent = `Completed: ${done}`;
    }

    // ========== Render all tasks ==========
    function renderTasks() {
      taskList.innerHTML = "";

      if (tasks.length === 0) {
        taskList.innerHTML = '<li class="empty">No tasks yet. Add one above!</li>';
        updateStats();
        return;
      }

      tasks.forEach((task, index) => {
        const li = document.createElement("li");
        li.classList.add("task-item");
        if (task.completed) li.classList.add("completed");

        li.innerHTML = `
          <input type="checkbox" ${task.completed ? "checked" : ""} />
          <span>${task.text}</span>
          <button class="delete-btn">✕</button>
        `;

        // ✅ Toggle complete
        const checkbox = li.querySelector("input[type='checkbox']");
        checkbox.addEventListener("change", () => {
          tasks[index].completed = checkbox.checked;
          saveTasks();
          renderTasks();
        });

        // ✅ Delete task
        const deleteBtn = li.querySelector(".delete-btn");
        deleteBtn.addEventListener("click", () => {
          tasks.splice(index, 1);
          saveTasks();
          renderTasks();
        });

        taskList.appendChild(li);
      });

      updateStats();
    }

    // ========== Add new task ==========
    function addTask() {
      const text = taskInput.value.trim();
      if (text === "") return;

      tasks.push({
        text: text,
        completed: false,
      });

      taskInput.value = "";
      saveTasks();
      renderTasks();
      taskInput.focus();
    }

    // ========== Event Listeners ==========
    addBtn.addEventListener("click", addTask);

    // Add task on Enter key
    taskInput.addEventListener("keypress", (e) => {
      if (e.key === "Enter") addTask();
    });

    // Clear all tasks
    clearBtn.addEventListener("click", () => {
      if (confirm("Are you sure you want to delete all tasks?")) {
        tasks = [];
        saveTasks();
        renderTasks();
      }
    });

    // ========== Initial Render ==========
    renderTasks();
  </script>

</body>
</html>
```

### How This Project Uses Everything from Chapter 5

```
✅ Arrays & Objects    → tasks = [{ text: "...", completed: false }]
✅ JSON                → JSON.stringify() and JSON.parse()
✅ localStorage        → saveTasks() and loading on page start
✅ Saving UI State     → Checkbox state persists after refresh
✅ Data-Driven UI      → renderTasks() generates HTML from data
```

---

## 7 — MCQs (Multiple Choice Questions)

**Q1.** What does `JSON.stringify()` do?

- a) Converts JSON to an object
- b) Converts an object to a JSON string ✅
- c) Deletes JSON data
- d) Validates JSON syntax

---

**Q2.** What does `localStorage.getItem("key")` return if the key doesn't exist?

- a) `undefined`
- b) `0`
- c) `null` ✅
- d) An error

---

**Q3.** Which method removes the last item from an array?

- a) `shift()`
- b) `splice()`
- c) `pop()` ✅
- d) `delete()`

---

**Q4.** What is the correct way to store an array in localStorage?

- a) `localStorage.setItem("arr", [1,2,3])`
- b) `localStorage.setItem("arr", JSON.stringify([1,2,3]))` ✅
- c) `localStorage.setArray("arr", [1,2,3])`
- d) `localStorage.push([1,2,3])`

---

**Q5.** What does `filter()` return?

- a) A single item
- b) A boolean
- c) A new array with items that pass the test ✅
- d) The original array, modified

---

**Q6.** How long does `sessionStorage` data last?

- a) Forever
- b) 24 hours
- c) Until the browser tab is closed ✅
- d) Until the computer restarts

---

**Q7.** Which is NOT a valid JSON value?

- a) `"hello"`
- b) `42`
- c) `function() {}` ✅
- d) `null`

---

**Q8.** What does `map()` do?

- a) Finds a single element
- b) Transforms each element and returns a new array ✅
- c) Removes elements that don't match
- d) Sorts the array

---

**Q9.** What will `Object.keys({a: 1, b: 2})` return?

- a) `[1, 2]`
- b) `["a", "b"]` ✅
- c) `{a, b}`
- d) `"a, b"`

---

**Q10.** In the To-Do app, why do we call `renderTasks()` after every change?

- a) To restart the app
- b) To clear localStorage
- c) To update the displayed HTML based on the current data ✅
- d) To validate the input

---

## 8 — Short Answer Questions

**Q1.** What is the difference between `localStorage` and `sessionStorage`? When would you use each?

> **Answer:** `localStorage` persists data even after the browser is closed — use it for saving user preferences like theme or saved tasks. `sessionStorage` only keeps data until the browser tab is closed — use it for temporary data like a multi-step form's progress that shouldn't persist forever.

---

**Q2.** Why can't we store arrays or objects directly in localStorage? How do we solve this?

> **Answer:** `localStorage` can only store strings. If you store an array directly, it gets converted to a comma-separated string and loses its structure. The solution is to use `JSON.stringify()` before saving and `JSON.parse()` when reading back.

---

**Q3.** What is the difference between `map()` and `forEach()`?

> **Answer:** `forEach()` loops through an array and performs an action but returns nothing (`undefined`). `map()` also loops through but **returns a new array** with transformed values. Use `map()` when you need a new array; use `forEach()` when you just want to do something (like logging or updating DOM).

---

**Q4.** What does "data-driven UI" mean and why is it important?

> **Answer:** Data-driven UI means the HTML on the screen is **generated from data** (arrays/objects) using JavaScript, rather than being hardcoded. It's important because when data changes (add/delete/update), you just update the data and re-render. This is the foundation of how frameworks like React work.

---

**Q5.** In the To-Do app, explain the flow that happens when a user adds a new task.

> **Answer:** When the user clicks "Add" or presses Enter: (1) the input value is read and trimmed, (2) a new task object `{text, completed: false}` is pushed to the `tasks` array, (3) the input is cleared, (4) `saveTasks()` writes the updated array to localStorage using `JSON.stringify()`, (5) `renderTasks()` clears the list and rebuilds all HTML from the `tasks` array, and (6) stats are updated.

---

## 📌 Chapter Summary

| Concept | What You Learned |
|---------|-----------------|
| Arrays | Store lists, use `push`, `pop`, `map`, `filter`, `forEach` |
| Objects | Store structured data with key-value pairs |
| JSON | `JSON.stringify()` to save, `JSON.parse()` to load |
| localStorage | Persistent browser storage (survives refresh & close) |
| sessionStorage | Temporary storage (dies when tab closes) |
| UI State | Save user preferences (theme, inputs) and restore on load |
| Data-Driven UI | Generate HTML from data arrays — foundation for React |

---