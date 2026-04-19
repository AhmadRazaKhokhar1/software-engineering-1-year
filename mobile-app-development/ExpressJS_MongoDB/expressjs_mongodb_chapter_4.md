# Chapter 4: CRUD Operations with Express & MongoDB

## 🎯 Learning Objectives

By the end of this chapter, you will:
- Connect Express applications to MongoDB using Mongoose
- Create robust Mongoose models with proper validation
- Build complete CRUD (Create, Read, Update, Delete) APIs
- Implement data validation at multiple levels
- Add pagination to handle large datasets
- Implement filtering and sorting capabilities
- Handle errors gracefully in database operations

---

## 4.1 Connecting Express to MongoDB

### Setting Up the Connection

**Install Required Packages:**

```bash
npm install mongoose dotenv
```

**Project Structure:**

```
project/
├── src/
│   ├── config/
│   │   └── db.js
│   ├── models/
│   │   └── Product.js
│   ├── controllers/
│   │   └── productController.js
│   ├── routes/
│   │   └── productRoutes.js
│   ├── middleware/
│   │   └── errorHandler.js
│   ├── utils/
│   │   ├── AppError.js
│   │   └── asyncHandler.js
│   └── app.js
├── .env
├── server.js
└── package.json
```

### Database Connection Module

```javascript
// src/config/db.js
const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    const conn = await mongoose.connect(process.env.MONGODB_URI);
    
    console.log(`✅ MongoDB Connected: ${conn.connection.host}`);
    
    // Connection event listeners
    mongoose.connection.on('error', (err) => {
      console.error(`MongoDB connection error: ${err}`);
    });
    
    mongoose.connection.on('disconnected', () => {
      console.warn('MongoDB disconnected');
    });
    
    mongoose.connection.on('reconnected', () => {
      console.log('MongoDB reconnected');
    });
    
  } catch (error) {
    console.error(`❌ Error: ${error.message}`);
    process.exit(1);
  }
};

module.exports = connectDB;
```

### Environment Configuration

```env
# .env
NODE_ENV=development
PORT=3000

# Local MongoDB
MONGODB_URI=mongodb://localhost:27017/myshop

# MongoDB Atlas (cloud)
# MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/myshop
```

### Server Entry Point

```javascript
// server.js
require('dotenv').config();

const app = require('./src/app');
const connectDB = require('./src/config/db');

const PORT = process.env.PORT || 3000;

const startServer = async () => {
  try {
    await connectDB();
    
    app.listen(PORT, () => {
      console.log(`🚀 Server running in ${process.env.NODE_ENV} mode on port ${PORT}`);
    });
  } catch (error) {
    console.error('Failed to start server:', error);
    process.exit(1);
  }
};

startServer();

// Handle unhandled rejections
process.on('unhandledRejection', (err) => {
  console.error('Unhandled Rejection:', err.message);
  process.exit(1);
});
```

### Express App Configuration

```javascript
// src/app.js
const express = require('express');
const cors = require('cors');
const morgan = require('morgan');

const productRoutes = require('./routes/productRoutes');
const errorHandler = require('./middleware/errorHandler');

const app = express();

// Middleware
app.use(cors());
app.use(express.json());

if (process.env.NODE_ENV === 'development') {
  app.use(morgan('dev'));
}

// Routes
app.use('/api/v1/products', productRoutes);

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'OK', timestamp: new Date().toISOString() });
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

## 4.2 Mongoose Models

### Creating a Complete Product Model

```javascript
// src/models/Product.js
const mongoose = require('mongoose');

const productSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Product name is required'],
    trim: true,
    minlength: [3, 'Name must be at least 3 characters'],
    maxlength: [100, 'Name cannot exceed 100 characters']
  },
  
  slug: {
    type: String,
    unique: true,
    lowercase: true
  },
  
  description: {
    type: String,
    required: [true, 'Description is required'],
    minlength: [10, 'Description must be at least 10 characters'],
    maxlength: [2000, 'Description cannot exceed 2000 characters']
  },
  
  price: {
    type: Number,
    required: [true, 'Price is required'],
    min: [0, 'Price cannot be negative']
  },
  
  discountPrice: {
    type: Number,
    validate: {
      validator: function(value) {
        return !value || value < this.price;
      },
      message: 'Discount price must be less than regular price'
    }
  },
  
  category: {
    type: String,
    required: [true, 'Category is required'],
    enum: {
      values: ['electronics', 'clothing', 'books', 'home', 'sports', 'other'],
      message: '{VALUE} is not a valid category'
    }
  },
  
  brand: {
    type: String,
    trim: true
  },
  
  stock: {
    type: Number,
    required: [true, 'Stock quantity is required'],
    min: [0, 'Stock cannot be negative'],
    default: 0
  },
  
  images: {
    type: [String],
    validate: {
      validator: function(arr) {
        return arr.length <= 10;
      },
      message: 'Cannot have more than 10 images'
    }
  },
  
  featured: {
    type: Boolean,
    default: false
  },
  
  ratings: {
    average: {
      type: Number,
      default: 0,
      min: [0, 'Rating cannot be below 0'],
      max: [5, 'Rating cannot exceed 5'],
      set: val => Math.round(val * 10) / 10
    },
    count: {
      type: Number,
      default: 0
    }
  },
  
  tags: [String],
  
  isActive: {
    type: Boolean,
    default: true
  }
  
}, {
  timestamps: true,
  toJSON: { virtuals: true },
  toObject: { virtuals: true }
});

// Indexes
productSchema.index({ name: 'text', description: 'text' });
productSchema.index({ category: 1, price: 1 });
productSchema.index({ slug: 1 });

// Virtual: Check if in stock
productSchema.virtual('inStock').get(function() {
  return this.stock > 0;
});

// Virtual: Discount percentage
productSchema.virtual('discountPercentage').get(function() {
  if (this.discountPrice && this.price > 0) {
    return Math.round(((this.price - this.discountPrice) / this.price) * 100);
  }
  return 0;
});

// Pre-save: Generate slug
productSchema.pre('save', function(next) {
  if (this.isModified('name')) {
    this.slug = this.name
      .toLowerCase()
      .replace(/[^a-z0-9]+/g, '-')
      .replace(/(^-|-$)/g, '');
  }
  next();
});

// Pre-find: Only active products
productSchema.pre(/^find/, function(next) {
  if (this.getQuery().isActive === undefined) {
    this.find({ isActive: { $ne: false } });
  }
  next();
});

// Static methods
productSchema.statics.findByCategory = function(category) {
  return this.find({ category });
};

productSchema.statics.findFeatured = function(limit = 10) {
  return this.find({ featured: true }).limit(limit);
};

// Instance methods
productSchema.methods.applyDiscount = function(percentage) {
  this.discountPrice = this.price * (1 - percentage / 100);
  return this.save();
};

const Product = mongoose.model('Product', productSchema);

module.exports = Product;
```

---

## 4.3 CRUD Operations - Create

### Create Controller

```javascript
// src/controllers/productController.js
const Product = require('../models/Product');
const asyncHandler = require('../utils/asyncHandler');
const AppError = require('../utils/AppError');

// @desc    Create a new product
// @route   POST /api/v1/products
// @access  Private/Admin
exports.createProduct = asyncHandler(async (req, res, next) => {
  const product = await Product.create(req.body);
  
  res.status(201).json({
    success: true,
    message: 'Product created successfully',
    data: product
  });
});
```

### Utility Files

```javascript
// src/utils/asyncHandler.js
module.exports = (fn) => {
  return (req, res, next) => {
    Promise.resolve(fn(req, res, next)).catch(next);
  };
};
```

```javascript
// src/utils/AppError.js
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

### Testing Create

```bash
curl -X POST http://localhost:3000/api/v1/products \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Wireless Headphones",
    "description": "High-quality wireless headphones with noise cancellation",
    "price": 149.99,
    "category": "electronics",
    "stock": 50
  }'
```

---

## 4.4 CRUD Operations - Read

### Get All Products

```javascript
// @desc    Get all products
// @route   GET /api/v1/products
// @access  Public
exports.getAllProducts = asyncHandler(async (req, res, next) => {
  const products = await Product.find();
  
  res.status(200).json({
    success: true,
    count: products.length,
    data: products
  });
});
```

### Get Single Product

```javascript
// @desc    Get single product
// @route   GET /api/v1/products/:id
// @access  Public
exports.getProduct = asyncHandler(async (req, res, next) => {
  const product = await Product.findById(req.params.id);
  
  if (!product) {
    return next(new AppError('Product not found', 404));
  }
  
  res.status(200).json({
    success: true,
    data: product
  });
});
```

### Get Product by Slug

```javascript
// @desc    Get product by slug
// @route   GET /api/v1/products/slug/:slug
// @access  Public
exports.getProductBySlug = asyncHandler(async (req, res, next) => {
  const product = await Product.findOne({ slug: req.params.slug });
  
  if (!product) {
    return next(new AppError('Product not found', 404));
  }
  
  res.status(200).json({
    success: true,
    data: product
  });
});
```

### Query Methods Reference

```javascript
// Find all
const products = await Product.find();

// Find with conditions
const electronics = await Product.find({ category: 'electronics' });

// Find one
const product = await Product.findOne({ slug: 'wireless-headphones' });

// Find by ID
const product = await Product.findById('65a1b2c3d4e5f6789012345');

// Select specific fields
const names = await Product.find().select('name price');

// Exclude fields
const products = await Product.find().select('-description');

// Sort results
const sorted = await Product.find().sort({ price: 1 });  // Ascending
const sorted = await Product.find().sort('-price');      // Descending

// Limit and skip
const page1 = await Product.find().skip(0).limit(10);
const page2 = await Product.find().skip(10).limit(10);

// Count documents
const count = await Product.countDocuments({ category: 'electronics' });

// Check existence
const exists = await Product.exists({ slug: 'wireless-headphones' });
```

---

## 4.5 CRUD Operations - Update

### Update Product

```javascript
// @desc    Update product
// @route   PUT /api/v1/products/:id
// @access  Private/Admin
exports.updateProduct = asyncHandler(async (req, res, next) => {
  let product = await Product.findById(req.params.id);
  
  if (!product) {
    return next(new AppError('Product not found', 404));
  }
  
  product = await Product.findByIdAndUpdate(
    req.params.id,
    req.body,
    {
      new: true,           // Return updated document
      runValidators: true  // Run schema validators
    }
  );
  
  res.status(200).json({
    success: true,
    message: 'Product updated successfully',
    data: product
  });
});
```

### Partial Update (PATCH)

```javascript
// @desc    Partial update product
// @route   PATCH /api/v1/products/:id
// @access  Private/Admin
exports.partialUpdateProduct = asyncHandler(async (req, res, next) => {
  const allowedFields = ['name', 'description', 'price', 'stock', 'discountPrice'];
  const updates = {};
  
  Object.keys(req.body).forEach(key => {
    if (allowedFields.includes(key)) {
      updates[key] = req.body[key];
    }
  });
  
  if (Object.keys(updates).length === 0) {
    return next(new AppError('No valid fields to update', 400));
  }
  
  const product = await Product.findByIdAndUpdate(
    req.params.id,
    { $set: updates },
    { new: true, runValidators: true }
  );
  
  if (!product) {
    return next(new AppError('Product not found', 404));
  }
  
  res.status(200).json({
    success: true,
    data: product
  });
});
```

### Update Methods Reference

```javascript
// findByIdAndUpdate - Returns document
const updated = await Product.findByIdAndUpdate(
  id,
  { price: 99.99 },
  { new: true, runValidators: true }
);

// updateOne - Returns result object
const result = await Product.updateOne(
  { _id: id },
  { $set: { price: 99.99 } }
);

// updateMany - Update multiple documents
const result = await Product.updateMany(
  { category: 'electronics' },
  { $inc: { price: 10 } }
);

// Update operators
await Product.findByIdAndUpdate(id, {
  $set: { price: 99.99 },        // Set value
  $inc: { stock: -1 },            // Increment/decrement
  $push: { tags: 'sale' },        // Add to array
  $pull: { tags: 'old' },         // Remove from array
  $addToSet: { tags: 'unique' }   // Add if not exists
});

// Using save() - Triggers middleware
const product = await Product.findById(id);
product.price = 99.99;
await product.save();
```

---

## 4.6 CRUD Operations - Delete

### Delete Product

```javascript
// @desc    Delete product
// @route   DELETE /api/v1/products/:id
// @access  Private/Admin
exports.deleteProduct = asyncHandler(async (req, res, next) => {
  const product = await Product.findById(req.params.id);
  
  if (!product) {
    return next(new AppError('Product not found', 404));
  }
  
  await product.deleteOne();
  
  res.status(200).json({
    success: true,
    message: 'Product deleted successfully',
    data: {}
  });
});
```

### Soft Delete (Recommended)

```javascript
// @desc    Soft delete product
// @route   DELETE /api/v1/products/:id
// @access  Private/Admin
exports.softDeleteProduct = asyncHandler(async (req, res, next) => {
  const product = await Product.findByIdAndUpdate(
    req.params.id,
    { 
      isActive: false,
      deletedAt: new Date()
    },
    { new: true }
  );
  
  if (!product) {
    return next(new AppError('Product not found', 404));
  }
  
  res.status(200).json({
    success: true,
    message: 'Product deactivated successfully'
  });
});

// @desc    Restore soft-deleted product
// @route   PATCH /api/v1/products/:id/restore
// @access  Private/Admin
exports.restoreProduct = asyncHandler(async (req, res, next) => {
  const product = await Product.findByIdAndUpdate(
    req.params.id,
    { 
      isActive: true,
      $unset: { deletedAt: 1 }
    },
    { new: true }
  );
  
  if (!product) {
    return next(new AppError('Product not found', 404));
  }
  
  res.status(200).json({
    success: true,
    message: 'Product restored successfully',
    data: product
  });
});
```

### Delete Methods Reference

```javascript
// findByIdAndDelete - Returns deleted document
const deleted = await Product.findByIdAndDelete(id);

// deleteOne - Returns result object
const result = await Product.deleteOne({ _id: id });

// deleteMany - Delete multiple
const result = await Product.deleteMany({ category: 'discontinued' });

// Using deleteOne on document instance
const product = await Product.findById(id);
await product.deleteOne();
```

---

## 4.7 Validation

### Schema-Level Validation

```javascript
const productSchema = new mongoose.Schema({
  // Required field
  name: {
    type: String,
    required: [true, 'Product name is required']
  },
  
  // String length validation
  description: {
    type: String,
    minlength: [10, 'Must be at least 10 characters'],
    maxlength: [2000, 'Cannot exceed 2000 characters']
  },
  
  // Number range validation
  price: {
    type: Number,
    min: [0.01, 'Price must be at least 0.01'],
    max: [1000000, 'Price cannot exceed 1,000,000']
  },
  
  // Enum validation
  category: {
    type: String,
    enum: {
      values: ['electronics', 'clothing', 'books'],
      message: '{VALUE} is not a valid category'
    }
  },
  
  // Regex validation
  email: {
    type: String,
    match: [/^\S+@\S+\.\S+$/, 'Please enter a valid email']
  },
  
  // Custom validator
  sku: {
    type: String,
    validate: {
      validator: function(v) {
        return /^[A-Z]{3}-\d{4}$/.test(v);
      },
      message: props => `${props.value} is not a valid SKU (format: AAA-0000)`
    }
  },
  
  // Cross-field validation
  discountPrice: {
    type: Number,
    validate: {
      validator: function(v) {
        return v < this.price;
      },
      message: 'Discount must be less than regular price'
    }
  }
});
```

### Request Validation Middleware

```javascript
// src/middleware/validate.js
exports.validateCreateProduct = (req, res, next) => {
  const errors = [];
  const { name, description, price, category } = req.body;
  
  if (!name || name.trim().length < 3) {
    errors.push('Name must be at least 3 characters');
  }
  
  if (!description || description.trim().length < 10) {
    errors.push('Description must be at least 10 characters');
  }
  
  if (price === undefined || price <= 0) {
    errors.push('Price must be a positive number');
  }
  
  const validCategories = ['electronics', 'clothing', 'books', 'home', 'sports', 'other'];
  if (!category || !validCategories.includes(category)) {
    errors.push(`Category must be one of: ${validCategories.join(', ')}`);
  }
  
  if (errors.length > 0) {
    return res.status(400).json({
      success: false,
      error: 'Validation failed',
      details: errors
    });
  }
  
  next();
};

exports.validateObjectId = (paramName = 'id') => {
  return (req, res, next) => {
    const id = req.params[paramName];
    
    if (!/^[a-f\d]{24}$/i.test(id)) {
      return res.status(400).json({
        success: false,
        error: `Invalid ${paramName} format`
      });
    }
    
    next();
  };
};
```

### Using Express-Validator

```bash
npm install express-validator
```

```javascript
// src/middleware/validators/productValidator.js
const { body, param, validationResult } = require('express-validator');

exports.createProductRules = [
  body('name')
    .trim()
    .notEmpty().withMessage('Name is required')
    .isLength({ min: 3, max: 100 }).withMessage('Name must be 3-100 characters'),
  
  body('description')
    .trim()
    .notEmpty().withMessage('Description is required')
    .isLength({ min: 10 }).withMessage('Description must be at least 10 characters'),
  
  body('price')
    .notEmpty().withMessage('Price is required')
    .isFloat({ min: 0.01 }).withMessage('Price must be greater than 0'),
  
  body('category')
    .notEmpty().withMessage('Category is required')
    .isIn(['electronics', 'clothing', 'books', 'home', 'sports', 'other'])
    .withMessage('Invalid category'),
  
  body('stock')
    .optional()
    .isInt({ min: 0 }).withMessage('Stock must be a non-negative integer')
];

exports.updateProductRules = [
  param('id').isMongoId().withMessage('Invalid product ID'),
  
  body('name')
    .optional()
    .trim()
    .isLength({ min: 3, max: 100 }).withMessage('Name must be 3-100 characters'),
  
  body('price')
    .optional()
    .isFloat({ min: 0.01 }).withMessage('Price must be greater than 0')
];

exports.validate = (req, res, next) => {
  const errors = validationResult(req);
  
  if (!errors.isEmpty()) {
    return res.status(400).json({
      success: false,
      error: 'Validation failed',
      details: errors.array().map(err => ({
        field: err.path,
        message: err.msg
      }))
    });
  }
  
  next();
};
```

---

## 4.8 Pagination & Filtering

### API Features Class

```javascript
// src/utils/APIFeatures.js
class APIFeatures {
  constructor(query, queryString) {
    this.query = query;
    this.queryString = queryString;
  }
  
  // Filter by field values
  filter() {
    const queryObj = { ...this.queryString };
    const excludedFields = ['page', 'sort', 'limit', 'fields', 'search'];
    excludedFields.forEach(field => delete queryObj[field]);
    
    // Convert gt, gte, lt, lte to MongoDB format
    let queryStr = JSON.stringify(queryObj);
    queryStr = queryStr.replace(/\b(gt|gte|lt|lte|in|ne)\b/g, match => `$${match}`);
    
    this.query = this.query.find(JSON.parse(queryStr));
    return this;
  }
  
  // Text search
  search() {
    if (this.queryString.search) {
      const searchTerm = this.queryString.search;
      this.query = this.query.find({
        $or: [
          { name: { $regex: searchTerm, $options: 'i' } },
          { description: { $regex: searchTerm, $options: 'i' } }
        ]
      });
    }
    return this;
  }
  
  // Sort results
  sort() {
    if (this.queryString.sort) {
      const sortBy = this.queryString.sort.split(',').join(' ');
      this.query = this.query.sort(sortBy);
    } else {
      this.query = this.query.sort('-createdAt');
    }
    return this;
  }
  
  // Select fields
  limitFields() {
    if (this.queryString.fields) {
      const fields = this.queryString.fields.split(',').join(' ');
      this.query = this.query.select(fields);
    } else {
      this.query = this.query.select('-__v');
    }
    return this;
  }
  
  // Pagination
  paginate() {
    const page = parseInt(this.queryString.page, 10) || 1;
    const limit = parseInt(this.queryString.limit, 10) || 10;
    const skip = (page - 1) * limit;
    
    this.query = this.query.skip(skip).limit(limit);
    this.pagination = { page, limit, skip };
    
    return this;
  }
}

module.exports = APIFeatures;
```

### Controller with Pagination

```javascript
// @desc    Get all products with filtering, sorting, pagination
// @route   GET /api/v1/products
// @access  Public
exports.getAllProducts = asyncHandler(async (req, res, next) => {
  // Count total documents
  const totalCount = await Product.countDocuments();
  
  // Build query with features
  const features = new APIFeatures(Product.find(), req.query)
    .filter()
    .search()
    .sort()
    .limitFields()
    .paginate();
  
  const products = await features.query;
  
  // Pagination info
  const { page, limit } = features.pagination;
  const totalPages = Math.ceil(totalCount / limit);
  
  res.status(200).json({
    success: true,
    count: products.length,
    pagination: {
      currentPage: page,
      totalPages,
      totalItems: totalCount,
      itemsPerPage: limit,
      hasNextPage: page < totalPages,
      hasPrevPage: page > 1
    },
    data: products
  });
});
```

### Query Examples

```bash
# Basic pagination
GET /api/v1/products?page=2&limit=10

# Filter by category
GET /api/v1/products?category=electronics

# Filter by price range
GET /api/v1/products?price[gte]=100&price[lte]=500

# Search
GET /api/v1/products?search=wireless

# Sort by price (ascending)
GET /api/v1/products?sort=price

# Sort by price (descending)
GET /api/v1/products?sort=-price

# Multiple sort fields
GET /api/v1/products?sort=-ratings.average,price

# Select specific fields
GET /api/v1/products?fields=name,price,category

# Combined query
GET /api/v1/products?category=electronics&price[gte]=50&sort=-price&page=1&limit=10
```

### Pagination Response

```json
{
  "success": true,
  "count": 10,
  "pagination": {
    "currentPage": 2,
    "totalPages": 5,
    "totalItems": 47,
    "itemsPerPage": 10,
    "hasNextPage": true,
    "hasPrevPage": true
  },
  "data": [...]
}
```

---

## 4.9 Sorting

### Sort Implementation

```javascript
// In APIFeatures class
sort() {
  if (this.queryString.sort) {
    // Input: "price,-createdAt" → Output: "price -createdAt"
    const sortBy = this.queryString.sort.split(',').join(' ');
    this.query = this.query.sort(sortBy);
  } else {
    this.query = this.query.sort('-createdAt');
  }
  return this;
}
```

### Sort Examples

```javascript
// Ascending (lowest first)
Product.find().sort({ price: 1 });
Product.find().sort('price');

// Descending (highest first)
Product.find().sort({ price: -1 });
Product.find().sort('-price');

// Multiple fields
Product.find().sort({ category: 1, price: -1 });
Product.find().sort('category -price');

// Nested field
Product.find().sort({ 'ratings.average': -1 });
```

### Common Sort Patterns

```bash
# Newest first
GET /api/v1/products?sort=-createdAt

# Cheapest first
GET /api/v1/products?sort=price

# Best rated
GET /api/v1/products?sort=-ratings.average

# Alphabetical
GET /api/v1/products?sort=name

# Featured first, then by rating
GET /api/v1/products?sort=-featured,-ratings.average
```

---

## 4.10 Error Handling

### Global Error Handler

```javascript
// src/middleware/errorHandler.js
const AppError = require('../utils/AppError');

const handleCastErrorDB = (err) => {
  return new AppError(`Invalid ${err.path}: ${err.value}`, 400);
};

const handleDuplicateFieldsDB = (err) => {
  const field = Object.keys(err.keyValue)[0];
  const value = err.keyValue[field];
  return new AppError(`Duplicate value '${value}' for field '${field}'`, 409);
};

const handleValidationErrorDB = (err) => {
  const errors = Object.values(err.errors).map(el => el.message);
  return new AppError(`Validation failed: ${errors.join('. ')}`, 400);
};

const sendErrorDev = (err, res) => {
  res.status(err.statusCode).json({
    success: false,
    status: err.status,
    message: err.message,
    stack: err.stack,
    error: err
  });
};

const sendErrorProd = (err, res) => {
  if (err.isOperational) {
    res.status(err.statusCode).json({
      success: false,
      message: err.message
    });
  } else {
    console.error('ERROR:', err);
    res.status(500).json({
      success: false,
      message: 'Something went wrong'
    });
  }
};

module.exports = (err, req, res, next) => {
  err.statusCode = err.statusCode || 500;
  err.status = err.status || 'error';
  
  if (process.env.NODE_ENV === 'development') {
    sendErrorDev(err, res);
  } else {
    let error = { ...err, message: err.message };
    
    if (err.name === 'CastError') error = handleCastErrorDB(err);
    if (err.code === 11000) error = handleDuplicateFieldsDB(err);
    if (err.name === 'ValidationError') error = handleValidationErrorDB(err);
    
    sendErrorProd(error, res);
  }
};
```

### Error Responses

```json
// 400 Bad Request
{
  "success": false,
  "message": "Validation failed: Price must be at least 0.01"
}

// 404 Not Found
{
  "success": false,
  "message": "Product not found"
}

// 409 Conflict
{
  "success": false,
  "message": "Duplicate value 'wireless-headphones' for field 'slug'"
}

// 500 Internal Server Error (Production)
{
  "success": false,
  "message": "Something went wrong"
}
```

---

## 4.11 Complete Routes File

```javascript
// src/routes/productRoutes.js
const express = require('express');
const router = express.Router();
const {
  getAllProducts,
  getProduct,
  getProductBySlug,
  createProduct,
  updateProduct,
  deleteProduct,
  getProductStats
} = require('../controllers/productController');
const {
  createProductRules,
  updateProductRules,
  validate
} = require('../middleware/validators/productValidator');
const { validateObjectId } = require('../middleware/validate');

// Special routes (before :id)
router.get('/stats', getProductStats);
router.get('/slug/:slug', getProductBySlug);

// Main CRUD routes
router.route('/')
  .get(getAllProducts)
  .post(createProductRules, validate, createProduct);

router.route('/:id')
  .get(validateObjectId('id'), getProduct)
  .put(validateObjectId('id'), updateProductRules, validate, updateProduct)
  .delete(validateObjectId('id'), deleteProduct);

module.exports = router;
```

---

## 📚 Chapter Summary

### What You Learned

✅ Connect Express to MongoDB using Mongoose  
✅ Create comprehensive Mongoose models with validation  
✅ Implement all CRUD operations  
✅ Validate data at schema and request levels  
✅ Build pagination for large datasets  
✅ Implement filtering and search  
✅ Add sorting capabilities  
✅ Handle errors gracefully  

### CRUD Quick Reference

```javascript
// CREATE
const doc = await Model.create(data);

// READ
const all = await Model.find();
const one = await Model.findById(id);
const filtered = await Model.find({ field: value });

// UPDATE
const updated = await Model.findByIdAndUpdate(id, data, { new: true });

// DELETE
await Model.findByIdAndDelete(id);
```

### Query Features

```javascript
// Pagination
.skip((page - 1) * limit).limit(limit)

// Filtering
.find({ category: 'electronics', price: { $gte: 100 } })

// Sorting
.sort('-price createdAt')

// Field selection
.select('name price -_id')
```

---