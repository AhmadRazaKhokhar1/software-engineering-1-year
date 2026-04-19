# Chapter 3: MongoDB Fundamentals & Data Modeling

## 🎯 Learning Objectives

By the end of this chapter, you will:
- Understand what MongoDB is and how it differs from SQL databases
- Master MongoDB's document-based data model
- Work with documents, collections, and databases
- Use MongoDB Compass for visual database management
- Apply schema design principles for optimal performance
- Choose between embedding and referencing for relationships
- Get started with Mongoose ODM for Node.js

---

## 3.1 What is MongoDB?

### Introduction to MongoDB

**MongoDB** is a **NoSQL document database** designed for modern application development.

Instead of storing data in tables with rows and columns (like SQL databases), MongoDB stores data in **flexible, JSON-like documents**.

```javascript
// A MongoDB document (looks like JSON)
{
  "_id": ObjectId("507f1f77bcf86cd799439011"),
  "name": "John Doe",
  "email": "john@example.com",
  "age": 28,
  "address": {
    "street": "123 Main St",
    "city": "New York",
    "country": "USA"
  },
  "hobbies": ["reading", "gaming", "coding"],
  "createdAt": ISODate("2024-01-15T10:30:00Z")
}
```

### Key Characteristics

| Feature | Description |
|---------|-------------|
| **Document-Oriented** | Data stored as JSON-like documents |
| **Schema Flexible** | Documents can have different structures |
| **Scalable** | Horizontal scaling with sharding |
| **High Performance** | Optimized for read/write operations |
| **Rich Query Language** | Powerful querying and aggregation |
| **Indexing** | Support for various index types |

### MongoDB Architecture

```
┌─────────────────────────────────────────────────┐
│                  MongoDB Server                  │
├─────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐              │
│  │  Database   │  │  Database   │  ...         │
│  │  (myapp)    │  │  (blog)     │              │
│  ├─────────────┤  ├─────────────┤              │
│  │ Collection  │  │ Collection  │              │
│  │  (users)    │  │  (posts)    │              │
│  ├─────────────┤  ├─────────────┤              │
│  │ Collection  │  │ Collection  │              │
│  │  (products) │  │  (comments) │              │
│  └─────────────┘  └─────────────┘              │
└─────────────────────────────────────────────────┘
```

**Hierarchy:**
- **Server** → Contains multiple databases
- **Database** → Contains multiple collections
- **Collection** → Contains multiple documents
- **Document** → Individual data record (JSON-like)

---

## 3.2 NoSQL vs SQL

### Fundamental Differences

| Aspect | SQL (Relational) | NoSQL (MongoDB) |
|--------|------------------|-----------------|
| **Data Model** | Tables, rows, columns | Collections, documents |
| **Schema** | Fixed, predefined | Flexible, dynamic |
| **Relationships** | JOINs between tables | Embedded documents or references |
| **Scaling** | Vertical (bigger server) | Horizontal (more servers) |
| **Query Language** | SQL | MongoDB Query Language |
| **ACID Transactions** | Full support | Supported (v4.0+) |
| **Best For** | Complex relationships | Flexible, scalable data |

### Data Representation Comparison

**SQL (Relational) Approach:**

```sql
-- Users table
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

-- Addresses table (separate table with foreign key)
CREATE TABLE addresses (
  id INT PRIMARY KEY,
  user_id INT REFERENCES users(id),
  street VARCHAR(200),
  city VARCHAR(100),
  country VARCHAR(100)
);

-- Orders table
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT REFERENCES users(id),
  product_name VARCHAR(100),
  quantity INT,
  price DECIMAL(10,2)
);

-- To get user with addresses and orders: 3 JOINs needed
SELECT * FROM users
JOIN addresses ON users.id = addresses.user_id
JOIN orders ON users.id = orders.user_id
WHERE users.id = 1;
```

**MongoDB (Document) Approach:**

```javascript
// Single document contains everything
{
  "_id": ObjectId("507f1f77bcf86cd799439011"),
  "name": "John Doe",
  "email": "john@example.com",
  "addresses": [
    {
      "street": "123 Main St",
      "city": "New York",
      "country": "USA"
    },
    {
      "street": "456 Oak Ave",
      "city": "Boston",
      "country": "USA"
    }
  ],
  "orders": [
    {
      "productName": "Laptop",
      "quantity": 1,
      "price": 999.99,
      "orderedAt": ISODate("2024-01-10")
    }
  ]
}

// One query retrieves everything
db.users.findOne({ _id: ObjectId("507f1f77bcf86cd799439011") })
```

### When to Use Each

**Choose SQL when:**
- Data has complex relationships
- Data structure is well-defined and stable
- ACID transactions are critical
- Need complex JOINs and aggregations
- Examples: Banking, ERP systems, accounting

**Choose MongoDB when:**
- Data structure may change over time
- Need high write throughput
- Horizontal scaling is required
- Working with hierarchical or nested data
- Rapid development and iteration
- Examples: Content management, real-time analytics, IoT

### The Right Tool for the Job

```
📊 SQL Database Strengths:
   ✅ Complex queries across multiple tables
   ✅ Strong data consistency
   ✅ Well-established, mature ecosystem
   ✅ Standardized query language

📄 MongoDB Strengths:
   ✅ Flexible schema evolution
   ✅ Natural fit for JavaScript/JSON
   ✅ Easy horizontal scaling
   ✅ Faster development iteration
   ✅ Better performance for document-based access
```

---

## 3.3 MongoDB Documents & Collections

### Documents

A **document** is the basic unit of data in MongoDB — similar to a row in SQL, but much more flexible.

**Document Characteristics:**
- JSON-like format (technically BSON - Binary JSON)
- Can contain nested objects and arrays
- Maximum size: 16MB
- Must have a unique `_id` field

```javascript
// Example document
{
  "_id": ObjectId("507f1f77bcf86cd799439011"),  // Unique identifier
  "title": "MongoDB Basics",                     // String
  "author": {                                    // Embedded document
    "name": "Jane Smith",
    "email": "jane@example.com"
  },
  "tags": ["database", "nosql", "mongodb"],      // Array
  "views": 1542,                                 // Number
  "published": true,                             // Boolean
  "publishedAt": ISODate("2024-01-15"),          // Date
  "content": null                                // Null
}
```

### BSON Data Types

MongoDB uses **BSON** (Binary JSON) which supports more data types:

| Type | Example | Description |
|------|---------|-------------|
| **String** | `"Hello"` | UTF-8 string |
| **Number** | `42`, `3.14` | Integer or floating-point |
| **Boolean** | `true`, `false` | True or false |
| **Array** | `[1, 2, 3]` | Ordered list |
| **Object** | `{ key: "value" }` | Embedded document |
| **ObjectId** | `ObjectId("...")` | 12-byte unique ID |
| **Date** | `ISODate("2024-01-15")` | Date and time |
| **Null** | `null` | Null or missing value |
| **Binary** | `BinData(...)` | Binary data |
| **Decimal128** | `NumberDecimal("9.99")` | High-precision decimal |

### ObjectId

The **ObjectId** is MongoDB's default unique identifier:

```javascript
ObjectId("507f1f77bcf86cd799439011")
//        |------||----||----||----|
//        timestamp  machine  pid  counter
//        (4 bytes) (3 bytes)(2b) (3 bytes)
```

**ObjectId contains:**
- **Timestamp**: When the document was created
- **Machine identifier**: Which server created it
- **Process ID**: Which process created it
- **Counter**: Incrementing value for uniqueness

```javascript
// Get timestamp from ObjectId
const id = ObjectId("507f1f77bcf86cd799439011");
id.getTimestamp(); // Returns the creation date
```

### Collections

A **collection** is a group of documents — similar to a table in SQL.

**Collection Characteristics:**
- No enforced schema (documents can differ)
- Created automatically when first document is inserted
- Case-sensitive names
- Cannot contain `$` or null characters

```javascript
// MongoDB shell examples

// Create collection (implicit - when inserting first document)
db.users.insertOne({ name: "John" });

// Create collection (explicit)
db.createCollection("products");

// List all collections
db.getCollectionNames();

// Drop collection
db.users.drop();
```

### Database Operations

```javascript
// Switch to or create a database
use myapp;

// Show current database
db;

// List all databases
show dbs;

// Drop database
db.dropDatabase();

// Database statistics
db.stats();
```

---

## 3.4 MongoDB Compass

### What is MongoDB Compass?

**MongoDB Compass** is the official **GUI (Graphical User Interface)** for MongoDB. It provides a visual way to:

- Browse databases and collections
- Query and analyze documents
- Create and manage indexes
- View query performance
- Build aggregation pipelines
- Validate schemas

### Installing MongoDB Compass

1. Download from: https://www.mongodb.com/try/download/compass
2. Install for your operating system
3. Launch and connect to your MongoDB server

### Connecting to MongoDB

**Local Connection:**
```
mongodb://localhost:27017
```

**MongoDB Atlas (Cloud):**
```
mongodb+srv://username:password@cluster.mongodb.net/myapp
```

### Compass Interface Overview

```
┌─────────────────────────────────────────────────────────┐
│  MongoDB Compass                                        │
├──────────────────┬──────────────────────────────────────┤
│                  │                                      │
│   Databases      │   Collection View                    │
│   ├── myapp      │   ┌──────────────────────────────┐  │
│   │   ├── users  │   │ Documents | Schema | Indexes │  │
│   │   └── posts  │   ├──────────────────────────────┤  │
│   └── admin      │   │ { "_id": ..., "name": ... }  │  │
│                  │   │ { "_id": ..., "name": ... }  │  │
│                  │   │ { "_id": ..., "name": ... }  │  │
│                  │   └──────────────────────────────┘  │
│                  │                                      │
│                  │   Filter: { "age": { "$gt": 18 } }  │
│                  │                                      │
└──────────────────┴──────────────────────────────────────┘
```

### Key Features

#### 1. Document Viewer
- View documents in list, JSON, or table format
- Edit documents directly
- Add new documents
- Delete documents

#### 2. Query Builder
```javascript
// Visual query building
Filter: { "status": "active", "age": { "$gte": 18 } }
Project: { "name": 1, "email": 1 }
Sort: { "createdAt": -1 }
Limit: 10
```

#### 3. Schema Analysis
Compass analyzes your documents and shows:
- Field names and types
- Field frequency
- Value distribution
- Data patterns

#### 4. Index Management
- View existing indexes
- Create new indexes
- Analyze index usage
- Drop unused indexes

#### 5. Aggregation Pipeline Builder
Visual drag-and-drop interface for building aggregation pipelines:
```
[Match] → [Group] → [Sort] → [Limit]
```

### Basic Operations in Compass

**Insert Document:**
1. Select collection
2. Click "Add Data" → "Insert Document"
3. Enter JSON or use form
4. Click "Insert"

**Query Documents:**
1. Enter filter in query bar
2. Add projection, sort, limit as needed
3. Click "Find"

**Update Document:**
1. Find the document
2. Click on it to expand
3. Click "Edit"
4. Modify fields
5. Click "Update"

---

## 3.5 Schema Design Principles

### Schema Design Mindset

Unlike SQL where you design tables first, in MongoDB you design schemas based on:

1. **How will the data be accessed?**
2. **What queries will be most common?**
3. **How often will data be updated vs read?**
4. **What are the relationships between data?**

### The Golden Rule

> **"Store together what is accessed together"**

### Schema Design Patterns

#### Pattern 1: Embedded Documents (Denormalization)

Store related data in the same document:

```javascript
// User with embedded addresses
{
  "_id": ObjectId("..."),
  "name": "John Doe",
  "email": "john@example.com",
  "addresses": [
    {
      "type": "home",
      "street": "123 Main St",
      "city": "New York",
      "zipCode": "10001"
    },
    {
      "type": "work",
      "street": "456 Business Ave",
      "city": "New York",
      "zipCode": "10002"
    }
  ]
}
```

**Advantages:**
- Single query retrieves all data
- Atomic updates (single document)
- Better read performance

**Use when:**
- Data is always accessed together
- Child data doesn't exist independently
- Limited number of embedded items

#### Pattern 2: References (Normalization)

Store references to related documents:

```javascript
// User document
{
  "_id": ObjectId("user123"),
  "name": "John Doe",
  "email": "john@example.com"
}

// Order documents (reference user)
{
  "_id": ObjectId("order1"),
  "userId": ObjectId("user123"),  // Reference
  "product": "Laptop",
  "price": 999.99,
  "orderedAt": ISODate("2024-01-15")
}

{
  "_id": ObjectId("order2"),
  "userId": ObjectId("user123"),  // Reference
  "product": "Mouse",
  "price": 25.99,
  "orderedAt": ISODate("2024-01-16")
}
```

**Advantages:**
- Avoids data duplication
- Documents stay smaller
- Independent updates

**Use when:**
- Related data is accessed independently
- Many-to-many relationships
- Large or unbounded related data

#### Pattern 3: Hybrid Approach

Combine embedding and referencing:

```javascript
// Order with embedded product summary but reference to user
{
  "_id": ObjectId("order123"),
  "userId": ObjectId("user123"),        // Reference
  "userEmail": "john@example.com",       // Duplicated for quick access
  "items": [                             // Embedded
    {
      "productId": ObjectId("prod1"),    // Reference
      "name": "Laptop",                  // Duplicated
      "price": 999.99,                   // Snapshot at order time
      "quantity": 1
    }
  ],
  "total": 999.99,
  "status": "processing"
}
```

### Schema Design Anti-Patterns

#### ❌ Anti-Pattern 1: Unbounded Arrays

```javascript
// BAD: Comments array can grow forever
{
  "_id": ObjectId("post123"),
  "title": "My Blog Post",
  "comments": [
    // Could be millions of comments!
    { "user": "A", "text": "..." },
    { "user": "B", "text": "..." },
    // ... 10,000 more
  ]
}

// GOOD: Separate collection for comments
// Post document
{
  "_id": ObjectId("post123"),
  "title": "My Blog Post",
  "commentCount": 10542
}

// Comments collection
{
  "_id": ObjectId("comment1"),
  "postId": ObjectId("post123"),
  "user": "A",
  "text": "Great post!"
}
```

#### ❌ Anti-Pattern 2: Massive Documents

```javascript
// BAD: Document too large (16MB limit)
{
  "userId": "...",
  "activityLog": [
    // Years of activity data
  ],
  "files": [
    // Large binary data
  ]
}

// GOOD: Separate collections
// User document (small)
{
  "_id": ObjectId("user123"),
  "name": "John"
}

// Activity logs (separate collection with TTL index)
{
  "_id": ObjectId("..."),
  "userId": ObjectId("user123"),
  "action": "login",
  "timestamp": ISODate("...")
}
```

#### ❌ Anti-Pattern 3: Unnecessary Normalization

```javascript
// BAD: Over-normalized (requires many lookups)
// Author collection
{ "_id": ObjectId("author1"), "name": "John" }

// Category collection
{ "_id": ObjectId("cat1"), "name": "Technology" }

// Tag collection
{ "_id": ObjectId("tag1"), "name": "MongoDB" }
{ "_id": ObjectId("tag2"), "name": "Database" }

// Post with all references
{
  "_id": ObjectId("post1"),
  "title": "MongoDB Guide",
  "authorId": ObjectId("author1"),
  "categoryId": ObjectId("cat1"),
  "tagIds": [ObjectId("tag1"), ObjectId("tag2")]
}
// Needs 4+ queries to display a post!

// GOOD: Embed what's needed
{
  "_id": ObjectId("post1"),
  "title": "MongoDB Guide",
  "author": {
    "id": ObjectId("author1"),
    "name": "John"
  },
  "category": "Technology",
  "tags": ["MongoDB", "Database"]
}
// One query gets everything
```

### Design Checklist

Ask these questions when designing schemas:

```
✅ What queries will run most frequently?
✅ What is the read/write ratio?
✅ Will arrays grow unbounded?
✅ Do I need atomic operations on related data?
✅ Will documents exceed 16MB?
✅ How will data be displayed in the application?
✅ What are the relationships between entities?
```

---

## 3.6 Relationships: Embedding vs Referencing

### Types of Relationships

#### One-to-One (1:1)

**Example:** User ↔ Profile

**Option 1: Embed (Preferred)**
```javascript
{
  "_id": ObjectId("user123"),
  "email": "john@example.com",
  "password": "hashedPassword",
  "profile": {
    "firstName": "John",
    "lastName": "Doe",
    "bio": "Software developer",
    "avatar": "https://..."
  }
}
```

**Option 2: Reference (When data is large or rarely accessed)**
```javascript
// User document
{
  "_id": ObjectId("user123"),
  "email": "john@example.com",
  "profileId": ObjectId("profile123")
}

// Profile document
{
  "_id": ObjectId("profile123"),
  "firstName": "John",
  "lastName": "Doe",
  "bio": "Very long bio...",
  "settings": { /* large nested object */ }
}
```

#### One-to-Many (1:N) - Few

**Example:** Blog Post ↔ Comments (limited to 50 comments)

**Embed (Preferred for few items):**
```javascript
{
  "_id": ObjectId("post123"),
  "title": "MongoDB Best Practices",
  "content": "...",
  "comments": [
    {
      "_id": ObjectId("c1"),
      "author": "Alice",
      "text": "Great article!",
      "createdAt": ISODate("2024-01-15")
    },
    {
      "_id": ObjectId("c2"),
      "author": "Bob",
      "text": "Very helpful",
      "createdAt": ISODate("2024-01-16")
    }
  ]
}
```

#### One-to-Many (1:N) - Many

**Example:** User ↔ Orders (potentially thousands)

**Reference (Preferred for many items):**
```javascript
// User document
{
  "_id": ObjectId("user123"),
  "name": "John Doe",
  "email": "john@example.com",
  "orderCount": 156  // Cached count
}

// Orders collection (separate)
{
  "_id": ObjectId("order1"),
  "userId": ObjectId("user123"),
  "items": [...],
  "total": 149.99,
  "status": "delivered",
  "createdAt": ISODate("2024-01-15")
}

{
  "_id": ObjectId("order2"),
  "userId": ObjectId("user123"),
  "items": [...],
  "total": 89.99,
  "status": "processing",
  "createdAt": ISODate("2024-01-20")
}
```

#### Many-to-Many (N:M)

**Example:** Students ↔ Courses

**Option 1: Array of references in both (bidirectional)**
```javascript
// Student document
{
  "_id": ObjectId("student1"),
  "name": "Alice",
  "courseIds": [
    ObjectId("course1"),
    ObjectId("course2")
  ]
}

// Course document
{
  "_id": ObjectId("course1"),
  "title": "MongoDB 101",
  "studentIds": [
    ObjectId("student1"),
    ObjectId("student2")
  ]
}
```

**Option 2: Junction collection (for additional relationship data)**
```javascript
// Students collection
{
  "_id": ObjectId("student1"),
  "name": "Alice"
}

// Courses collection
{
  "_id": ObjectId("course1"),
  "title": "MongoDB 101"
}

// Enrollments collection (junction)
{
  "_id": ObjectId("enroll1"),
  "studentId": ObjectId("student1"),
  "courseId": ObjectId("course1"),
  "enrolledAt": ISODate("2024-01-10"),
  "grade": "A",
  "status": "active"
}
```

### Decision Framework

```
                    ┌─────────────────────────────┐
                    │ Will the related data grow  │
                    │       unbounded?            │
                    └──────────────┬──────────────┘
                                   │
                    ┌──────────────┴──────────────┐
                    │                             │
                   YES                           NO
                    │                             │
                    ▼                             ▼
              ┌─────────┐               ┌─────────────────┐
              │Reference│               │Is data accessed │
              └─────────┘               │   together?     │
                                        └────────┬────────┘
                                                 │
                                    ┌────────────┴───────────┐
                                    │                        │
                                   YES                       NO
                                    │                        │
                                    ▼                        ▼
                               ┌───────┐               ┌─────────┐
                               │ Embed │               │Reference│
                               └───────┘               └─────────┘
```

### Quick Reference Table

| Relationship | Size | Access Pattern | Strategy |
|--------------|------|----------------|----------|
| 1:1 | Small | Together | **Embed** |
| 1:1 | Large | Separate | **Reference** |
| 1:Few | < 100 | Together | **Embed** |
| 1:Many | Unbounded | Separate | **Reference** |
| N:M | Any | Complex | **Reference + Junction** |

---

## 3.7 Mongoose Introduction

### What is Mongoose?

**Mongoose** is an **ODM (Object Document Mapper)** for MongoDB and Node.js.

It provides:
- Schema definitions with validation
- Model-based document creation
- Query building helpers
- Middleware (hooks)
- Population (joining documents)

**Think of Mongoose as the "translator" between your JavaScript code and MongoDB.**

```
┌──────────────────┐      ┌──────────────────┐      ┌──────────────────┐
│   Your Code      │ ──>  │    Mongoose      │ ──>  │    MongoDB       │
│ (JavaScript)     │      │    (ODM)         │      │   (Database)     │
│                  │ <──  │                  │ <──  │                  │
└──────────────────┘      └──────────────────┘      └──────────────────┘
```

### Installing Mongoose

```bash
npm install mongoose
```

### Connecting to MongoDB

```javascript
// db.js
const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    const conn = await mongoose.connect('mongodb://localhost:27017/myapp', {
      // Options (most are now defaults in Mongoose 6+)
    });
    
    console.log(`MongoDB Connected: ${conn.connection.host}`);
  } catch (error) {
    console.error(`Error: ${error.message}`);
    process.exit(1);
  }
};

module.exports = connectDB;
```

```javascript
// server.js
const express = require('express');
const connectDB = require('./db');

const app = express();

// Connect to database
connectDB();

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

### Defining Schemas

A **Schema** defines the structure and validation rules for documents:

```javascript
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  // Basic types
  name: String,
  email: String,
  age: Number,
  isActive: Boolean,
  
  // With options
  username: {
    type: String,
    required: true,
    unique: true,
    lowercase: true,
    trim: true,
    minlength: 3,
    maxlength: 30
  },
  
  password: {
    type: String,
    required: [true, 'Password is required'],
    minlength: [8, 'Password must be at least 8 characters'],
    select: false  // Don't include in queries by default
  },
  
  // Enum
  role: {
    type: String,
    enum: ['user', 'admin', 'moderator'],
    default: 'user'
  },
  
  // Array of strings
  tags: [String],
  
  // Array of objects
  socialLinks: [{
    platform: String,
    url: String
  }],
  
  // Embedded document
  profile: {
    bio: String,
    avatar: String,
    website: String
  },
  
  // Reference to another collection
  posts: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Post'
  }],
  
  // Timestamps
  createdAt: {
    type: Date,
    default: Date.now
  }
});

// Create model from schema
const User = mongoose.model('User', userSchema);

module.exports = User;
```

### Schema Types

| Type | Description | Example |
|------|-------------|---------|
| `String` | Text | `"Hello"` |
| `Number` | Numeric | `42`, `3.14` |
| `Boolean` | True/False | `true` |
| `Date` | Date object | `new Date()` |
| `Buffer` | Binary data | Files, images |
| `ObjectId` | MongoDB ObjectId | Reference to documents |
| `Array` | List | `[1, 2, 3]` |
| `Mixed` | Any type | `Schema.Types.Mixed` |
| `Decimal128` | High precision | Financial data |
| `Map` | Key-value pairs | Dynamic fields |

### Schema Options

```javascript
const postSchema = new mongoose.Schema({
  title: String,
  content: String,
  author: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User'
  }
}, {
  // Schema options
  timestamps: true,              // Adds createdAt and updatedAt
  collection: 'blog_posts',      // Custom collection name
  toJSON: { virtuals: true },    // Include virtuals when converting to JSON
  toObject: { virtuals: true }
});
```

### Validation

```javascript
const productSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Product name is required'],
    trim: true,
    maxlength: [100, 'Name cannot exceed 100 characters']
  },
  
  price: {
    type: Number,
    required: true,
    min: [0, 'Price cannot be negative']
  },
  
  email: {
    type: String,
    match: [/^\S+@\S+\.\S+$/, 'Please enter a valid email']
  },
  
  category: {
    type: String,
    enum: {
      values: ['electronics', 'clothing', 'books'],
      message: '{VALUE} is not a valid category'
    }
  },
  
  // Custom validator
  sku: {
    type: String,
    validate: {
      validator: function(v) {
        return /^[A-Z]{3}-\d{4}$/.test(v);
      },
      message: props => `${props.value} is not a valid SKU format (AAA-0000)`
    }
  }
});
```

### Virtual Properties

Virtuals are document properties that don't persist to MongoDB:

```javascript
const userSchema = new mongoose.Schema({
  firstName: String,
  lastName: String,
  email: String
});

// Virtual property
userSchema.virtual('fullName').get(function() {
  return `${this.firstName} ${this.lastName}`;
});

// Virtual with setter
userSchema.virtual('fullName').set(function(name) {
  const parts = name.split(' ');
  this.firstName = parts[0];
  this.lastName = parts[1];
});

const User = mongoose.model('User', userSchema);

const user = new User({ firstName: 'John', lastName: 'Doe' });
console.log(user.fullName); // "John Doe"
```

### Instance Methods

Methods available on document instances:

```javascript
const userSchema = new mongoose.Schema({
  email: String,
  password: String
});

// Instance method
userSchema.methods.comparePassword = async function(candidatePassword) {
  // 'this' refers to the document
  return await bcrypt.compare(candidatePassword, this.password);
};

userSchema.methods.generateAuthToken = function() {
  return jwt.sign({ id: this._id }, process.env.JWT_SECRET);
};

const User = mongoose.model('User', userSchema);

// Usage
const user = await User.findOne({ email: 'john@example.com' });
const isMatch = await user.comparePassword('password123');
const token = user.generateAuthToken();
```

### Static Methods

Methods available on the Model:

```javascript
const userSchema = new mongoose.Schema({
  email: String,
  role: String
});

// Static method
userSchema.statics.findAdmins = function() {
  return this.find({ role: 'admin' });
};

userSchema.statics.findByEmail = function(email) {
  return this.findOne({ email: email.toLowerCase() });
};

const User = mongoose.model('User', userSchema);

// Usage
const admins = await User.findAdmins();
const user = await User.findByEmail('John@Example.com');
```

### Middleware (Hooks)

Execute code before or after certain operations:

```javascript
const userSchema = new mongoose.Schema({
  email: String,
  password: String,
  passwordChangedAt: Date
});

// Pre-save middleware
userSchema.pre('save', async function(next) {
  // Only hash password if it was modified
  if (!this.isModified('password')) return next();
  
  // Hash password
  this.password = await bcrypt.hash(this.password, 12);
  
  // Set password changed timestamp
  this.passwordChangedAt = Date.now() - 1000;
  
  next();
});

// Post-save middleware
userSchema.post('save', function(doc, next) {
  console.log(`User ${doc.email} was saved`);
  next();
});

// Pre-find middleware (query middleware)
userSchema.pre(/^find/, function(next) {
  // 'this' refers to the query
  this.find({ isActive: { $ne: false } });
  next();
});

// Pre-remove middleware
userSchema.pre('remove', async function(next) {
  // Delete related documents
  await Post.deleteMany({ author: this._id });
  next();
});
```

### Complete Model Example

```javascript
// models/User.js
const mongoose = require('mongoose');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Name is required'],
    trim: true,
    maxlength: [50, 'Name cannot exceed 50 characters']
  },
  email: {
    type: String,
    required: [true, 'Email is required'],
    unique: true,
    lowercase: true,
    match: [/^\S+@\S+\.\S+$/, 'Please enter a valid email']
  },
  password: {
    type: String,
    required: [true, 'Password is required'],
    minlength: [8, 'Password must be at least 8 characters'],
    select: false
  },
  role: {
    type: String,
    enum: ['user', 'admin'],
    default: 'user'
  },
  avatar: {
    type: String,
    default: 'default-avatar.png'
  },
  isActive: {
    type: Boolean,
    default: true
  }
}, {
  timestamps: true,
  toJSON: { virtuals: true },
  toObject: { virtuals: true }
});

// Virtual for user's posts
userSchema.virtual('posts', {
  ref: 'Post',
  localField: '_id',
  foreignField: 'author'
});

// Hash password before saving
userSchema.pre('save', async function(next) {
  if (!this.isModified('password')) return next();
  this.password = await bcrypt.hash(this.password, 12);
  next();
});

// Compare passwords
userSchema.methods.comparePassword = async function(candidatePassword) {
  return await bcrypt.compare(candidatePassword, this.password);
};

// Generate JWT
userSchema.methods.generateToken = function() {
  return jwt.sign(
    { id: this._id, role: this.role },
    process.env.JWT_SECRET,
    { expiresIn: process.env.JWT_EXPIRES_IN }
  );
};

// Find by credentials
userSchema.statics.findByCredentials = async function(email, password) {
  const user = await this.findOne({ email }).select('+password');
  
  if (!user || !(await user.comparePassword(password))) {
    throw new Error('Invalid credentials');
  }
  
  return user;
};

const User = mongoose.model('User', userSchema);

module.exports = User;
```

---

## 📚 Chapter Summary

### What You Learned

✅ MongoDB is a document-based NoSQL database  
✅ Documents are JSON-like objects stored in collections  
✅ NoSQL offers flexibility and scalability advantages  
✅ MongoDB Compass provides visual database management  
✅ Schema design depends on access patterns  
✅ Embed data accessed together; reference data accessed separately  
✅ Mongoose provides schemas, validation, and middleware for MongoDB  

### Key Concepts

```javascript
// MongoDB document structure
{
  "_id": ObjectId("..."),
  "field": "value",
  "nested": { "field": "value" },
  "array": [1, 2, 3]
}

// Mongoose schema definition
const schema = new mongoose.Schema({
  field: { type: String, required: true }
}, { timestamps: true });

// Mongoose model
const Model = mongoose.model('ModelName', schema);
```

### Design Principles

```
1. Store together what is accessed together
2. Avoid unbounded arrays
3. Keep documents under 16MB
4. Design for your queries, not your entities
5. Accept some data duplication for read performance
```

### Next Chapter Preview

In **Chapter 4**, you'll learn:
- Connecting Express to MongoDB
- Creating Mongoose models
- Building CRUD APIs
- Input validation
- Pagination and filtering
- Sorting and error handling

---