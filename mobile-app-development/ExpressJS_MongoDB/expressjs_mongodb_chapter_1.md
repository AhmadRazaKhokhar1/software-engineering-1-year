# Chapter 1: Backend Fundamentals & Express Basics

## 🎯 Learning Objectives

By the end of this chapter, you will:
- Understand what backend development is and why it matters
- Learn client-server architecture and how the web works
- Master REST APIs and their design principles
- Understand HTTP methods and status codes
- Set up and configure an Express.js server
- Create basic routes and handle requests
- Understand and implement middleware

---

## 1.1 What is Backend Development?

### Understanding the Full Picture

When you visit a website or use a mobile app, you're interacting with **two main parts**:

1. **Frontend (Client)**: What users see and interact with (buttons, forms, pages)
2. **Backend (Server)**: The invisible part that processes requests, manages data, and sends responses

**Think of it like a restaurant:**
- **Frontend** = The dining area where customers sit and order
- **Backend** = The kitchen where chefs prepare food, manage inventory, and handle orders

### What Does the Backend Do?

The backend is responsible for:

```
✅ Processing business logic
✅ Managing databases (storing and retrieving data)
✅ Authentication & Authorization (who can access what)
✅ Handling file uploads
✅ Sending emails
✅ Processing payments
✅ Communicating with third-party services
```

### Real-World Example

When you log into Instagram:
1. **Frontend**: You type your username and password, click "Log In"
2. **Backend**: 
   - Receives your credentials
   - Checks if they match the database
   - Generates a security token
   - Sends back your profile data
3. **Frontend**: Displays your feed

---

## 1.2 Client-Server Architecture

### The Request-Response Cycle

Every interaction on the web follows this pattern:

```
┌─────────┐                          ┌─────────┐
│         │   1. Request (HTTP)      │         │
│ Client  │ ──────────────────────>  │ Server  │
│(Browser)│                          │(Backend)│
│         │   2. Response (JSON)     │         │
│         │ <──────────────────────  │         │
└─────────┘                          └─────────┘
```

### Example Flow: Getting User Profile

**Step 1: Client sends request**
```
GET /api/users/42 HTTP/1.1
Host: example.com
Authorization: Bearer token123
```

**Step 2: Server processes**
```javascript
// Server receives request
// Finds user with ID 42 in database
// Formats response
```

**Step 3: Server sends response**
```json
{
  "id": 42,
  "name": "John Doe",
  "email": "john@example.com"
}
```

### Components of Client-Server Architecture

| Component | Role | Example |
|-----------|------|---------|
| **Client** | Initiates requests | Browser, Mobile App |
| **Server** | Processes requests | Express.js application |
| **Database** | Stores data | MongoDB |
| **Network** | Connects them | Internet (HTTP/HTTPS) |

---

## 1.3 REST APIs Explained

### What is an API?

**API** = **Application Programming Interface**

It's a contract that defines how two software systems communicate.

**Think of it like a menu at a restaurant:**
- The menu lists what you can order (available endpoints)
- You make a request (order food)
- The kitchen prepares it (server processes)
- You receive your meal (response)

### What is REST?

**REST** = **Representational State Transfer**

REST is a set of **rules** for designing web APIs:

#### The 6 REST Principles

1. **Client-Server Separation**: Frontend and backend are independent
2. **Stateless**: Each request contains all necessary information
3. **Cacheable**: Responses can be cached for performance
4. **Uniform Interface**: Consistent URL structure
5. **Layered System**: Client doesn't know if it's talking directly to the server
6. **Code on Demand** (optional): Server can send executable code

### RESTful Naming Conventions

REST uses **resources** and **HTTP methods**:

```
Resource: /users
```

| Action | HTTP Method | Endpoint | Description |
|--------|-------------|----------|-------------|
| Get all users | GET | `/api/users` | Retrieve list |
| Get one user | GET | `/api/users/:id` | Retrieve single user |
| Create user | POST | `/api/users` | Add new user |
| Update user | PUT/PATCH | `/api/users/:id` | Modify existing |
| Delete user | DELETE | `/api/users/:id` | Remove user |

### Example REST API

```javascript
// GET /api/products
// Response: List of all products
[
  { "id": 1, "name": "Laptop", "price": 999 },
  { "id": 2, "name": "Mouse", "price": 25 }
]

// GET /api/products/1
// Response: Single product
{ "id": 1, "name": "Laptop", "price": 999 }

// POST /api/products
// Request body: { "name": "Keyboard", "price": 75 }
// Response: Newly created product
{ "id": 3, "name": "Keyboard", "price": 75 }
```

---

## 1.4 HTTP Methods & Status Codes

### HTTP Methods (Verbs)

HTTP methods tell the server **what action** to perform:

| Method | Purpose | Safe* | Idempotent** |
|--------|---------|-------|--------------|
| **GET** | Retrieve data | ✅ Yes | ✅ Yes |
| **POST** | Create new resource | ❌ No | ❌ No |
| **PUT** | Replace entire resource | ❌ No | ✅ Yes |
| **PATCH** | Update part of resource | ❌ No | ❌ No |
| **DELETE** | Remove resource | ❌ No | ✅ Yes |

*Safe = Doesn't modify data  
**Idempotent = Multiple identical requests have same effect as one

### HTTP Status Codes

Status codes tell the client **what happened**:

#### ✅ 2xx Success
```
200 OK              → Request succeeded
201 Created         → New resource created
204 No Content      → Success, but no data to return
```

#### 🔄 3xx Redirection
```
301 Moved Permanently → Resource has new URL
304 Not Modified      → Use cached version
```

#### ❌ 4xx Client Errors
```
400 Bad Request       → Invalid data sent
401 Unauthorized      → Authentication required
403 Forbidden         → Authenticated but no permission
404 Not Found         → Resource doesn't exist
409 Conflict          → Request conflicts with current state
422 Unprocessable     → Validation failed
```

#### 🔥 5xx Server Errors
```
500 Internal Server Error → Something broke on server
502 Bad Gateway           → Invalid response from upstream
503 Service Unavailable   → Server temporarily down
```

### Real-World Examples

```javascript
// ✅ Success: User created
POST /api/users
Response: 201 Created
{ "id": 5, "name": "Alice" }

// ❌ Client Error: Email already exists
POST /api/users
Response: 409 Conflict
{ "error": "Email already registered" }

// ❌ Client Error: Invalid email format
POST /api/users
Response: 422 Unprocessable Entity
{ "error": "Invalid email format" }

// ❌ Server Error: Database connection failed
GET /api/users
Response: 500 Internal Server Error
{ "error": "Database unavailable" }
```

---

## 1.5 Express.js Overview

### What is Express.js?

**Express** is a minimal, fast, and flexible **web framework** for Node.js.

**Think of it as:**
- **Node.js** = The engine (like a car engine)
- **Express.js** = The framework that makes building easier (like power steering, GPS, etc.)

### Why Express?

```
✅ Minimal & unopinionated (you choose structure)
✅ Robust routing system
✅ Middleware support
✅ Large ecosystem (plugins, tools)
✅ Easy to learn
✅ Industry standard (used by Netflix, Uber, IBM)
```

### Express vs Alternatives

| Framework | Philosophy | Use Case |
|-----------|------------|----------|
| **Express** | Minimal, flexible | Full control |
| **Fastify** | Performance-focused | High-speed APIs |
| **NestJS** | Opinionated, TypeScript | Enterprise apps |
| **Koa** | Modern, minimal | Lightweight apps |

---

## 1.6 Setting Up an Express Server

### Prerequisites

Make sure you have:
```bash
node --version  # Should be v16 or higher
npm --version   # Should be v8 or higher
```

### Step 1: Initialize Project

```bash
# Create project folder
mkdir my-express-app
cd my-express-app

# Initialize package.json
npm init -y
```

### Step 2: Install Express

```bash
npm install express
```

Your `package.json` should now include:
```json
{
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

### Step 3: Create Your First Server

Create `server.js`:

```javascript
// Import Express
const express = require('express');

// Create an Express application
const app = express();

// Define a port
const PORT = 3000;

// Create a basic route
app.get('/', (req, res) => {
  res.send('Hello, Express!');
});

// Start the server
app.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});
```

### Step 4: Run the Server

```bash
node server.js
```

**Output:**
```
Server is running on http://localhost:3000
```

Open your browser and visit:
```
http://localhost:3000
```

You should see: **"Hello, Express!"**

### Understanding the Code

```javascript
const express = require('express');
// Imports the Express library

const app = express();
// Creates an Express application instance

const PORT = 3000;
// Defines which port the server listens on

app.get('/', (req, res) => { ... });
// Defines a route handler for GET requests to '/'

app.listen(PORT, () => { ... });
// Starts the server and listens for requests
```

---

## 1.7 Routing Basics

### What is Routing?

Routing determines **how an application responds** to client requests at specific **endpoints (URLs)**.

**Structure:**
```javascript
app.METHOD(PATH, HANDLER)
```

- **METHOD**: HTTP method (get, post, put, delete)
- **PATH**: Route path (URL)
- **HANDLER**: Function executed when route is matched

### Basic Routes

```javascript
const express = require('express');
const app = express();

// GET request to homepage
app.get('/', (req, res) => {
  res.send('Homepage');
});

// GET request to /about
app.get('/about', (req, res) => {
  res.send('About Page');
});

// POST request to /submit
app.post('/submit', (req, res) => {
  res.send('Form submitted');
});

// PUT request to /update
app.put('/update', (req, res) => {
  res.send('Data updated');
});

// DELETE request to /delete
app.delete('/delete', (req, res) => {
  res.send('Data deleted');
});

app.listen(3000);
```

### Route Parameters

Capture dynamic values from the URL:

```javascript
// URL: /users/42
app.get('/users/:id', (req, res) => {
  const userId = req.params.id;
  res.send(`User ID: ${userId}`);
});

// URL: /products/laptop/reviews/5
app.get('/products/:productName/reviews/:reviewId', (req, res) => {
  const { productName, reviewId } = req.params;
  res.send(`Product: ${productName}, Review: ${reviewId}`);
});
```

### Query Strings

Access URL query parameters:

```javascript
// URL: /search?term=express&sort=recent
app.get('/search', (req, res) => {
  const { term, sort } = req.query;
  res.send(`Searching for: ${term}, Sorted by: ${sort}`);
});
```

### Sending Different Response Types

```javascript
// Send plain text
app.get('/text', (req, res) => {
  res.send('Plain text response');
});

// Send JSON (most common for APIs)
app.get('/json', (req, res) => {
  res.json({ 
    message: 'Success', 
    data: [1, 2, 3] 
  });
});

// Send status code
app.get('/not-found', (req, res) => {
  res.status(404).json({ error: 'Not found' });
});

// Send HTML
app.get('/html', (req, res) => {
  res.send('<h1>Hello HTML</h1>');
});
```

---

## 1.8 Middleware Concept

### What is Middleware?

Middleware functions are functions that have access to:
- **req** (request object)
- **res** (response object)
- **next** (next middleware function)

**Think of middleware as a series of checkpoints:**

```
Request → Middleware 1 → Middleware 2 → Middleware 3 → Route Handler → Response
            ↓                ↓                ↓
         (Logging)      (Authentication)   (Validation)
```

### Middleware Structure

```javascript
function myMiddleware(req, res, next) {
  // Do something
  console.log('Middleware executed');
  
  // Pass control to next middleware
  next();
}
```

**Important:** Always call `next()` or send a response, otherwise the request hangs!

### Types of Middleware

#### 1. Application-Level Middleware

Runs on every request:

```javascript
const express = require('express');
const app = express();

// This runs for ALL requests
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next();
});

app.get('/', (req, res) => {
  res.send('Home');
});
```

#### 2. Route-Level Middleware

Runs only for specific routes:

```javascript
// Middleware function
const checkAuth = (req, res, next) => {
  const isAuthenticated = true; // Simulate authentication
  
  if (isAuthenticated) {
    next(); // User is authenticated, proceed
  } else {
    res.status(401).json({ error: 'Unauthorized' });
  }
};

// Apply middleware to specific route
app.get('/dashboard', checkAuth, (req, res) => {
  res.send('Dashboard - Protected Route');
});
```

#### 3. Built-in Middleware

Express provides useful built-in middleware:

```javascript
// Parse JSON request bodies
app.use(express.json());

// Parse URL-encoded data (form submissions)
app.use(express.urlencoded({ extended: true }));

// Serve static files (images, CSS, JS)
app.use(express.static('public'));
```

**Example with express.json():**

```javascript
app.use(express.json());

app.post('/api/users', (req, res) => {
  // req.body is automatically parsed
  const { name, email } = req.body;
  res.json({ 
    message: 'User created', 
    user: { name, email } 
  });
});
```

#### 4. Third-Party Middleware

Install and use community middleware:

```bash
npm install morgan
```

```javascript
const morgan = require('morgan');

// HTTP request logger
app.use(morgan('dev'));

// Now every request is logged:
// GET / 200 15.234 ms - 12
```

### Middleware Execution Order

**Order matters!** Middleware executes top-to-bottom:

```javascript
const express = require('express');
const app = express();

// 1. First middleware
app.use((req, res, next) => {
  console.log('First');
  next();
});

// 2. Second middleware
app.use((req, res, next) => {
  console.log('Second');
  next();
});

// 3. Route handler
app.get('/', (req, res) => {
  console.log('Route handler');
  res.send('Done');
});

// Output when you visit /:
// First
// Second
// Route handler
```

### Practical Middleware Examples

#### Example 1: Request Logging

```javascript
app.use((req, res, next) => {
  const timestamp = new Date().toISOString();
  console.log(`[${timestamp}] ${req.method} ${req.url}`);
  next();
});
```

#### Example 2: Request Timing

```javascript
app.use((req, res, next) => {
  req.startTime = Date.now();
  next();
});

app.get('/', (req, res) => {
  const duration = Date.now() - req.startTime;
  res.send(`Request took ${duration}ms`);
});
```

#### Example 3: Authentication Check

```javascript
const requireAuth = (req, res, next) => {
  const token = req.headers.authorization;
  
  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }
  
  // Verify token (simplified)
  if (token === 'valid-token') {
    next();
  } else {
    res.status(403).json({ error: 'Invalid token' });
  }
};

// Protected route
app.get('/api/profile', requireAuth, (req, res) => {
  res.json({ name: 'John Doe' });
});
```

---

## 1.9 Complete Example: Building a Simple API

Let's combine everything:

```javascript
const express = require('express');
const app = express();
const PORT = 3000;

// Middleware
app.use(express.json());

app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next();
});

// Fake database
let users = [
  { id: 1, name: 'Alice', email: 'alice@example.com' },
  { id: 2, name: 'Bob', email: 'bob@example.com' }
];

// Routes

// Get all users
app.get('/api/users', (req, res) => {
  res.json(users);
});

// Get single user
app.get('/api/users/:id', (req, res) => {
  const user = users.find(u => u.id === parseInt(req.params.id));
  
  if (!user) {
    return res.status(404).json({ error: 'User not found' });
  }
  
  res.json(user);
});

// Create user
app.post('/api/users', (req, res) => {
  const { name, email } = req.body;
  
  if (!name || !email) {
    return res.status(400).json({ error: 'Name and email required' });
  }
  
  const newUser = {
    id: users.length + 1,
    name,
    email
  };
  
  users.push(newUser);
  res.status(201).json(newUser);
});

// Update user
app.put('/api/users/:id', (req, res) => {
  const user = users.find(u => u.id === parseInt(req.params.id));
  
  if (!user) {
    return res.status(404).json({ error: 'User not found' });
  }
  
  user.name = req.body.name || user.name;
  user.email = req.body.email || user.email;
  
  res.json(user);
});

// Delete user
app.delete('/api/users/:id', (req, res) => {
  const index = users.findIndex(u => u.id === parseInt(req.params.id));
  
  if (index === -1) {
    return res.status(404).json({ error: 'User not found' });
  }
  
  users.splice(index, 1);
  res.status(204).send();
});

// Start server
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

### Testing the API

```bash
# Get all users
curl http://localhost:3000/api/users

# Get user by ID
curl http://localhost:3000/api/users/1

# Create user
curl -X POST http://localhost:3000/api/users \
  -H "Content-Type: application/json" \
  -d '{"name":"Charlie","email":"charlie@example.com"}'

# Update user
curl -X PUT http://localhost:3000/api/users/1 \
  -H "Content-Type: application/json" \
  -d '{"name":"Alice Updated"}'

# Delete user
curl -X DELETE http://localhost:3000/api/users/2
```

---

## 📚 Chapter Summary

### What You Learned

✅ Backend processes requests, manages data, and handles business logic  
✅ Client-server architecture follows request-response cycle  
✅ REST APIs use resources and HTTP methods for consistent design  
✅ HTTP methods (GET, POST, PUT, DELETE) define actions  
✅ HTTP status codes communicate results (2xx, 4xx, 5xx)  
✅ Express.js is a minimal Node.js framework for building servers  
✅ Routing maps URLs to handler functions  
✅ Middleware functions process requests before reaching routes  

### Key Takeaways

```javascript
// Basic Express server structure
const express = require('express');
const app = express();

// Middleware
app.use(express.json());

// Routes
app.get('/api/resource', handler);
app.post('/api/resource', handler);
app.put('/api/resource/:id', handler);
app.delete('/api/resource/:id', handler);

// Start server
app.listen(3000);
```

### Next Chapter Preview

In **Chapter 2**, you'll learn:
- Advanced routing techniques
- Custom middleware creation
- Proper folder structure
- Error handling
- Environment variables
- API versioning

---