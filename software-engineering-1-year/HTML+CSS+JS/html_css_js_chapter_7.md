

# Chapter 7 — Asynchronous JavaScript & APIs

> **Goal:** Real-world JS skills — Learn how JavaScript handles tasks that take time, how to fetch data from the internet, and how to build apps that talk to real APIs.

---

## 1 — What is Async JS?

### The Problem: JavaScript is Single-Threaded

JavaScript can only do **one thing at a time**. Imagine a restaurant with one waiter:

```
Synchronous (Blocking):
Customer 1 orders → Waiter goes to kitchen → WAITS 10 min → Serves food
Customer 2 orders → Waiter goes to kitchen → WAITS 10 min → Serves food
Customer 3 is STILL waiting...
```

```
Asynchronous (Non-Blocking):
Customer 1 orders → Waiter sends order to kitchen → Moves on
Customer 2 orders → Waiter sends order to kitchen → Moves on
Customer 3 orders → Waiter sends order to kitchen → Moves on
Kitchen calls back when food is ready → Waiter serves it
```

JavaScript uses the **asynchronous** approach for slow tasks.

### What Takes Time?

- 🌐 Fetching data from an API (network request)
- ⏱️ Timers (`setTimeout`, `setInterval`)
- 📁 Reading files
- 🗄️ Database queries

### Synchronous vs Asynchronous Code

```javascript
// ✅ Synchronous — runs line by line
console.log("Step 1");
console.log("Step 2");
console.log("Step 3");
// Output: Step 1, Step 2, Step 3 (in order)

// ✅ Asynchronous — setTimeout doesn't block
console.log("Step 1");

setTimeout(() => {
  console.log("Step 2 (after 2 seconds)");
}, 2000);

console.log("Step 3");

// Output: Step 1, Step 3, Step 2 (after 2 seconds)
```

> 🧠 **Key Insight:** JavaScript doesn't wait for `setTimeout`. It moves on, and comes back when the timer finishes.

### How It Works: The Event Loop

```
┌──────────────────────────────────────────┐
│              YOUR CODE                    │
│  console.log("A")                        │
│  setTimeout(callback, 2000)              │
│  console.log("B")                        │
└──────────────┬───────────────────────────┘
               │
               ▼
┌──────────────────────┐
│     CALL STACK        │  ← Runs code one line at a time
│  1. console.log("A") │
│  2. setTimeout(...)  │ ──► Sent to Web API (browser handles timer)
│  3. console.log("B") │
└──────────────────────┘
                              After 2 seconds...
┌──────────────────────┐     ┌──────────────┐
│   CALLBACK QUEUE      │ ◄── │   Web API    │
│  callback()           │     └──────────────┘
└──────────┬───────────┘
           │ Event Loop checks: "Is Call Stack empty?"
           ▼ Yes → Move callback to Call Stack
┌──────────────────────┐
│     CALL STACK        │
│  callback()           │  ← Now it runs
└──────────────────────┘
```

### setTimeout & setInterval

```javascript
// ✅ setTimeout — runs ONCE after delay
setTimeout(() => {
  console.log("This runs after 3 seconds");
}, 3000);

// ✅ setInterval — runs REPEATEDLY
let count = 0;
const timer = setInterval(() => {
  count++;
  console.log(`Count: ${count}`);

  if (count === 5) {
    clearInterval(timer); // Stop after 5
  }
}, 1000);

// Output: Count: 1, Count: 2, ... Count: 5 (then stops)
```

### Callback Functions — The Old Way

A **callback** is a function passed to another function, to be called later:

```javascript
function fetchUserData(userId, callback) {
  console.log("Fetching user...");

  setTimeout(() => {
    // Simulating API delay
    const user = { id: userId, name: "Ali", email: "ali@test.com" };
    callback(user); // Call the function back with data
  }, 2000);
}

fetchUserData(1, (user) => {
  console.log("Got user:", user.name);
});

console.log("This runs BEFORE the user data arrives!");
```

### Callback Hell — Why We Needed Something Better

```javascript
// ❌ Nested callbacks = hard to read = "Callback Hell"
getUser(1, (user) => {
  getOrders(user.id, (orders) => {
    getOrderDetails(orders[0], (details) => {
      getShipping(details.id, (shipping) => {
        console.log(shipping);
        // 😵 4 levels deep... imagine 10 levels
      });
    });
  });
});
```

> This is why **Promises** were invented.

---

## 2 — Promises

### What is a Promise?

A Promise is an object that says: **"I'll give you a result later — either success or failure."**

Think of it like ordering food online:
- **Pending** — Order placed, waiting...
- **Fulfilled** — Food delivered! 🎉 (`.then()`)
- **Rejected** — Order cancelled! ❌ (`.catch()`)

```
Promise States:
┌─────────┐
│ PENDING  │ ──── waiting for result
└────┬─────┘
     │
     ├──► FULFILLED (resolved) ──► .then(result)
     │
     └──► REJECTED (error) ──► .catch(error)
```

### Creating a Promise

```javascript
const myPromise = new Promise((resolve, reject) => {
  const success = true; // Simulate success/failure

  setTimeout(() => {
    if (success) {
      resolve("Data loaded successfully! ✅");
    } else {
      reject("Something went wrong! ❌");
    }
  }, 2000);
});

// Using the promise
myPromise
  .then((result) => {
    console.log(result); // "Data loaded successfully! ✅"
  })
  .catch((error) => {
    console.log(error); // Only runs if rejected
  });
```

### Chaining Promises

```javascript
function getUser(id) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({ id: id, name: "Ali" });
    }, 1000);
  });
}

function getOrders(userName) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(["Order-1", "Order-2", "Order-3"]);
    }, 1000);
  });
}

// ✅ Clean chain instead of callback hell
getUser(1)
  .then((user) => {
    console.log("User:", user.name);
    return getOrders(user.name);
  })
  .then((orders) => {
    console.log("Orders:", orders);
  })
  .catch((error) => {
    console.log("Error:", error);
  });
```

> Each `.then()` receives the result from the previous one. Much cleaner than nested callbacks!

---

## 3 — fetch() API

### What is fetch()?

`fetch()` is the modern way to make **HTTP requests** (talk to servers/APIs) from JavaScript. It returns a **Promise**.

```
Your Browser ────fetch()────► API Server
                                │
Your Browser ◄───JSON data──────┘
```

### Basic fetch() Syntax

```javascript
fetch("https://api.example.com/data")
  .then((response) => response.json()) // Convert response to JS object
  .then((data) => {
    console.log(data); // Use the data
  })
  .catch((error) => {
    console.log("Error:", error);
  });
```

### Understanding the Response

```javascript
fetch("https://jsonplaceholder.typicode.com/users/1")
  .then((response) => {
    console.log(response.status);  // 200 (success)
    console.log(response.ok);     // true (status 200-299)
    return response.json();        // Parse JSON body
  })
  .then((user) => {
    console.log(user.name);  // "Leanne Graham"
    console.log(user.email); // "Sincere@april.biz"
  });
```

> 🧠 `response.json()` is **also a Promise** — that's why we need another `.then()`.

### Real Example: Fetch a List of Users

```html
<h2>👥 User List</h2>
<ul id="userList"></ul>

<script>
  const userList = document.getElementById("userList");

  fetch("https://jsonplaceholder.typicode.com/users")
    .then((response) => response.json())
    .then((users) => {
      users.forEach((user) => {
        const li = document.createElement("li");
        li.textContent = `${user.name} — ${user.email}`;
        userList.appendChild(li);
      });
    })
    .catch((error) => {
      userList.innerHTML = `<li>Failed to load users: ${error.message}</li>`;
    });
</script>
```

### Checking for Errors Properly

```javascript
fetch("https://jsonplaceholder.typicode.com/users/999")
  .then((response) => {
    // ⚠️ fetch doesn't throw on 404 errors!
    if (!response.ok) {
      throw new Error(`HTTP Error: ${response.status}`);
    }
    return response.json();
  })
  .then((data) => {
    console.log(data);
  })
  .catch((error) => {
    console.log("Caught:", error.message);
  });
```

> ⚠️ **Important:** `fetch()` only rejects on **network failures** (no internet). A 404 or 500 response is still "successful" to fetch. Always check `response.ok`.

---

## 4 — async/await

### The Cleaner Way to Write Promises

`async/await` is **syntactic sugar** over Promises. Same thing underneath, but reads like normal synchronous code.

```javascript
// ❌ Promise chain (works, but nested)
fetch("https://jsonplaceholder.typicode.com/users/1")
  .then((response) => response.json())
  .then((user) => console.log(user.name))
  .catch((error) => console.log(error));

// ✅ async/await (cleaner, same result)
async function getUser() {
  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/users/1");
    const user = await response.json();
    console.log(user.name);
  } catch (error) {
    console.log("Error:", error);
  }
}

getUser();
```

### The Rules

```javascript
// Rule 1: "await" can ONLY be used inside an "async" function
async function myFunction() {
  const result = await somePromise;
}

// Rule 2: "async" functions ALWAYS return a Promise
async function greet() {
  return "Hello";
}
greet().then((msg) => console.log(msg)); // "Hello"

// Rule 3: Use try/catch for error handling
async function riskyOperation() {
  try {
    const response = await fetch("https://bad-url.invalid");
    const data = await response.json();
  } catch (error) {
    console.log("Something failed:", error.message);
  }
}
```

### Step-by-Step: What "await" Does

```javascript
async function fetchPosts() {
  console.log("1. Starting fetch...");

  const response = await fetch("https://jsonplaceholder.typicode.com/posts");
  // ⏸️ JavaScript PAUSES here until fetch completes
  // Other code outside this function keeps running

  console.log("2. Got response");

  const posts = await response.json();
  // ⏸️ Pauses again until JSON parsing completes

  console.log("3. Got data:", posts.length, "posts");
  return posts;
}

fetchPosts();
console.log("4. This runs BEFORE the fetch finishes!");

// Output order: 1, 4, 2, 3
```

### Multiple API Calls — Sequential vs Parallel

```javascript
// ❌ Sequential — slow (one after another)
async function sequential() {
  const users = await fetch("https://jsonplaceholder.typicode.com/users").then(r => r.json());
  const posts = await fetch("https://jsonplaceholder.typicode.com/posts").then(r => r.json());
  // Total time: users time + posts time
  console.log(users.length, posts.length);
}

// ✅ Parallel — fast (both at the same time)
async function parallel() {
  const [usersRes, postsRes] = await Promise.all([
    fetch("https://jsonplaceholder.typicode.com/users"),
    fetch("https://jsonplaceholder.typicode.com/posts"),
  ]);

  const users = await usersRes.json();
  const posts = await postsRes.json();
  // Total time: whichever takes longer (not both combined)
  console.log(users.length, posts.length);
}
```

> 💡 Use `Promise.all()` when requests don't depend on each other.

---

## 5 — Handling API Errors

### Types of Errors

```
1. Network Error    → No internet, server down (fetch rejects)
2. HTTP Error       → 404 Not Found, 500 Server Error (fetch resolves!)
3. Data Error       → API returns unexpected format
4. Parsing Error    → Invalid JSON
```

### Robust Error Handling Function

```javascript
async function safeFetch(url) {
  try {
    const response = await fetch(url);

    // Check HTTP status
    if (!response.ok) {
      throw new Error(`Server error: ${response.status} ${response.statusText}`);
    }

    // Parse JSON
    const data = await response.json();
    return { success: true, data };
  } catch (error) {
    // Catches network errors, HTTP errors, and JSON parse errors
    return { success: false, error: error.message };
  }
}

// Usage
async function loadUsers() {
  const result = await safeFetch("https://jsonplaceholder.typicode.com/users");

  if (result.success) {
    console.log("Users:", result.data);
  } else {
    console.log("Failed:", result.error);
  }
}

loadUsers();
```

### User-Friendly Error Messages

```javascript
function getErrorMessage(error) {
  if (!navigator.onLine) {
    return "You are offline. Please check your internet connection.";
  }
  if (error.message.includes("404")) {
    return "The requested data was not found.";
  }
  if (error.message.includes("500")) {
    return "Server is having issues. Please try again later.";
  }
  if (error.message.includes("Failed to fetch")) {
    return "Could not connect to the server. Please try again.";
  }
  return "Something went wrong. Please try again.";
}
```

---

## 6 — Loading States & Spinners

### Why Loading States Matter

When you fetch data, there's always a delay. Without feedback, users think the app is broken.

```
User clicks button → ??? → Data appears
                     😕 "Is it working?"

User clicks button → Spinner shows → Data appears
                     😊 "It's loading!"
```

### Loading State Pattern

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <style>
    body { font-family: Arial, sans-serif; padding: 30px; }
    
    /* Spinner */
    .spinner {
      width: 40px;
      height: 40px;
      border: 4px solid #e0e0e0;
      border-top: 4px solid #4a90d9;
      border-radius: 50%;
      animation: spin 0.8s linear infinite;
      margin: 20px auto;
    }
    @keyframes spin {
      to { transform: rotate(360deg); }
    }

    /* States */
    .loading-container { text-align: center; color: #888; }
    .error-container {
      background: #fee;
      color: #c00;
      padding: 15px;
      border-radius: 8px;
      text-align: center;
    }
    .error-container button {
      margin-top: 10px;
      padding: 8px 20px;
      background: #c00;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
    .user-card {
      background: #f9f9f9;
      padding: 15px;
      margin: 10px 0;
      border-radius: 8px;
      border-left: 4px solid #4a90d9;
    }
    .user-card h3 { margin: 0 0 5px; }
    .user-card p { margin: 2px 0; color: #666; font-size: 14px; }
    #loadBtn {
      padding: 12px 25px;
      background: #4a90d9;
      color: white;
      border: none;
      border-radius: 8px;
      font-size: 16px;
      cursor: pointer;
    }
    #loadBtn:disabled { opacity: 0.5; cursor: not-allowed; }
  </style>
</head>
<body>

  <h1>👥 User Directory</h1>
  <button id="loadBtn">Load Users</button>
  <div id="container"></div>

  <script>
    const container = document.getElementById("container");
    const loadBtn = document.getElementById("loadBtn");

    // ✅ Show loading state
    function showLoading() {
      container.innerHTML = `
        <div class="loading-container">
          <div class="spinner"></div>
          <p>Loading users...</p>
        </div>
      `;
    }

    // ✅ Show error state
    function showError(message) {
      container.innerHTML = `
        <div class="error-container">
          <p>⚠️ ${message}</p>
          <button onclick="loadUsers()">Try Again</button>
        </div>
      `;
    }

    // ✅ Show data
    function showUsers(users) {
      container.innerHTML = users
        .map(
          (user) => `
          <div class="user-card">
            <h3>${user.name}</h3>
            <p>📧 ${user.email}</p>
            <p>🏢 ${user.company.name}</p>
            <p>🌍 ${user.address.city}</p>
          </div>
        `
        )
        .join("");
    }

    // ✅ Main function with all 3 states
    async function loadUsers() {
      showLoading();
      loadBtn.disabled = true;

      try {
        const response = await fetch(
          "https://jsonplaceholder.typicode.com/users"
        );

        if (!response.ok) {
          throw new Error(`HTTP ${response.status}`);
        }

        const users = await response.json();

        // Simulate slow connection (remove in production)
        await new Promise((resolve) => setTimeout(resolve, 1500));

        showUsers(users);
      } catch (error) {
        showError("Failed to load users. Please try again.");
      } finally {
        loadBtn.disabled = false;
      }
    }

    loadBtn.addEventListener("click", loadUsers);
  </script>

</body>
</html>
```

### The Three States Every API Call Should Have

```javascript
async function fetchData() {
  // STATE 1: Loading
  showLoading();

  try {
    const data = await fetch(url).then((r) => r.json());
    // STATE 2: Success
    showData(data);
  } catch (error) {
    // STATE 3: Error
    showError(error.message);
  }
}
```

### Skeleton Loading (Modern Pattern)

Instead of spinners, many modern apps show "skeleton" placeholders:

```html
<style>
  .skeleton {
    background: linear-gradient(90deg, #eee 25%, #ddd 50%, #eee 75%);
    background-size: 200% 100%;
    animation: shimmer 1.5s infinite;
    border-radius: 6px;
    height: 20px;
    margin: 8px 0;
  }
  .skeleton.title { width: 60%; height: 24px; }
  .skeleton.text { width: 80%; }
  .skeleton.short { width: 40%; }

  @keyframes shimmer {
    0% { background-position: 200% 0; }
    100% { background-position: -200% 0; }
  }
</style>

<script>
  function showSkeleton() {
    container.innerHTML = `
      <div style="padding: 15px;">
        <div class="skeleton title"></div>
        <div class="skeleton text"></div>
        <div class="skeleton short"></div>
      </div>
    `.repeat(3); // Show 3 skeleton cards
  }
</script>
```

---

## 7 — Mini-Project: Weather App

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Weather App</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      font-family: 'Segoe UI', sans-serif;
      background: linear-gradient(135deg, #74b9ff, #0984e3);
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 20px;
    }
    .app {
      background: rgba(255, 255, 255, 0.95);
      border-radius: 20px;
      padding: 40px;
      width: 100%;
      max-width: 420px;
      box-shadow: 0 10px 40px rgba(0,0,0,0.2);
      text-align: center;
    }
    h1 { margin-bottom: 25px; color: #2d3436; }

    /* Search */
    .search-box {
      display: flex;
      gap: 10px;
      margin-bottom: 25px;
    }
    .search-box input {
      flex: 1;
      padding: 12px 18px;
      border: 2px solid #ddd;
      border-radius: 10px;
      font-size: 15px;
      outline: none;
      transition: border-color 0.3s;
    }
    .search-box input:focus { border-color: #0984e3; }
    .search-box button {
      padding: 12px 20px;
      background: #0984e3;
      color: white;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      font-size: 15px;
      transition: background 0.3s;
    }
    .search-box button:hover { background: #0770c2; }
    .search-box button:disabled { opacity: 0.5; cursor: not-allowed; }

    /* Weather Card */
    .weather-card { padding: 20px 0; }
    .weather-icon { font-size: 64px; margin-bottom: 10px; }
    .city-name { font-size: 28px; font-weight: bold; color: #2d3436; }
    .temperature {
      font-size: 48px;
      font-weight: bold;
      color: #0984e3;
      margin: 10px 0;
    }
    .description {
      font-size: 18px;
      color: #636e72;
      text-transform: capitalize;
      margin-bottom: 20px;
    }
    .details {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 15px;
      margin-top: 15px;
    }
    .detail-item {
      background: #f0f8ff;
      padding: 12px;
      border-radius: 10px;
    }
    .detail-item .label {
      font-size: 12px;
      color: #888;
      margin-bottom: 4px;
    }
    .detail-item .value {
      font-size: 18px;
      font-weight: bold;
      color: #2d3436;
    }

    /* Loading spinner */
    .spinner {
      width: 50px;
      height: 50px;
      border: 4px solid #e0e0e0;
      border-top: 4px solid #0984e3;
      border-radius: 50%;
      animation: spin 0.8s linear infinite;
      margin: 30px auto;
    }
    @keyframes spin { to { transform: rotate(360deg); } }
    .loading-text { color: #888; margin-top: 10px; }

    /* Error */
    .error-box {
      background: #ffe0e0;
      color: #c0392b;
      padding: 15px;
      border-radius: 10px;
      margin-top: 10px;
    }

    /* Initial state */
    .initial-message {
      color: #b2bec3;
      font-size: 16px;
      padding: 30px 0;
    }

    /* Recent searches */
    .recent {
      margin-top: 20px;
      text-align: left;
    }
    .recent h3 {
      font-size: 14px;
      color: #888;
      margin-bottom: 8px;
    }
    .recent-list {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
    }
    .recent-tag {
      background: #f0f0f0;
      padding: 5px 12px;
      border-radius: 20px;
      font-size: 13px;
      cursor: pointer;
      border: none;
      transition: background 0.2s;
    }
    .recent-tag:hover { background: #dfe6e9; }
  </style>
</head>
<body>

  <div class="app">
    <h1>🌤️ Weather App</h1>

    <!-- Search -->
    <div class="search-box">
      <input type="text" id="cityInput" placeholder="Enter city name..." />
      <button id="searchBtn">Search</button>
    </div>

    <!-- Result Container -->
    <div id="result">
      <div class="initial-message">
        🔍 Search for a city to see the weather
      </div>
    </div>

    <!-- Recent Searches -->
    <div class="recent" id="recentSection" style="display: none;">
      <h3>Recent Searches</h3>
      <div class="recent-list" id="recentList"></div>
    </div>
  </div>

  <script>
    // ========== Configuration ==========
    // Using wttr.in — a FREE weather API that needs NO API key!
    const API_BASE = "https://wttr.in";

    // ========== DOM Elements ==========
    const cityInput = document.getElementById("cityInput");
    const searchBtn = document.getElementById("searchBtn");
    const result = document.getElementById("result");
    const recentSection = document.getElementById("recentSection");
    const recentList = document.getElementById("recentList");

    // ========== Recent Searches (from localStorage) ==========
    let recentSearches = JSON.parse(localStorage.getItem("recentCities")) || [];

    function saveRecentSearch(city) {
      // Remove if already exists, then add to front
      recentSearches = recentSearches.filter(
        (c) => c.toLowerCase() !== city.toLowerCase()
      );
      recentSearches.unshift(city);

      // Keep only last 5
      if (recentSearches.length > 5) recentSearches.pop();

      localStorage.setItem("recentCities", JSON.stringify(recentSearches));
      renderRecent();
    }

    function renderRecent() {
      if (recentSearches.length === 0) {
        recentSection.style.display = "none";
        return;
      }
      recentSection.style.display = "block";
      recentList.innerHTML = recentSearches
        .map(
          (city) =>
            `<button class="recent-tag" onclick="fetchWeather('${city}')">${city}</button>`
        )
        .join("");
    }

    // ========== Weather Icons ==========
    function getWeatherEmoji(description) {
      const desc = description.toLowerCase();
      if (desc.includes("sunny") || desc.includes("clear")) return "☀️";
      if (desc.includes("partly cloudy")) return "⛅";
      if (desc.includes("cloudy") || desc.includes("overcast")) return "☁️";
      if (desc.includes("rain") || desc.includes("drizzle")) return "🌧️";
      if (desc.includes("thunder") || desc.includes("storm")) return "⛈️";
      if (desc.includes("snow")) return "❄️";
      if (desc.includes("fog") || desc.includes("mist")) return "🌫️";
      return "🌡️";
    }

    // ========== Show Loading ==========
    function showLoading() {
      result.innerHTML = `
        <div>
          <div class="spinner"></div>
          <p class="loading-text">Fetching weather data...</p>
        </div>
      `;
    }

    // ========== Show Error ==========
    function showError(message) {
      result.innerHTML = `
        <div class="error-box">
          ⚠️ ${message}
        </div>
      `;
    }

    // ========== Show Weather ==========
    function showWeather(data, cityName) {
      const current = data.current_condition[0];
      const tempC = current.temp_C;
      const description = current.weatherDesc[0].value;
      const humidity = current.humidity;
      const windSpeed = current.windspeedKmph;
      const feelsLike = current.FeelsLikeC;
      const uvIndex = current.uvIndex;

      result.innerHTML = `
        <div class="weather-card">
          <div class="weather-icon">${getWeatherEmoji(description)}</div>
          <div class="city-name">${cityName}</div>
          <div class="temperature">${tempC}°C</div>
          <div class="description">${description}</div>
          <div class="details">
            <div class="detail-item">
              <div class="label">Feels Like</div>
              <div class="value">${feelsLike}°C</div>
            </div>
            <div class="detail-item">
              <div class="label">Humidity</div>
              <div class="value">${humidity}%</div>
            </div>
            <div class="detail-item">
              <div class="label">Wind</div>
              <div class="value">${windSpeed} km/h</div>
            </div>
            <div class="detail-item">
              <div class="label">UV Index</div>
              <div class="value">${uvIndex}</div>
            </div>
          </div>
        </div>
      `;
    }

    // ========== Fetch Weather (Main Function) ==========
    async function fetchWeather(city) {
      const cityName = city || cityInput.value.trim();

      if (cityName === "") {
        showError("Please enter a city name.");
        return;
      }

      showLoading();
      searchBtn.disabled = true;

      try {
        const response = await fetch(
          `${API_BASE}/${encodeURIComponent(cityName)}?format=j1`
        );

        if (!response.ok) {
          throw new Error(`City not found (${response.status})`);
        }

        const data = await response.json();

        // Check if valid data
        if (!data.current_condition || data.current_condition.length === 0) {
          throw new Error("No weather data available for this city.");
        }

        showWeather(data, cityName);
        saveRecentSearch(cityName);
        cityInput.value = "";
      } catch (error) {
        if (!navigator.onLine) {
          showError("You are offline. Check your internet connection.");
        } else {
          showError(`Could not find weather for "${cityName}". Try another city.`);
        }
      } finally {
        searchBtn.disabled = false;
      }
    }

    // ========== Event Listeners ==========
    searchBtn.addEventListener("click", () => fetchWeather());

    cityInput.addEventListener("keypress", (e) => {
      if (e.key === "Enter") fetchWeather();
    });

    // ========== Initialize ==========
    renderRecent();
  </script>

</body>
</html>
```

### What This Project Covers

```
✅ async/await         → fetchWeather() is an async function
✅ fetch() API         → Real API call to wttr.in
✅ Promises            → await pauses until data arrives
✅ Error Handling      → try/catch, network check, HTTP status check
✅ Loading States      → Spinner while fetching
✅ localStorage        → Recent searches persist after refresh
✅ Data-Driven UI      → Weather card built from API response data
✅ DOM Manipulation    → Dynamic HTML generation
✅ Events              → Click, keypress, dynamic button clicks
```

---

## 📌 Chapter Summary

| Concept | What You Learned |
|---------|-----------------|
| Async JS | JavaScript doesn't wait for slow tasks, uses callbacks/promises |
| Callbacks | Functions passed to run later — leads to callback hell |
| Promises | `.then()` for success, `.catch()` for errors, chainable |
| fetch() | Modern API for HTTP requests, returns a Promise |
| response.json() | Parses response body into JS object (also a Promise) |
| async/await | Cleaner syntax for Promises, use `try/catch` for errors |
| Promise.all() | Run multiple async tasks in parallel |
| Error Handling | Check `response.ok`, use try/catch, show user-friendly messages |
| Loading States | Spinners, skeletons, disabled buttons during fetch |
| Event Loop | How JS handles async tasks behind the scenes |

---