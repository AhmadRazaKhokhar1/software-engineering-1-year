# Chapter 5: Authentication, Authorization & Security

## 🎯 Learning Objectives

By the end of this chapter, you will:
- Understand the difference between authentication and authorization
- Implement JWT-based authentication system
- Hash passwords securely using bcrypt
- Create protected routes with middleware
- Implement role-based access control (RBAC)
- Apply security best practices
- Set up rate limiting and CORS

---

## 5.1 Authentication vs Authorization

### Understanding the Difference

**Authentication** answers: **"Who are you?"**
- Verifying user identity
- Login process (email/password, OAuth, etc.)
- Results in a token or session

**Authorization** answers: **"What can you do?"**
- Verifying user permissions
- Access control based on roles
- Determines allowed actions

### Visual Representation

```
┌─────────────────────────────────────────────────────────────┐
│                        USER REQUEST                          │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│              AUTHENTICATION (Who are you?)                   │
│                                                              │
│  • Check if token exists                                     │
│  • Verify token is valid                                     │
│  • Identify the user                                         │
│                                                              │
│  ❌ Failed → 401 Unauthorized                                │
└─────────────────────────┬───────────────────────────────────┘
                          │ ✅ Passed
                          ▼
┌─────────────────────────────────────────────────────────────┐
│              AUTHORIZATION (What can you do?)                │
│                                                              │
│  • Check user's role/permissions                             │
│  • Verify access to resource                                 │
│  • Validate ownership if needed                              │
│                                                              │
│  ❌ Failed → 403 Forbidden                                   │
└─────────────────────────┬───────────────────────────────────┘
                          │ ✅ Passed
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                    ACCESS GRANTED                            │
│                  Execute the request                         │
└─────────────────────────────────────────────────────────────┘
```

### Real-World Example

```
Scenario: Accessing Admin Dashboard

1. User sends request with JWT token
   → Authentication: "This is John (user ID: 123)"

2. Check John's role
   → Authorization: "John is a regular user, not admin"

3. Result
   → 403 Forbidden: "You don't have permission to access this"
```

### HTTP Status Codes

| Code | Name | When to Use |
|------|------|-------------|
| **401** | Unauthorized | No token, invalid token, expired token |
| **403** | Forbidden | Valid user but lacks permission |

---

## 5.2 JWT-Based Authentication

### What is JWT?

**JWT (JSON Web Token)** is a compact, self-contained token for securely transmitting information.

### JWT Structure

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjEyMyIsIm5hbWUiOiJKb2huIiwiaWF0IjoxNjE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
│                                      │                                                  │
└──────────── HEADER ──────────────────┴────────────── PAYLOAD ───────────────────────────┴───── SIGNATURE ─────┘
```

**Header:**
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

**Payload:**
```json
{
  "id": "123",
  "name": "John",
  "role": "user",
  "iat": 1616239022,
  "exp": 1616842822
}
```

**Signature:**
```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

### JWT Flow

```
┌──────────┐                                    ┌──────────┐
│  Client  │                                    │  Server  │
└────┬─────┘                                    └────┬─────┘
     │                                               │
     │  1. POST /login {email, password}             │
     │──────────────────────────────────────────────>│
     │                                               │
     │                        2. Validate credentials│
     │                        3. Generate JWT token  │
     │                                               │
     │  4. Return { token: "eyJ..." }                │
     │<──────────────────────────────────────────────│
     │                                               │
     │  5. Store token (localStorage/cookie)         │
     │                                               │
     │  6. GET /api/profile                          │
     │     Authorization: Bearer eyJ...              │
     │──────────────────────────────────────────────>│
     │                                               │
     │                        7. Verify JWT          │
     │                        8. Extract user info   │
     │                                               │
     │  9. Return { user: {...} }                    │
     │<──────────────────────────────────────────────│
     │                                               │
```

### Installing Dependencies

```bash
npm install jsonwebtoken bcryptjs
```

### Environment Variables

```env
# .env
JWT_SECRET=your-super-secret-key-at-least-32-characters-long
JWT_EXPIRES_IN=7d
JWT_COOKIE_EXPIRES_IN=7
```

### JWT Utility Functions

```javascript
// src/utils/jwt.js
const jwt = require('jsonwebtoken');

// Generate JWT token
exports.generateToken = (userId) => {
  return jwt.sign(
    { id: userId },
    process.env.JWT_SECRET,
    { expiresIn: process.env.JWT_EXPIRES_IN }
  );
};

// Verify JWT token
exports.verifyToken = (token) => {
  return jwt.verify(token, process.env.JWT_SECRET);
};

// Generate token and send response
exports.sendTokenResponse = (user, statusCode, res) => {
  const token = this.generateToken(user._id);
  
  // Cookie options
  const cookieOptions = {
    expires: new Date(
      Date.now() + process.env.JWT_COOKIE_EXPIRES_IN * 24 * 60 * 60 * 1000
    ),
    httpOnly: true,  // Cannot be accessed by JavaScript
    secure: process.env.NODE_ENV === 'production',  // HTTPS only in production
    sameSite: 'strict'
  };
  
  // Remove password from output
  user.password = undefined;
  
  res
    .status(statusCode)
    .cookie('token', token, cookieOptions)
    .json({
      success: true,
      token,
      data: { user }
    });
};
```

---

## 5.3 Password Hashing

### Why Hash Passwords?

**Never store plain-text passwords!**

If database is compromised:
- ❌ Plain text: Attacker sees all passwords
- ✅ Hashed: Attacker sees only hashes (can't reverse)

### How Bcrypt Works

```
Password: "mypassword123"
    │
    ▼
┌─────────────────────────────────────┐
│           BCRYPT HASH               │
│                                     │
│  1. Generate random salt            │
│  2. Combine password + salt         │
│  3. Apply hashing algorithm         │
│  4. Repeat (cost factor times)      │
│                                     │
└─────────────────────────────────────┘
    │
    ▼
Hash: "$2a$12$LQv3c1yqBWVHxkd0LHAkCOYz6TtxMQJqhN8/X4.GQpIkXg0Whw5Gq"
       │    │  │                                                    │
       │    │  └──────────────── Hash ──────────────────────────────┘
       │    └── Cost Factor (12 rounds)
       └── Algorithm version
```

### User Model with Password Hashing

```javascript
// src/models/User.js
const mongoose = require('mongoose');
const bcrypt = require('bcryptjs');

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Name is required'],
    trim: true,
    minlength: [2, 'Name must be at least 2 characters'],
    maxlength: [50, 'Name cannot exceed 50 characters']
  },
  
  email: {
    type: String,
    required: [true, 'Email is required'],
    unique: true,
    lowercase: true,
    trim: true,
    match: [/^\S+@\S+\.\S+$/, 'Please enter a valid email']
  },
  
  password: {
    type: String,
    required: [true, 'Password is required'],
    minlength: [8, 'Password must be at least 8 characters'],
    select: false  // Never return password in queries
  },
  
  role: {
    type: String,
    enum: ['user', 'admin', 'moderator'],
    default: 'user'
  },
  
  isActive: {
    type: Boolean,
    default: true
  },
  
  passwordChangedAt: Date,
  
  passwordResetToken: String,
  
  passwordResetExpires: Date
  
}, {
  timestamps: true
});

// Pre-save middleware: Hash password
userSchema.pre('save', async function(next) {
  // Only hash if password is modified
  if (!this.isModified('password')) return next();
  
  // Hash password with cost factor 12
  this.password = await bcrypt.hash(this.password, 12);
  
  // Set password changed timestamp (except for new users)
  if (!this.isNew) {
    this.passwordChangedAt = Date.now() - 1000;
  }
  
  next();
});

// Instance method: Compare passwords
userSchema.methods.comparePassword = async function(candidatePassword) {
  return await bcrypt.compare(candidatePassword, this.password);
};

// Instance method: Check if password changed after token was issued
userSchema.methods.changedPasswordAfter = function(tokenTimestamp) {
  if (this.passwordChangedAt) {
    const changedTimestamp = parseInt(
      this.passwordChangedAt.getTime() / 1000,
      10
    );
    return tokenTimestamp < changedTimestamp;
  }
  return false;
};

// Instance method: Generate password reset token
userSchema.methods.createPasswordResetToken = function() {
  const crypto = require('crypto');
  
  // Generate random token
  const resetToken = crypto.randomBytes(32).toString('hex');
  
  // Hash token and store in database
  this.passwordResetToken = crypto
    .createHash('sha256')
    .update(resetToken)
    .digest('hex');
  
  // Set expiration (10 minutes)
  this.passwordResetExpires = Date.now() + 10 * 60 * 1000;
  
  // Return unhashed token (to send via email)
  return resetToken;
};

const User = mongoose.model('User', userSchema);

module.exports = User;
```

---

## 5.4 Authentication Controller

### Complete Auth Controller

```javascript
// src/controllers/authController.js
const crypto = require('crypto');
const User = require('../models/User');
const asyncHandler = require('../utils/asyncHandler');
const AppError = require('../utils/AppError');
const { generateToken, sendTokenResponse } = require('../utils/jwt');

// @desc    Register new user
// @route   POST /api/v1/auth/register
// @access  Public
exports.register = asyncHandler(async (req, res, next) => {
  const { name, email, password, passwordConfirm } = req.body;
  
  // Check if passwords match
  if (password !== passwordConfirm) {
    return next(new AppError('Passwords do not match', 400));
  }
  
  // Check if user already exists
  const existingUser = await User.findOne({ email });
  if (existingUser) {
    return next(new AppError('Email already registered', 409));
  }
  
  // Create user
  const user = await User.create({
    name,
    email,
    password
  });
  
  // Send token response
  sendTokenResponse(user, 201, res);
});

// @desc    Login user
// @route   POST /api/v1/auth/login
// @access  Public
exports.login = asyncHandler(async (req, res, next) => {
  const { email, password } = req.body;
  
  // Validate input
  if (!email || !password) {
    return next(new AppError('Please provide email and password', 400));
  }
  
  // Find user and include password
  const user = await User.findOne({ email }).select('+password');
  
  // Check if user exists and password is correct
  if (!user || !(await user.comparePassword(password))) {
    return next(new AppError('Invalid email or password', 401));
  }
  
  // Check if user is active
  if (!user.isActive) {
    return next(new AppError('Your account has been deactivated', 401));
  }
  
  // Send token response
  sendTokenResponse(user, 200, res);
});

// @desc    Logout user
// @route   POST /api/v1/auth/logout
// @access  Private
exports.logout = asyncHandler(async (req, res, next) => {
  // Clear cookie
  res.cookie('token', 'none', {
    expires: new Date(Date.now() + 10 * 1000),
    httpOnly: true
  });
  
  res.status(200).json({
    success: true,
    message: 'Logged out successfully'
  });
});

// @desc    Get current logged in user
// @route   GET /api/v1/auth/me
// @access  Private
exports.getMe = asyncHandler(async (req, res, next) => {
  const user = await User.findById(req.user.id);
  
  res.status(200).json({
    success: true,
    data: user
  });
});

// @desc    Update password
// @route   PUT /api/v1/auth/update-password
// @access  Private
exports.updatePassword = asyncHandler(async (req, res, next) => {
  const { currentPassword, newPassword, newPasswordConfirm } = req.body;
  
  // Validate input
  if (!currentPassword || !newPassword) {
    return next(new AppError('Please provide current and new password', 400));
  }
  
  if (newPassword !== newPasswordConfirm) {
    return next(new AppError('Passwords do not match', 400));
  }
  
  // Get user with password
  const user = await User.findById(req.user.id).select('+password');
  
  // Check current password
  if (!(await user.comparePassword(currentPassword))) {
    return next(new AppError('Current password is incorrect', 401));
  }
  
  // Update password
  user.password = newPassword;
  await user.save();
  
  // Send new token
  sendTokenResponse(user, 200, res);
});

// @desc    Forgot password
// @route   POST /api/v1/auth/forgot-password
// @access  Public
exports.forgotPassword = asyncHandler(async (req, res, next) => {
  const { email } = req.body;
  
  if (!email) {
    return next(new AppError('Please provide an email', 400));
  }
  
  // Find user
  const user = await User.findOne({ email });
  
  if (!user) {
    return next(new AppError('No user found with that email', 404));
  }
  
  // Generate reset token
  const resetToken = user.createPasswordResetToken();
  await user.save({ validateBeforeSave: false });
  
  // Create reset URL
  const resetURL = `${req.protocol}://${req.get('host')}/api/v1/auth/reset-password/${resetToken}`;
  
  try {
    // In production, send email here
    // await sendEmail({ email, subject: 'Password Reset', resetURL });
    
    res.status(200).json({
      success: true,
      message: 'Password reset token sent to email',
      // Only in development
      ...(process.env.NODE_ENV === 'development' && { resetURL })
    });
  } catch (error) {
    user.passwordResetToken = undefined;
    user.passwordResetExpires = undefined;
    await user.save({ validateBeforeSave: false });
    
    return next(new AppError('Error sending email. Try again later.', 500));
  }
});

// @desc    Reset password
// @route   PUT /api/v1/auth/reset-password/:token
// @access  Public
exports.resetPassword = asyncHandler(async (req, res, next) => {
  const { password, passwordConfirm } = req.body;
  
  if (!password || !passwordConfirm) {
    return next(new AppError('Please provide password and confirmation', 400));
  }
  
  if (password !== passwordConfirm) {
    return next(new AppError('Passwords do not match', 400));
  }
  
  // Hash token from URL
  const hashedToken = crypto
    .createHash('sha256')
    .update(req.params.token)
    .digest('hex');
  
  // Find user with valid token
  const user = await User.findOne({
    passwordResetToken: hashedToken,
    passwordResetExpires: { $gt: Date.now() }
  });
  
  if (!user) {
    return next(new AppError('Invalid or expired reset token', 400));
  }
  
  // Update password
  user.password = password;
  user.passwordResetToken = undefined;
  user.passwordResetExpires = undefined;
  await user.save();
  
  // Send new token
  sendTokenResponse(user, 200, res);
});
```

---

## 5.5 Protected Routes

### Authentication Middleware

```javascript
// src/middleware/auth.js
const jwt = require('jsonwebtoken');
const User = require('../models/User');
const asyncHandler = require('../utils/asyncHandler');
const AppError = require('../utils/AppError');

// Protect routes - Authentication
exports.protect = asyncHandler(async (req, res, next) => {
  let token;
  
  // 1. Get token from header or cookie
  if (
    req.headers.authorization &&
    req.headers.authorization.startsWith('Bearer')
  ) {
    token = req.headers.authorization.split(' ')[1];
  } else if (req.cookies && req.cookies.token) {
    token = req.cookies.token;
  }
  
  // 2. Check if token exists
  if (!token) {
    return next(new AppError('Please log in to access this resource', 401));
  }
  
  try {
    // 3. Verify token
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    
    // 4. Check if user still exists
    const user = await User.findById(decoded.id);
    
    if (!user) {
      return next(new AppError('User no longer exists', 401));
    }
    
    // 5. Check if user changed password after token was issued
    if (user.changedPasswordAfter(decoded.iat)) {
      return next(
        new AppError('Password recently changed. Please log in again.', 401)
      );
    }
    
    // 6. Check if user is active
    if (!user.isActive) {
      return next(new AppError('Your account has been deactivated', 401));
    }
    
    // 7. Grant access - attach user to request
    req.user = user;
    next();
    
  } catch (error) {
    if (error.name === 'JsonWebTokenError') {
      return next(new AppError('Invalid token. Please log in again.', 401));
    }
    if (error.name === 'TokenExpiredError') {
      return next(new AppError('Token expired. Please log in again.', 401));
    }
    return next(new AppError('Authentication failed', 401));
  }
});

// Optional authentication - doesn't fail if no token
exports.optionalAuth = asyncHandler(async (req, res, next) => {
  let token;
  
  if (
    req.headers.authorization &&
    req.headers.authorization.startsWith('Bearer')
  ) {
    token = req.headers.authorization.split(' ')[1];
  } else if (req.cookies && req.cookies.token) {
    token = req.cookies.token;
  }
  
  if (token) {
    try {
      const decoded = jwt.verify(token, process.env.JWT_SECRET);
      const user = await User.findById(decoded.id);
      
      if (user && user.isActive && !user.changedPasswordAfter(decoded.iat)) {
        req.user = user;
      }
    } catch (error) {
      // Token invalid, but that's okay for optional auth
    }
  }
  
  next();
});
```

### Using Protected Routes

```javascript
// src/routes/userRoutes.js
const express = require('express');
const router = express.Router();
const { protect } = require('../middleware/auth');
const userController = require('../controllers/userController');

// Public routes
router.get('/public-data', userController.getPublicData);

// Protected routes - require authentication
router.use(protect);  // All routes below require auth

router.get('/profile', userController.getProfile);
router.put('/profile', userController.updateProfile);
router.delete('/account', userController.deleteAccount);

module.exports = router;
```

### Alternative: Per-Route Protection

```javascript
const express = require('express');
const router = express.Router();
const { protect } = require('../middleware/auth');
const productController = require('../controllers/productController');

// Public routes
router.get('/', productController.getAllProducts);
router.get('/:id', productController.getProduct);

// Protected routes
router.post('/', protect, productController.createProduct);
router.put('/:id', protect, productController.updateProduct);
router.delete('/:id', protect, productController.deleteProduct);

module.exports = router;
```

---

## 5.6 Role-Based Access Control (RBAC)

### Authorization Middleware

```javascript
// src/middleware/auth.js (continued)

// Restrict to specific roles - Authorization
exports.restrictTo = (...roles) => {
  return (req, res, next) => {
    // Check if user's role is in allowed roles
    if (!roles.includes(req.user.role)) {
      return next(
        new AppError('You do not have permission to perform this action', 403)
      );
    }
    next();
  };
};

// Check resource ownership
exports.checkOwnership = (Model, paramField = 'id') => {
  return asyncHandler(async (req, res, next) => {
    const resource = await Model.findById(req.params[paramField]);
    
    if (!resource) {
      return next(new AppError('Resource not found', 404));
    }
    
    // Check if user owns the resource or is admin
    const isOwner = resource.user?.toString() === req.user.id;
    const isAdmin = req.user.role === 'admin';
    
    if (!isOwner && !isAdmin) {
      return next(
        new AppError('You do not have permission to access this resource', 403)
      );
    }
    
    // Attach resource to request for later use
    req.resource = resource;
    next();
  });
};
```

### Using Role-Based Access

```javascript
// src/routes/userRoutes.js
const express = require('express');
const router = express.Router();
const { protect, restrictTo } = require('../middleware/auth');
const userController = require('../controllers/userController');

// All routes below require authentication
router.use(protect);

// Any authenticated user
router.get('/me', userController.getMe);
router.put('/me', userController.updateMe);

// Admin only routes
router.get('/', restrictTo('admin'), userController.getAllUsers);
router.get('/:id', restrictTo('admin'), userController.getUser);
router.put('/:id', restrictTo('admin'), userController.updateUser);
router.delete('/:id', restrictTo('admin'), userController.deleteUser);

// Admin and moderator
router.put(
  '/:id/ban',
  restrictTo('admin', 'moderator'),
  userController.banUser
);

module.exports = router;
```

### Using Ownership Check

```javascript
// src/routes/postRoutes.js
const express = require('express');
const router = express.Router();
const { protect, restrictTo, checkOwnership } = require('../middleware/auth');
const Post = require('../models/Post');
const postController = require('../controllers/postController');

// Public routes
router.get('/', postController.getAllPosts);
router.get('/:id', postController.getPost);

// Protected routes
router.use(protect);

router.post('/', postController.createPost);

// Owner or admin can update/delete
router.put(
  '/:id',
  checkOwnership(Post, 'id'),
  postController.updatePost
);

router.delete(
  '/:id',
  checkOwnership(Post, 'id'),
  postController.deletePost
);

module.exports = router;
```

### Advanced RBAC with Permissions

```javascript
// src/config/permissions.js
const permissions = {
  admin: {
    users: ['create', 'read', 'update', 'delete'],
    products: ['create', 'read', 'update', 'delete'],
    orders: ['create', 'read', 'update', 'delete'],
    reports: ['read', 'export']
  },
  moderator: {
    users: ['read', 'update'],
    products: ['read', 'update'],
    orders: ['read'],
    reports: ['read']
  },
  user: {
    users: ['read'],  // Only own profile
    products: ['read'],
    orders: ['create', 'read']  // Only own orders
  }
};

module.exports = permissions;
```

```javascript
// src/middleware/auth.js (advanced permission check)

const permissions = require('../config/permissions');

exports.hasPermission = (resource, action) => {
  return (req, res, next) => {
    const userRole = req.user.role;
    const userPermissions = permissions[userRole];
    
    if (!userPermissions) {
      return next(new AppError('Role not configured', 500));
    }
    
    const resourcePermissions = userPermissions[resource];
    
    if (!resourcePermissions || !resourcePermissions.includes(action)) {
      return next(
        new AppError(`You cannot ${action} ${resource}`, 403)
      );
    }
    
    next();
  };
};
```

```javascript
// Usage
router.delete(
  '/products/:id',
  protect,
  hasPermission('products', 'delete'),
  productController.deleteProduct
);
```

---

## 5.7 Security Best Practices

### Install Security Packages

```bash
npm install helmet express-rate-limit express-mongo-sanitize xss-clean hpp cors
```

### Security Middleware Setup

```javascript
// src/app.js
const express = require('express');
const helmet = require('helmet');
const rateLimit = require('express-rate-limit');
const mongoSanitize = require('express-mongo-sanitize');
const xss = require('xss-clean');
const hpp = require('hpp');
const cors = require('cors');

const app = express();

// 1. Set security HTTP headers
app.use(helmet());

// 2. CORS configuration
app.use(cors({
  origin: process.env.ALLOWED_ORIGINS?.split(',') || 'http://localhost:3000',
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization']
}));

// 3. Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,  // 15 minutes
  max: 100,                   // 100 requests per window
  message: {
    success: false,
    error: 'Too many requests, please try again later'
  },
  standardHeaders: true,
  legacyHeaders: false
});
app.use('/api', limiter);

// 4. Body parser with size limit
app.use(express.json({ limit: '10kb' }));
app.use(express.urlencoded({ extended: true, limit: '10kb' }));

// 5. Data sanitization against NoSQL injection
app.use(mongoSanitize());

// 6. Data sanitization against XSS
app.use(xss());

// 7. Prevent HTTP Parameter Pollution
app.use(hpp({
  whitelist: ['price', 'rating', 'category', 'sort']
}));

// Routes...
```

### Individual Security Measures Explained

#### 1. Helmet - HTTP Headers

```javascript
const helmet = require('helmet');

app.use(helmet());

// This sets various HTTP headers:
// - X-DNS-Prefetch-Control
// - X-Frame-Options (prevents clickjacking)
// - X-Content-Type-Options
// - Strict-Transport-Security (HTTPS only)
// - X-XSS-Protection
// - Content-Security-Policy
```

#### 2. NoSQL Injection Prevention

```javascript
const mongoSanitize = require('express-mongo-sanitize');

app.use(mongoSanitize());

// Prevents attacks like:
// POST /login { "email": { "$gt": "" }, "password": { "$gt": "" } }
// This would match any user in the database!

// After sanitization:
// { "email": {}, "password": {} } - $ operators removed
```

#### 3. XSS Prevention

```javascript
const xss = require('xss-clean');

app.use(xss());

// Prevents attacks like:
// { "name": "<script>alert('hacked')</script>" }

// After sanitization:
// { "name": "&lt;script&gt;alert('hacked')&lt;/script&gt;" }
```

#### 4. HPP (HTTP Parameter Pollution)

```javascript
const hpp = require('hpp');

app.use(hpp({
  whitelist: ['price', 'category']  // Allow duplicates for these
}));

// Prevents attacks like:
// GET /api/products?sort=price&sort=name
// Only the last 'sort' value will be used

// Whitelist allows:
// GET /api/products?category=electronics&category=clothing
// Both categories will be used for filtering
```

### Password Security

```javascript
// Strong password validation in schema
const passwordRegex = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$/;

password: {
  type: String,
  required: true,
  minlength: 8,
  validate: {
    validator: function(v) {
      return passwordRegex.test(v);
    },
    message: 'Password must contain uppercase, lowercase, number, and special character'
  },
  select: false
}
```

### Sensitive Data Protection

```javascript
// Never send sensitive data in responses
userSchema.methods.toJSON = function() {
  const user = this.toObject();
  
  // Remove sensitive fields
  delete user.password;
  delete user.passwordResetToken;
  delete user.passwordResetExpires;
  delete user.__v;
  
  return user;
};

// Or use transform option
userSchema.set('toJSON', {
  transform: function(doc, ret) {
    delete ret.password;
    delete ret.passwordResetToken;
    delete ret.passwordResetExpires;
    delete ret.__v;
    return ret;
  }
});
```

---

## 5.8 Rate Limiting

### Basic Rate Limiter

```javascript
const rateLimit = require('express-rate-limit');

// General API limiter
const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,  // 15 minutes
  max: 100,                   // 100 requests per window
  message: {
    success: false,
    error: 'Too many requests from this IP'
  }
});

app.use('/api', apiLimiter);
```

### Different Limiters for Different Routes

```javascript
// Strict limiter for auth routes
const authLimiter = rateLimit({
  windowMs: 60 * 60 * 1000,  // 1 hour
  max: 5,                     // 5 attempts
  message: {
    success: false,
    error: 'Too many login attempts. Please try again in an hour.'
  },
  skipSuccessfulRequests: true  // Don't count successful logins
});

// Apply to auth routes
app.use('/api/v1/auth/login', authLimiter);
app.use('/api/v1/auth/forgot-password', authLimiter);

// Lenient limiter for public routes
const publicLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 200
});

app.use('/api/v1/products', publicLimiter);

// Strict limiter for sensitive operations
const sensitiveLimiter = rateLimit({
  windowMs: 60 * 60 * 1000,
  max: 10,
  message: {
    success: false,
    error: 'Rate limit exceeded for this operation'
  }
});

app.use('/api/v1/auth/reset-password', sensitiveLimiter);
app.use('/api/v1/users/delete-account', sensitiveLimiter);
```

### Custom Rate Limiter with Redis

```bash
npm install rate-limit-redis ioredis
```

```javascript
const rateLimit = require('express-rate-limit');
const RedisStore = require('rate-limit-redis');
const Redis = require('ioredis');

const redisClient = new Redis(process.env.REDIS_URL);

const limiter = rateLimit({
  store: new RedisStore({
    client: redisClient,
    prefix: 'rate_limit:'
  }),
  windowMs: 15 * 60 * 1000,
  max: 100,
  message: {
    success: false,
    error: 'Too many requests'
  }
});

app.use('/api', limiter);
```

---

## 5.9 CORS Configuration

### Basic CORS Setup

```javascript
const cors = require('cors');

// Allow all origins (not recommended for production)
app.use(cors());

// Allow specific origin
app.use(cors({
  origin: 'https://myapp.com'
}));
```

### Advanced CORS Configuration

```javascript
const cors = require('cors');

const corsOptions = {
  // Dynamic origin based on whitelist
  origin: function(origin, callback) {
    const whitelist = [
      'http://localhost:3000',
      'http://localhost:5173',
      'https://myapp.com',
      'https://admin.myapp.com'
    ];
    
    // Allow requests with no origin (mobile apps, curl, etc.)
    if (!origin) return callback(null, true);
    
    if (whitelist.indexOf(origin) !== -1) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  
  // Allow credentials (cookies, authorization headers)
  credentials: true,
  
  // Allowed HTTP methods
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE', 'OPTIONS'],
  
  // Allowed headers
  allowedHeaders: [
    'Content-Type',
    'Authorization',
    'X-Requested-With',
    'Accept'
  ],
  
  // Expose headers to frontend
  exposedHeaders: [
    'X-Total-Count',
    'X-Page-Count',
    'X-RateLimit-Limit',
    'X-RateLimit-Remaining'
  ],
  
  // Cache preflight request results (in seconds)
  maxAge: 86400,  // 24 hours
  
  // Pass preflight response to next handler
  preflightContinue: false,
  
  // Success status for legacy browsers
  optionsSuccessStatus: 204
};

app.use(cors(corsOptions));

// Handle preflight requests
app.options('*', cors(corsOptions));
```

### Environment-Based CORS

```javascript
const cors = require('cors');

const getCorsOptions = () => {
  if (process.env.NODE_ENV === 'production') {
    return {
      origin: process.env.ALLOWED_ORIGINS.split(','),
      credentials: true
    };
  }
  
  // Development - allow all
  return {
    origin: true,
    credentials: true
  };
};

app.use(cors(getCorsOptions()));
```

---

## 5.10 Complete Auth Routes

```javascript
// src/routes/authRoutes.js
const express = require('express');
const router = express.Router();
const rateLimit = require('express-rate-limit');
const authController = require('../controllers/authController');
const { protect } = require('../middleware/auth');

// Rate limiters
const authLimiter = rateLimit({
  windowMs: 60 * 60 * 1000,
  max: 5,
  message: { success: false, error: 'Too many attempts. Try again later.' }
});

const passwordLimiter = rateLimit({
  windowMs: 60 * 60 * 1000,
  max: 3,
  message: { success: false, error: 'Too many password reset attempts.' }
});

// Public routes
router.post('/register', authController.register);
router.post('/login', authLimiter, authController.login);
router.post('/forgot-password', passwordLimiter, authController.forgotPassword);
router.put('/reset-password/:token', authController.resetPassword);

// Protected routes
router.use(protect);

router.post('/logout', authController.logout);
router.get('/me', authController.getMe);
router.put('/update-password', authController.updatePassword);
router.put('/update-me', authController.updateMe);
router.delete('/delete-me', authController.deleteMe);

module.exports = router;
```

---

## 5.11 Complete Security Setup

```javascript
// src/app.js - Complete secure Express app
const express = require('express');
const helmet = require('helmet');
const rateLimit = require('express-rate-limit');
const mongoSanitize = require('express-mongo-sanitize');
const xss = require('xss-clean');
const hpp = require('hpp');
const cors = require('cors');
const cookieParser = require('cookie-parser');
const compression = require('compression');

// Route imports
const authRoutes = require('./routes/authRoutes');
const userRoutes = require('./routes/userRoutes');
const productRoutes = require('./routes/productRoutes');

// Middleware imports
const errorHandler = require('./middleware/errorHandler');

const app = express();

// Trust proxy (for rate limiting behind reverse proxy)
app.set('trust proxy', 1);

// Security HTTP headers
app.use(helmet());

// CORS
app.use(cors({
  origin: process.env.ALLOWED_ORIGINS?.split(',') || 'http://localhost:3000',
  credentials: true
}));
app.options('*', cors());

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100,
  message: { success: false, error: 'Too many requests' }
});
app.use('/api', limiter);

// Body parsers
app.use(express.json({ limit: '10kb' }));
app.use(express.urlencoded({ extended: true, limit: '10kb' }));
app.use(cookieParser());

// Data sanitization
app.use(mongoSanitize());
app.use(xss());
app.use(hpp({ whitelist: ['price', 'rating', 'category'] }));

// Compression
app.use(compression());

// Routes
app.use('/api/v1/auth', authRoutes);
app.use('/api/v1/users', userRoutes);
app.use('/api/v1/products', productRoutes);

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'OK' });
});

// 404 handler
app.use((req, res) => {
  res.status(404).json({
    success: false,
    error: `Route ${req.method} ${req.originalUrl} not found`
  });
});

// Error handler
app.use(errorHandler);

module.exports = app;
```

---

## 📚 Chapter Summary

### What You Learned

✅ Authentication verifies identity; Authorization verifies permissions  
✅ JWT provides stateless authentication with tokens  
✅ Bcrypt securely hashes passwords with salt  
✅ Protected routes require valid authentication  
✅ RBAC restricts access based on user roles  
✅ Security middleware protects against common attacks  
✅ Rate limiting prevents abuse  
✅ CORS controls cross-origin access  

### Authentication Flow

```javascript
// 1. Register
POST /api/v1/auth/register { name, email, password }
→ Returns JWT token

// 2. Login
POST /api/v1/auth/login { email, password }
→ Returns JWT token

// 3. Access protected route
GET /api/v1/users/me
Headers: { Authorization: "Bearer <token>" }
→ Returns user data
```

### Security Checklist

```
✅ Use HTTPS in production
✅ Hash passwords with bcrypt
✅ Use JWT with expiration
✅ Implement rate limiting
✅ Sanitize user input
✅ Set secure HTTP headers
✅ Configure CORS properly
✅ Never expose sensitive data
✅ Validate all input
✅ Use environment variables for secrets
```

---