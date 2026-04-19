# Chapter 2: Express Routing, Middleware & Project Structure

## 🎯 Learning Objectives

By the end of this chapter, you will:
- Master advanced routing techniques
- Work with route parameters and query strings effectively
- Create custom middleware for various purposes
- Implement robust error-handling middleware
- Organize Express projects with proper folder structure
- Use environment variables securely
- Implement API versioning strategies

---

## 2.1 Advanced Routing

### Express Router

As your application grows, putting all routes in one file becomes unmanageable. **Express Router** lets you create modular, mountable route handlers.

**Think of Router as a mini-application** that handles routes for a specific resource.

```javascript
const express = require('express');
const router = express.Router();

// Define routes on the router
router.get('/', (req, res) => {
  res.json({ message: 'Get all users' });
});

router.get('/:id', (req, res) => {
  res.json({ message: `Get user ${req.params.id}` });
});

router.post('/', (req, res) => {
  res.json({ message: 'Create user' });
});

// Export the router
module.exports = router;
```

**Mount the router in your main app:**

```javascript
const express = require('express');
const app = express();
const userRoutes = require('./routes/users');

// Mount at /api/users
app.use('/api/users', userRoutes);

// Now these routes work:
// GET  /api/users      → Get all users
// GET  /api/users/42   → Get user 42
// POST /api/users      → Create user
```

### Route Chaining

Handle multiple HTTP methods on the same path elegantly:

```javascript
const router = express.Router();

// Traditional approach (repetitive)
router.get('/products', getAllProducts);
router.post('/products', createProduct);

// Chained approach (cleaner)
router.route('/products')
  .get(getAllProducts)
  .post(createProduct);

router.route('/products/:id')
  .get(getProduct)
  .put(updateProduct)
  .delete(deleteProduct);
```

### Router-Level Middleware

Apply middleware to specific routers:

```javascript
const express = require('express');
const router = express.Router();

// Middleware that runs for all routes in this router
router.use((req, res, next) => {
  console.log('User router middleware');
  next();
});

// Middleware for specific route
router.get('/:id', validateUserId, getUser);

function validateUserId(req, res, next) {
  const id = parseInt(req.params.id);
  if (isNaN(id) || id <= 0) {
    return res.status(400).json({ error: 'Invalid user ID' });
  }
  next();
}

function getUser(req, res) {
  res.json({ id: req.params.id, name: 'John Doe' });
}

module.exports = router;
```

### Nested Routes

Create routes within routes for related resources:

```javascript
// routes/products.js
const express = require('express');
const router = express.Router();
const reviewRouter = require('./reviews');

// GET /api/products
router.get('/', (req, res) => {
  res.json({ products: [] });
});

// GET /api/products/:id
router.get('/:id', (req, res) => {
  res.json({ product: { id: req.params.id } });
});

// Nest review routes under products
// GET /api/products/:productId/reviews
router.use('/:productId/reviews', reviewRouter);

module.exports = router;
```

```javascript
// routes/reviews.js
const express = require('express');
// mergeParams allows access to parent route params
const router = express.Router({ mergeParams: true });

// GET /api/products/:productId/reviews
router.get('/', (req, res) => {
  res.json({ 
    productId: req.params.productId,
    reviews: [] 
  });
});

// POST /api/products/:productId/reviews
router.post('/', (req, res) => {
  res.json({ 
    productId: req.params.productId,
    review: req.body 
  });
});

module.exports = router;
```

---

## 2.2 Route Parameters & Query Strings

### Route Parameters (req.params)

Route parameters are **named segments** in the URL path:

```javascript
// Single parameter
app.get('/users/:id', (req, res) => {
  // URL: /users/42
  console.log(req.params.id); // '42' (always a string)
  res.json({ userId: req.params.id });
});

// Multiple parameters
app.get('/posts/:year/:month/:slug', (req, res) => {
  // URL: /posts/2024/01/express-guide
  const { year, month, slug } = req.params;
  // year: '2024', month: '01', slug: 'express-guide'
  res.json({ year, month, slug });
});

// Optional parameters (use ? at the end)
app.get('/books/:category/:id?', (req, res) => {
  // /books/fiction → { category: 'fiction', id: undefined }
  // /books/fiction/123 → { category: 'fiction', id: '123' }
  res.json(req.params);
});
```

### Query Strings (req.query)

Query strings come after `?` in the URL:

```javascript
app.get('/search', (req, res) => {
  // URL: /search?term=express&page=2&limit=10
  const { term, page, limit } = req.query;
  // term: 'express', page: '2', limit: '10' (all strings)
  
  res.json({
    searchTerm: term,
    page: parseInt(page) || 1,
    limit: parseInt(limit) || 10
  });
});
```

### Advanced Query String Handling

```javascript
app.get('/api/products', (req, res) => {
  // URL: /api/products?category=electronics&minPrice=100&maxPrice=500&sort=price&order=desc
  
  const {
    category,
    minPrice,
    maxPrice,
    sort = 'createdAt',  // Default value
    order = 'asc',       // Default value
    page = '1',
    limit = '10'
  } = req.query;

  // Convert to proper types
  const queryOptions = {
    category,
    priceRange: {
      min: minPrice ? parseFloat(minPrice) : 0,
      max: maxPrice ? parseFloat(maxPrice) : Infinity
    },
    sorting: {
      field: sort,
      direction: order === 'desc' ? -1 : 1
    },
    pagination: {
      page: Math.max(1, parseInt(page)),
      limit: Math.min(100, Math.max(1, parseInt(limit)))
    }
  };

  res.json(queryOptions);
});
```

### Array Query Parameters

```javascript
app.get('/api/filter', (req, res) => {
  // URL: /api/filter?tags=javascript&tags=nodejs&tags=express
  // OR: /api/filter?tags[]=javascript&tags[]=nodejs
  
  let tags = req.query.tags;
  
  // Ensure tags is always an array
  if (!tags) {
    tags = [];
  } else if (!Array.isArray(tags)) {
    tags = [tags];
  }
  
  res.json({ tags }); // ['javascript', 'nodejs', 'express']
});
```

### Request Body (req.body)

For POST, PUT, PATCH requests with JSON data:

```javascript
app.use(express.json()); // Required middleware

app.post('/api/users', (req, res) => {
  const { name, email, age } = req.body;
  
  // Validate
  if (!name || !email) {
    return res.status(400).json({ 
      error: 'Name and email are required' 
    });
  }
  
  res.status(201).json({
    message: 'User created',
    user: { name, email, age }
  });
});
```

### Complete Request Data Example

```javascript
app.put('/api/posts/:id', (req, res) => {
  // All request data sources:
  
  // 1. Route parameters (from URL path)
  const { id } = req.params;
  
  // 2. Query strings (from URL after ?)
  const { notify } = req.query;
  
  // 3. Request body (from JSON payload)
  const { title, content } = req.body;
  
  // 4. Headers (metadata)
  const authToken = req.headers.authorization;
  const contentType = req.headers['content-type'];
  
  res.json({
    id,
    notify: notify === 'true',
    title,
    content,
    authenticated: !!authToken
  });
});
```

---

## 2.3 Custom Middleware

### Middleware Structure Review

```javascript
const myMiddleware = (req, res, next) => {
  // 1. Do something with req or res
  // 2. Either call next() OR send a response
  // 3. Never do both!
  next();
};
```

### Practical Custom Middleware Examples

#### 1. Request Logger

```javascript
const requestLogger = (req, res, next) => {
  const start = Date.now();
  
  // Log when response finishes
  res.on('finish', () => {
    const duration = Date.now() - start;
    const log = {
      method: req.method,
      url: req.originalUrl,
      status: res.statusCode,
      duration: `${duration}ms`,
      timestamp: new Date().toISOString()
    };
    console.log(JSON.stringify(log));
  });
  
  next();
};

app.use(requestLogger);
```

#### 2. Request Validator

```javascript
const validateContentType = (req, res, next) => {
  // Only check for requests with body
  if (['POST', 'PUT', 'PATCH'].includes(req.method)) {
    const contentType = req.headers['content-type'];
    
    if (!contentType || !contentType.includes('application/json')) {
      return res.status(415).json({
        error: 'Unsupported Media Type',
        message: 'Content-Type must be application/json'
      });
    }
  }
  next();
};

app.use(validateContentType);
```

#### 3. Rate Limiter (Simple Implementation)

```javascript
const requestCounts = new Map();

const rateLimiter = (maxRequests = 100, windowMs = 60000) => {
  return (req, res, next) => {
    const clientIP = req.ip;
    const now = Date.now();
    
    // Get or create client record
    let clientData = requestCounts.get(clientIP);
    
    if (!clientData || now - clientData.windowStart > windowMs) {
      // New window
      clientData = { count: 1, windowStart: now };
    } else {
      clientData.count++;
    }
    
    requestCounts.set(clientIP, clientData);
    
    // Set rate limit headers
    res.set({
      'X-RateLimit-Limit': maxRequests,
      'X-RateLimit-Remaining': Math.max(0, maxRequests - clientData.count),
      'X-RateLimit-Reset': new Date(clientData.windowStart + windowMs).toISOString()
    });
    
    if (clientData.count > maxRequests) {
      return res.status(429).json({
        error: 'Too Many Requests',
        message: `Rate limit exceeded. Try again in ${Math.ceil((clientData.windowStart + windowMs - now) / 1000)} seconds.`
      });
    }
    
    next();
  };
};

// Apply rate limiting: 100 requests per minute
app.use(rateLimiter(100, 60000));
```

#### 4. Authentication Middleware

```javascript
const authenticate = (req, res, next) => {
  const authHeader = req.headers.authorization;
  
  if (!authHeader) {
    return res.status(401).json({
      error: 'Unauthorized',
      message: 'No authorization header provided'
    });
  }
  
  // Expected format: "Bearer <token>"
  const parts = authHeader.split(' ');
  
  if (parts.length !== 2 || parts[0] !== 'Bearer') {
    return res.status(401).json({
      error: 'Unauthorized',
      message: 'Invalid authorization format. Use: Bearer <token>'
    });
  }
  
  const token = parts[1];
  
  try {
    // In real app, verify JWT token
    // const decoded = jwt.verify(token, process.env.JWT_SECRET);
    
    // Simulated user data
    req.user = {
      id: 1,
      name: 'John Doe',
      role: 'admin'
    };
    
    next();
  } catch (error) {
    return res.status(403).json({
      error: 'Forbidden',
      message: 'Invalid or expired token'
    });
  }
};

// Use on protected routes
app.get('/api/profile', authenticate, (req, res) => {
  res.json({ user: req.user });
});
```

#### 5. Role-Based Authorization

```javascript
const authorize = (...allowedRoles) => {
  return (req, res, next) => {
    // Must be used after authenticate middleware
    if (!req.user) {
      return res.status(401).json({
        error: 'Unauthorized',
        message: 'Authentication required'
      });
    }
    
    if (!allowedRoles.includes(req.user.role)) {
      return res.status(403).json({
        error: 'Forbidden',
        message: `Access denied. Required roles: ${allowedRoles.join(', ')}`
      });
    }
    
    next();
  };
};

// Usage: Only admins can delete
app.delete('/api/users/:id', 
  authenticate, 
  authorize('admin'), 
  deleteUser
);

// Admins and moderators can update
app.put('/api/posts/:id', 
  authenticate, 
  authorize('admin', 'moderator'), 
  updatePost
);
```

#### 6. Request ID Generator

```javascript
const { v4: uuidv4 } = require('uuid');

const addRequestId = (req, res, next) => {
  // Check if client sent an ID, otherwise generate one
  req.requestId = req.headers['x-request-id'] || uuidv4();
  
  // Add to response headers
  res.set('X-Request-Id', req.requestId);
  
  next();
};

app.use(addRequestId);

// Now every request has a unique ID for tracking
app.get('/api/data', (req, res) => {
  console.log(`Processing request ${req.requestId}`);
  res.json({ requestId: req.requestId, data: [] });
});
```

---

## 2.4 Error-Handling Middleware

### Error Middleware Signature

Error-handling middleware has **4 parameters** (not 3):

```javascript
const errorHandler = (err, req, res, next) => {
  // Handle the error
  res.status(500).json({ error: err.message });
};

// MUST be defined AFTER all routes
app.use(errorHandler);
```

### Creating Custom Error Classes

```javascript
// utils/AppError.js
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.status = `${statusCode}`.startsWith('4') ? 'fail' : 'error';
    this.isOperational = true;

    Error.captureStackTrace(this, this.constructor);
  }
}

module.exports = AppError;
```

**Specialized Error Classes:**

```javascript
// errors/index.js
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
    Error.captureStackTrace(this, this.constructor);
  }
}

class NotFoundError extends AppError {
  constructor(resource = 'Resource') {
    super(`${resource} not found`, 404);
  }
}

class ValidationError extends AppError {
  constructor(message) {
    super(message, 400);
  }
}

class UnauthorizedError extends AppError {
  constructor(message = 'Authentication required') {
    super(message, 401);
  }
}

class ForbiddenError extends AppError {
  constructor(message = 'Access denied') {
    super(message, 403);
  }
}

class ConflictError extends AppError {
  constructor(message) {
    super(message, 409);
  }
}

module.exports = {
  AppError,
  NotFoundError,
  ValidationError,
  UnauthorizedError,
  ForbiddenError,
  ConflictError
};
```

### Throwing Errors in Routes

```javascript
const { NotFoundError, ValidationError } = require('./errors');

app.get('/api/users/:id', (req, res, next) => {
  try {
    const userId = parseInt(req.params.id);
    
    if (isNaN(userId)) {
      throw new ValidationError('User ID must be a number');
    }
    
    const user = users.find(u => u.id === userId);
    
    if (!user) {
      throw new NotFoundError('User');
    }
    
    res.json(user);
  } catch (error) {
    next(error); // Pass error to error handler
  }
});
```

### Async Error Handling

Express doesn't catch errors in async functions automatically:

```javascript
// ❌ Problem: Error not caught
app.get('/api/data', async (req, res) => {
  const data = await fetchData(); // If this throws, Express doesn't catch it
  res.json(data);
});

// ✅ Solution 1: Try-catch
app.get('/api/data', async (req, res, next) => {
  try {
    const data = await fetchData();
    res.json(data);
  } catch (error) {
    next(error);
  }
});

// ✅ Solution 2: Async wrapper function
const asyncHandler = (fn) => {
  return (req, res, next) => {
    Promise.resolve(fn(req, res, next)).catch(next);
  };
};

app.get('/api/data', asyncHandler(async (req, res) => {
  const data = await fetchData();
  res.json(data);
}));
```

### Complete Error Handler

```javascript
// middleware/errorHandler.js
const errorHandler = (err, req, res, next) => {
  // Log error for debugging
  console.error('Error:', {
    message: err.message,
    stack: err.stack,
    requestId: req.requestId,
    path: req.path,
    method: req.method
  });

  // Default error values
  let statusCode = err.statusCode || 500;
  let message = err.message || 'Internal Server Error';
  let errors = null;

  // Handle specific error types
  
  // Mongoose validation error
  if (err.name === 'ValidationError') {
    statusCode = 400;
    message = 'Validation Error';
    errors = Object.values(err.errors).map(e => e.message);
  }
  
  // Mongoose duplicate key error
  if (err.code === 11000) {
    statusCode = 409;
    const field = Object.keys(err.keyValue)[0];
    message = `${field} already exists`;
  }
  
  // Mongoose cast error (invalid ObjectId)
  if (err.name === 'CastError') {
    statusCode = 400;
    message = `Invalid ${err.path}: ${err.value}`;
  }
  
  // JWT errors
  if (err.name === 'JsonWebTokenError') {
    statusCode = 401;
    message = 'Invalid token';
  }
  
  if (err.name === 'TokenExpiredError') {
    statusCode = 401;
    message = 'Token expired';
  }

  // Send response
  const response = {
    success: false,
    error: {
      message,
      ...(errors && { errors }),
      ...(process.env.NODE_ENV === 'development' && { 
        stack: err.stack 
      })
    }
  };

  res.status(statusCode).json(response);
};

module.exports = errorHandler;
```

### 404 Handler (Not Found)

```javascript
// This must be BEFORE the error handler
const notFoundHandler = (req, res, next) => {
  res.status(404).json({
    success: false,
    error: {
      message: `Route ${req.method} ${req.originalUrl} not found`
    }
  });
};

// Order matters!
app.use('/api/users', userRoutes);
app.use('/api/products', productRoutes);
app.use(notFoundHandler);  // 404 handler
app.use(errorHandler);     // Error handler (must be last)
```

---

## 2.5 Express Folder Structure

### Simple Structure (Small Projects)

```
my-app/
├── node_modules/
├── server.js           # Entry point
├── routes/
│   ├── users.js
│   └── products.js
├── middleware/
│   └── auth.js
├── .env
├── .gitignore
└── package.json
```

### Recommended Structure (Medium to Large Projects)

```
my-app/
├── src/
│   ├── config/
│   │   ├── db.js           # Database connection
│   │   └── config.js       # App configuration
│   │
│   ├── controllers/
│   │   ├── userController.js
│   │   └── productController.js
│   │
│   ├── middleware/
│   │   ├── auth.js
│   │   ├── errorHandler.js
│   │   ├── validate.js
│   │   └── rateLimiter.js
│   │
│   ├── models/
│   │   ├── User.js
│   │   └── Product.js
│   │
│   ├── routes/
│   │   ├── index.js        # Route aggregator
│   │   ├── userRoutes.js
│   │   └── productRoutes.js
│   │
│   ├── services/
│   │   ├── userService.js
│   │   └── emailService.js
│   │
│   ├── utils/
│   │   ├── AppError.js
│   │   ├── asyncHandler.js
│   │   └── helpers.js
│   │
│   ├── validators/
│   │   ├── userValidator.js
│   │   └── productValidator.js
│   │
│   └── app.js              # Express app setup
│
├── tests/
│   ├── unit/
│   └── integration/
│
├── logs/
├── .env
├── .env.example
├── .gitignore
├── package.json
└── server.js               # Entry point
```

### Implementation Example

#### Entry Point (server.js)

```javascript
// server.js
const app = require('./src/app');
const config = require('./src/config/config');

const PORT = config.port;

const server = app.listen(PORT, () => {
  console.log(`Server running in ${config.env} mode on port ${PORT}`);
});

// Handle unhandled promise rejections
process.on('unhandledRejection', (err) => {
  console.error('Unhandled Rejection:', err.message);
  server.close(() => process.exit(1));
});

// Handle uncaught exceptions
process.on('uncaughtException', (err) => {
  console.error('Uncaught Exception:', err.message);
  process.exit(1);
});
```

#### App Setup (src/app.js)

```javascript
// src/app.js
const express = require('express');
const cors = require('cors');
const helmet = require('helmet');
const morgan = require('morgan');

const routes = require('./routes');
const errorHandler = require('./middleware/errorHandler');
const notFoundHandler = require('./middleware/notFoundHandler');

const app = express();

// Security middleware
app.use(helmet());
app.use(cors());

// Request parsing
app.use(express.json({ limit: '10kb' }));
app.use(express.urlencoded({ extended: true }));

// Logging
if (process.env.NODE_ENV === 'development') {
  app.use(morgan('dev'));
}

// API routes
app.use('/api/v1', routes);

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'OK', timestamp: new Date().toISOString() });
});

// Error handling
app.use(notFoundHandler);
app.use(errorHandler);

module.exports = app;
```

#### Route Aggregator (src/routes/index.js)

```javascript
// src/routes/index.js
const express = require('express');
const router = express.Router();

const userRoutes = require('./userRoutes');
const productRoutes = require('./productRoutes');
const authRoutes = require('./authRoutes');

router.use('/auth', authRoutes);
router.use('/users', userRoutes);
router.use('/products', productRoutes);

module.exports = router;
```

#### Route File (src/routes/userRoutes.js)

```javascript
// src/routes/userRoutes.js
const express = require('express');
const router = express.Router();
const userController = require('../controllers/userController');
const { authenticate, authorize } = require('../middleware/auth');
const { validateUser, validateUserUpdate } = require('../validators/userValidator');

router.route('/')
  .get(authenticate, authorize('admin'), userController.getAllUsers)
  .post(validateUser, userController.createUser);

router.route('/:id')
  .get(userController.getUser)
  .put(authenticate, validateUserUpdate, userController.updateUser)
  .delete(authenticate, authorize('admin'), userController.deleteUser);

module.exports = router;
```

#### Controller (src/controllers/userController.js)

```javascript
// src/controllers/userController.js
const userService = require('../services/userService');
const asyncHandler = require('../utils/asyncHandler');
const { NotFoundError } = require('../utils/AppError');

exports.getAllUsers = asyncHandler(async (req, res) => {
  const { page, limit, sort } = req.query;
  const users = await userService.findAll({ page, limit, sort });
  
  res.json({
    success: true,
    count: users.length,
    data: users
  });
});

exports.getUser = asyncHandler(async (req, res) => {
  const user = await userService.findById(req.params.id);
  
  if (!user) {
    throw new NotFoundError('User');
  }
  
  res.json({
    success: true,
    data: user
  });
});

exports.createUser = asyncHandler(async (req, res) => {
  const user = await userService.create(req.body);
  
  res.status(201).json({
    success: true,
    data: user
  });
});

exports.updateUser = asyncHandler(async (req, res) => {
  const user = await userService.update(req.params.id, req.body);
  
  if (!user) {
    throw new NotFoundError('User');
  }
  
  res.json({
    success: true,
    data: user
  });
});

exports.deleteUser = asyncHandler(async (req, res) => {
  const user = await userService.delete(req.params.id);
  
  if (!user) {
    throw new NotFoundError('User');
  }
  
  res.status(204).send();
});
```

#### Service Layer (src/services/userService.js)

```javascript
// src/services/userService.js
// Simulated database operations
let users = [
  { id: 1, name: 'Alice', email: 'alice@example.com', role: 'user' },
  { id: 2, name: 'Bob', email: 'bob@example.com', role: 'admin' }
];

let nextId = 3;

exports.findAll = async ({ page = 1, limit = 10, sort = 'id' }) => {
  // In real app: return await User.find().sort(sort).skip().limit();
  return users;
};

exports.findById = async (id) => {
  // In real app: return await User.findById(id);
  return users.find(u => u.id === parseInt(id));
};

exports.findByEmail = async (email) => {
  return users.find(u => u.email === email);
};

exports.create = async (userData) => {
  const user = {
    id: nextId++,
    ...userData,
    role: 'user',
    createdAt: new Date()
  };
  users.push(user);
  return user;
};

exports.update = async (id, userData) => {
  const index = users.findIndex(u => u.id === parseInt(id));
  if (index === -1) return null;
  
  users[index] = { ...users[index], ...userData };
  return users[index];
};

exports.delete = async (id) => {
  const index = users.findIndex(u => u.id === parseInt(id));
  if (index === -1) return null;
  
  const deleted = users.splice(index, 1);
  return deleted[0];
};
```

---

## 2.6 Environment Variables (dotenv)

### Why Environment Variables?

Environment variables store:
- Configuration that changes between environments
- Sensitive data (API keys, passwords)
- Feature flags

**Never hardcode secrets in your code!**

### Setting Up dotenv

```bash
npm install dotenv
```

#### .env File

```env
# .env
NODE_ENV=development
PORT=3000

# Database
DB_HOST=localhost
DB_PORT=27017
DB_NAME=myapp
MONGODB_URI=mongodb://localhost:27017/myapp

# JWT
JWT_SECRET=your-super-secret-key-change-in-production
JWT_EXPIRES_IN=7d

# Email
SMTP_HOST=smtp.mailtrap.io
SMTP_PORT=2525
SMTP_USER=your-smtp-user
SMTP_PASS=your-smtp-password

# External APIs
STRIPE_SECRET_KEY=sk_test_xxxxx
AWS_ACCESS_KEY=AKIAIOSFODNN7EXAMPLE
AWS_SECRET_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

#### .env.example File

```env
# .env.example (commit this to git)
NODE_ENV=development
PORT=3000

# Database
DB_HOST=localhost
DB_PORT=27017
DB_NAME=myapp
MONGODB_URI=

# JWT
JWT_SECRET=
JWT_EXPIRES_IN=7d

# Email
SMTP_HOST=
SMTP_PORT=
SMTP_USER=
SMTP_PASS=

# External APIs
STRIPE_SECRET_KEY=
AWS_ACCESS_KEY=
AWS_SECRET_KEY=
```

#### .gitignore

```gitignore
# .gitignore
node_modules/
.env
logs/
*.log
```

### Loading Environment Variables

```javascript
// Load at the very top of entry point
require('dotenv').config();

// Or specify path
require('dotenv').config({ path: './config/.env' });
```

### Configuration Module

```javascript
// src/config/config.js
require('dotenv').config();

const config = {
  env: process.env.NODE_ENV || 'development',
  port: parseInt(process.env.PORT, 10) || 3000,
  
  db: {
    uri: process.env.MONGODB_URI,
    host: process.env.DB_HOST || 'localhost',
    port: parseInt(process.env.DB_PORT, 10) || 27017,
    name: process.env.DB_NAME || 'myapp'
  },
  
  jwt: {
    secret: process.env.JWT_SECRET,
    expiresIn: process.env.JWT_EXPIRES_IN || '7d'
  },
  
  email: {
    host: process.env.SMTP_HOST,
    port: parseInt(process.env.SMTP_PORT, 10),
    user: process.env.SMTP_USER,
    pass: process.env.SMTP_PASS
  }
};

// Validate required variables
const requiredEnvVars = ['JWT_SECRET', 'MONGODB_URI'];

for (const envVar of requiredEnvVars) {
  if (!process.env[envVar]) {
    throw new Error(`Missing required environment variable: ${envVar}`);
  }
}

module.exports = config;
```

### Using Configuration

```javascript
const config = require('./config/config');

// Database connection
mongoose.connect(config.db.uri);

// JWT signing
jwt.sign(payload, config.jwt.secret, { expiresIn: config.jwt.expiresIn });

// Server startup
app.listen(config.port);
```

### Different Environments

```bash
# Development
NODE_ENV=development node server.js

# Production
NODE_ENV=production node server.js

# Using different .env files
# .env.development
# .env.production
# .env.test
```

```javascript
// Load environment-specific config
const envFile = `.env.${process.env.NODE_ENV || 'development'}`;
require('dotenv').config({ path: envFile });
```

---

## 2.7 API Versioning

### Why Version APIs?

- Maintain backward compatibility
- Allow gradual migration
- Support multiple client versions
- Deprecate old features safely

### Versioning Strategies

#### 1. URL Path Versioning (Most Common)

```javascript
// routes/index.js
const express = require('express');
const router = express.Router();

const v1Routes = require('./v1');
const v2Routes = require('./v2');

router.use('/v1', v1Routes);
router.use('/v2', v2Routes);

module.exports = router;
```

```javascript
// In app.js
app.use('/api', routes);

// Results in:
// /api/v1/users
// /api/v2/users
```

**Folder structure:**

```
src/
├── routes/
│   ├── index.js
│   ├── v1/
│   │   ├── index.js
│   │   ├── userRoutes.js
│   │   └── productRoutes.js
│   └── v2/
│       ├── index.js
│       ├── userRoutes.js
│       └── productRoutes.js
```

```javascript
// routes/v1/index.js
const express = require('express');
const router = express.Router();

const userRoutes = require('./userRoutes');
const productRoutes = require('./productRoutes');

router.use('/users', userRoutes);
router.use('/products', productRoutes);

module.exports = router;
```

```javascript
// routes/v1/userRoutes.js
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
  res.json({
    version: 'v1',
    users: [
      { id: 1, name: 'John' } // v1 format
    ]
  });
});

module.exports = router;
```

```javascript
// routes/v2/userRoutes.js
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
  res.json({
    version: 'v2',
    data: {
      users: [
        { id: 1, firstName: 'John', lastName: 'Doe' } // v2 format
      ],
      pagination: { page: 1, limit: 10, total: 1 }
    }
  });
});

module.exports = router;
```

#### 2. Header Versioning

```javascript
// middleware/apiVersion.js
const versionMiddleware = (req, res, next) => {
  // Accept-Version: v1 or Accept-Version: v2
  const version = req.headers['accept-version'] || 'v1';
  req.apiVersion = version;
  next();
};

app.use(versionMiddleware);

// In routes
app.get('/api/users', (req, res) => {
  if (req.apiVersion === 'v2') {
    return res.json({ data: { users: [] } });
  }
  res.json({ users: [] }); // v1 default
});
```

#### 3. Query Parameter Versioning

```javascript
// /api/users?version=2
app.get('/api/users', (req, res) => {
  const version = req.query.version || '1';
  
  if (version === '2') {
    return res.json({ data: { users: [] } });
  }
  res.json({ users: [] });
});
```

### Version Deprecation

```javascript
// middleware/deprecation.js
const deprecationWarning = (deprecatedVersion, sunsetDate) => {
  return (req, res, next) => {
    res.set({
      'Deprecation': 'true',
      'Sunset': sunsetDate,
      'Link': `</api/v2${req.path}>; rel="successor-version"`
    });
    
    console.warn(`Deprecated API ${deprecatedVersion} accessed: ${req.path}`);
    
    next();
  };
};

// Apply to v1 routes
const v1Router = require('./v1');
app.use('/api/v1', deprecationWarning('v1', '2025-01-01'), v1Router);
```

---

## 2.8 Complete Project Example

Let's put everything together:

### Project Structure

```
express-api/
├── src/
│   ├── config/
│   │   └── config.js
│   ├── controllers/
│   │   └── taskController.js
│   ├── middleware/
│   │   ├── auth.js
│   │   ├── errorHandler.js
│   │   ├── notFound.js
│   │   └── validate.js
│   ├── routes/
│   │   ├── index.js
│   │   └── taskRoutes.js
│   ├── services/
│   │   └── taskService.js
│   ├── utils/
│   │   ├── AppError.js
│   │   └── asyncHandler.js
│   └── app.js
├── .env
├── .env.example
├── .gitignore
├── package.json
└── server.js
```

### Implementation Files

**server.js**
```javascript
require('dotenv').config();
const app = require('./src/app');
const config = require('./src/config/config');

const server = app.listen(config.port, () => {
  console.log(`🚀 Server running in ${config.env} mode on port ${config.port}`);
});

process.on('unhandledRejection', (err) => {
  console.error('Unhandled Rejection:', err);
  server.close(() => process.exit(1));
});
```

**src/config/config.js**
```javascript
module.exports = {
  env: process.env.NODE_ENV || 'development',
  port: process.env.PORT || 3000,
  jwtSecret: process.env.JWT_SECRET || 'dev-secret'
};
```

**src/app.js**
```javascript
const express = require('express');
const cors = require('cors');
const routes = require('./routes');
const errorHandler = require('./middleware/errorHandler');
const notFound = require('./middleware/notFound');

const app = express();

app.use(cors());
app.use(express.json());

// Request logging
app.use((req, res, next) => {
  console.log(`${new Date().toISOString()} - ${req.method} ${req.url}`);
  next();
});

// Routes
app.use('/api/v1', routes);

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'OK' });
});

// Error handling
app.use(notFound);
app.use(errorHandler);

module.exports = app;
```

**src/routes/index.js**
```javascript
const express = require('express');
const router = express.Router();
const taskRoutes = require('./taskRoutes');

router.use('/tasks', taskRoutes);

module.exports = router;
```

**src/routes/taskRoutes.js**
```javascript
const express = require('express');
const router = express.Router();
const taskController = require('../controllers/taskController');
const { validateTask } = require('../middleware/validate');

router.route('/')
  .get(taskController.getAllTasks)
  .post(validateTask, taskController.createTask);

router.route('/:id')
  .get(taskController.getTask)
  .put(validateTask, taskController.updateTask)
  .delete(taskController.deleteTask);

module.exports = router;
```

**src/controllers/taskController.js**
```javascript
const taskService = require('../services/taskService');
const asyncHandler = require('../utils/asyncHandler');
const { NotFoundError } = require('../utils/AppError');

exports.getAllTasks = asyncHandler(async (req, res) => {
  const { status, sort } = req.query;
  const tasks = await taskService.findAll({ status, sort });
  
  res.json({
    success: true,
    count: tasks.length,
    data: tasks
  });
});

exports.getTask = asyncHandler(async (req, res) => {
  const task = await taskService.findById(req.params.id);
  
  if (!task) {
    throw new NotFoundError('Task');
  }
  
  res.json({ success: true, data: task });
});

exports.createTask = asyncHandler(async (req, res) => {
  const task = await taskService.create(req.body);
  res.status(201).json({ success: true, data: task });
});

exports.updateTask = asyncHandler(async (req, res) => {
  const task = await taskService.update(req.params.id, req.body);
  
  if (!task) {
    throw new NotFoundError('Task');
  }
  
  res.json({ success: true, data: task });
});

exports.deleteTask = asyncHandler(async (req, res) => {
  const task = await taskService.delete(req.params.id);
  
  if (!task) {
    throw new NotFoundError('Task');
  }
  
  res.status(204).send();
});
```

**src/services/taskService.js**
```javascript
let tasks = [];
let nextId = 1;

exports.findAll = async ({ status, sort }) => {
  let result = [...tasks];
  
  if (status) {
    result = result.filter(t => t.status === status);
  }
  
  if (sort === 'date') {
    result.sort((a, b) => new Date(b.createdAt) - new Date(a.createdAt));
  }
  
  return result;
};

exports.findById = async (id) => {
  return tasks.find(t => t.id === parseInt(id));
};

exports.create = async (data) => {
  const task = {
    id: nextId++,
    title: data.title,
    description: data.description || '',
    status: 'pending',
    createdAt: new Date()
  };
  tasks.push(task);
  return task;
};

exports.update = async (id, data) => {
  const index = tasks.findIndex(t => t.id === parseInt(id));
  if (index === -1) return null;
  
  tasks[index] = { ...tasks[index], ...data, updatedAt: new Date() };
  return tasks[index];
};

exports.delete = async (id) => {
  const index = tasks.findIndex(t => t.id === parseInt(id));
  if (index === -1) return null;
  
  return tasks.splice(index, 1)[0];
};
```

**src/middleware/validate.js**
```javascript
const { ValidationError } = require('../utils/AppError');

exports.validateTask = (req, res, next) => {
  const { title } = req.body;
  
  if (!title || title.trim() === '') {
    throw new ValidationError('Title is required');
  }
  
  if (title.length > 100) {
    throw new ValidationError('Title must be less than 100 characters');
  }
  
  next();
};
```

**src/middleware/errorHandler.js**
```javascript
module.exports = (err, req, res, next) => {
  const statusCode = err.statusCode || 500;
  
  res.status(statusCode).json({
    success: false,
    error: {
      message: err.message || 'Internal Server Error',
      ...(process.env.NODE_ENV === 'development' && { stack: err.stack })
    }
  });
};
```

**src/middleware/notFound.js**
```javascript
module.exports = (req, res) => {
  res.status(404).json({
    success: false,
    error: { message: `Route ${req.method} ${req.originalUrl} not found` }
  });
};
```

**src/utils/AppError.js**
```javascript
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
  }
}

class NotFoundError extends AppError {
  constructor(resource = 'Resource') {
    super(`${resource} not found`, 404);
  }
}

class ValidationError extends AppError {
  constructor(message) {
    super(message, 400);
  }
}

module.exports = { AppError, NotFoundError, ValidationError };
```

**src/utils/asyncHandler.js**
```javascript
module.exports = (fn) => (req, res, next) => {
  Promise.resolve(fn(req, res, next)).catch(next);
};
```

---

## 📚 Chapter Summary

### What You Learned

✅ Express Router for modular route organization  
✅ Route chaining and nested routes  
✅ Route parameters and query string handling  
✅ Creating custom middleware for various purposes  
✅ Error-handling middleware with custom error classes  
✅ Async error handling with wrapper functions  
✅ Professional folder structure for Express apps  
✅ Environment variables with dotenv  
✅ API versioning strategies  

### Key Patterns

```javascript
// Router module
const router = express.Router();
router.route('/').get(handler).post(handler);
module.exports = router;

// Custom middleware
const middleware = (req, res, next) => {
  // do something
  next();
};

// Async handler wrapper
const asyncHandler = (fn) => (req, res, next) => 
  Promise.resolve(fn(req, res, next)).catch(next);

// Error middleware (4 params)
const errorHandler = (err, req, res, next) => {
  res.status(err.statusCode || 500).json({ error: err.message });
};
```

### Next Chapter Preview

In **Chapter 3**, you'll learn:
- MongoDB fundamentals and architecture
- NoSQL vs SQL databases
- Document and collection concepts
- MongoDB Compass GUI
- Schema design principles
- Relationships: embedding vs referencing
- Introduction to Mongoose ODM

---