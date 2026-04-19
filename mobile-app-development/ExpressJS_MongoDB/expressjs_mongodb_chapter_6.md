# Chapter 6: Advanced Backend Features & Performance

## 🎯 Learning Objectives

By the end of this chapter, you will:
- Implement file uploads with Multer
- Understand background jobs concepts
- Work with MongoDB transactions
- Create and optimize database indexes
- Apply performance optimization techniques
- Implement logging for production
- Document APIs with Swagger/Postman

---

## 6.1 File Uploads

### Introduction to Multer

**Multer** is a Node.js middleware for handling `multipart/form-data`, primarily used for file uploads.

```bash
npm install multer
```

### Basic File Upload Setup

```javascript
// src/middleware/upload.js
const multer = require('multer');
const path = require('path');
const AppError = require('../utils/AppError');

// Storage configuration
const storage = multer.diskStorage({
  destination: function(req, file, cb) {
    cb(null, 'uploads/');
  },
  filename: function(req, file, cb) {
    // Generate unique filename
    const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
    const ext = path.extname(file.originalname);
    cb(null, `${file.fieldname}-${uniqueSuffix}${ext}`);
  }
});

// File filter
const fileFilter = (req, file, cb) => {
  // Allowed file types
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif', 'image/webp'];
  
  if (allowedTypes.includes(file.mimetype)) {
    cb(null, true);
  } else {
    cb(new AppError('Invalid file type. Only JPEG, PNG, GIF, and WebP are allowed.', 400), false);
  }
};

// Create multer instance
const upload = multer({
  storage: storage,
  fileFilter: fileFilter,
  limits: {
    fileSize: 5 * 1024 * 1024  // 5MB max
  }
});

module.exports = upload;
```

### Upload Routes

```javascript
// src/routes/uploadRoutes.js
const express = require('express');
const router = express.Router();
const upload = require('../middleware/upload');
const { protect } = require('../middleware/auth');
const uploadController = require('../controllers/uploadController');

// Single file upload
router.post(
  '/single',
  protect,
  upload.single('image'),
  uploadController.uploadSingle
);

// Multiple files upload (same field)
router.post(
  '/multiple',
  protect,
  upload.array('images', 5),  // Max 5 files
  uploadController.uploadMultiple
);

// Multiple fields with files
router.post(
  '/fields',
  protect,
  upload.fields([
    { name: 'avatar', maxCount: 1 },
    { name: 'gallery', maxCount: 5 }
  ]),
  uploadController.uploadFields
);

module.exports = router;
```

### Upload Controller

```javascript
// src/controllers/uploadController.js
const asyncHandler = require('../utils/asyncHandler');
const AppError = require('../utils/AppError');
const fs = require('fs').promises;
const path = require('path');

// @desc    Upload single file
// @route   POST /api/v1/upload/single
// @access  Private
exports.uploadSingle = asyncHandler(async (req, res, next) => {
  if (!req.file) {
    return next(new AppError('Please upload a file', 400));
  }
  
  res.status(200).json({
    success: true,
    data: {
      filename: req.file.filename,
      originalName: req.file.originalname,
      mimetype: req.file.mimetype,
      size: req.file.size,
      path: `/uploads/${req.file.filename}`
    }
  });
});

// @desc    Upload multiple files
// @route   POST /api/v1/upload/multiple
// @access  Private
exports.uploadMultiple = asyncHandler(async (req, res, next) => {
  if (!req.files || req.files.length === 0) {
    return next(new AppError('Please upload at least one file', 400));
  }
  
  const files = req.files.map(file => ({
    filename: file.filename,
    originalName: file.originalname,
    mimetype: file.mimetype,
    size: file.size,
    path: `/uploads/${file.filename}`
  }));
  
  res.status(200).json({
    success: true,
    count: files.length,
    data: files
  });
});

// @desc    Upload multiple fields
// @route   POST /api/v1/upload/fields
// @access  Private
exports.uploadFields = asyncHandler(async (req, res, next) => {
  const result = {};
  
  if (req.files.avatar && req.files.avatar[0]) {
    result.avatar = {
      filename: req.files.avatar[0].filename,
      path: `/uploads/${req.files.avatar[0].filename}`
    };
  }
  
  if (req.files.gallery && req.files.gallery.length > 0) {
    result.gallery = req.files.gallery.map(file => ({
      filename: file.filename,
      path: `/uploads/${file.filename}`
    }));
  }
  
  res.status(200).json({
    success: true,
    data: result
  });
});

// @desc    Delete file
// @route   DELETE /api/v1/upload/:filename
// @access  Private
exports.deleteFile = asyncHandler(async (req, res, next) => {
  const filePath = path.join(__dirname, '../../uploads', req.params.filename);
  
  try {
    await fs.access(filePath);
    await fs.unlink(filePath);
    
    res.status(200).json({
      success: true,
      message: 'File deleted successfully'
    });
  } catch (error) {
    return next(new AppError('File not found', 404));
  }
});
```

### Memory Storage (For Cloud Upload)

```javascript
// src/middleware/uploadMemory.js
const multer = require('multer');
const AppError = require('../utils/AppError');

const storage = multer.memoryStorage();

const fileFilter = (req, file, cb) => {
  if (file.mimetype.startsWith('image/')) {
    cb(null, true);
  } else {
    cb(new AppError('Only images are allowed', 400), false);
  }
};

const upload = multer({
  storage: storage,
  fileFilter: fileFilter,
  limits: {
    fileSize: 5 * 1024 * 1024
  }
});

module.exports = upload;
```

### Image Processing with Sharp

```bash
npm install sharp
```

```javascript
// src/middleware/imageProcessor.js
const sharp = require('sharp');
const path = require('path');
const asyncHandler = require('../utils/asyncHandler');

exports.resizeUserPhoto = asyncHandler(async (req, res, next) => {
  if (!req.file) return next();
  
  // Generate filename
  req.file.filename = `user-${req.user.id}-${Date.now()}.jpeg`;
  
  // Process image
  await sharp(req.file.buffer)
    .resize(500, 500)          // Resize to 500x500
    .toFormat('jpeg')          // Convert to JPEG
    .jpeg({ quality: 90 })     // Set quality
    .toFile(`uploads/users/${req.file.filename}`);
  
  next();
});

exports.resizeProductImages = asyncHandler(async (req, res, next) => {
  if (!req.files || !req.files.images) return next();
  
  req.body.images = [];
  
  await Promise.all(
    req.files.images.map(async (file, index) => {
      const filename = `product-${req.params.id}-${Date.now()}-${index + 1}.jpeg`;
      
      await sharp(file.buffer)
        .resize(800, 800)
        .toFormat('jpeg')
        .jpeg({ quality: 85 })
        .toFile(`uploads/products/${filename}`);
      
      req.body.images.push(filename);
    })
  );
  
  next();
});
```

### Cloud Storage (AWS S3)

```bash
npm install @aws-sdk/client-s3 @aws-sdk/s3-request-presigner
```

```javascript
// src/utils/s3.js
const { S3Client, PutObjectCommand, DeleteObjectCommand, GetObjectCommand } = require('@aws-sdk/client-s3');
const { getSignedUrl } = require('@aws-sdk/s3-request-presigner');

const s3Client = new S3Client({
  region: process.env.AWS_REGION,
  credentials: {
    accessKeyId: process.env.AWS_ACCESS_KEY_ID,
    secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY
  }
});

// Upload file to S3
exports.uploadToS3 = async (file, folder = 'uploads') => {
  const key = `${folder}/${Date.now()}-${file.originalname}`;
  
  const command = new PutObjectCommand({
    Bucket: process.env.AWS_BUCKET_NAME,
    Key: key,
    Body: file.buffer,
    ContentType: file.mimetype,
    ACL: 'public-read'
  });
  
  await s3Client.send(command);
  
  return {
    key,
    url: `https://${process.env.AWS_BUCKET_NAME}.s3.${process.env.AWS_REGION}.amazonaws.com/${key}`
  };
};

// Delete file from S3
exports.deleteFromS3 = async (key) => {
  const command = new DeleteObjectCommand({
    Bucket: process.env.AWS_BUCKET_NAME,
    Key: key
  });
  
  await s3Client.send(command);
};

// Generate signed URL for private files
exports.getSignedUrl = async (key, expiresIn = 3600) => {
  const command = new GetObjectCommand({
    Bucket: process.env.AWS_BUCKET_NAME,
    Key: key
  });
  
  return await getSignedUrl(s3Client, command, { expiresIn });
};
```

### Upload to S3 Controller

```javascript
// src/controllers/uploadController.js
const { uploadToS3, deleteFromS3 } = require('../utils/s3');
const asyncHandler = require('../utils/asyncHandler');
const AppError = require('../utils/AppError');

exports.uploadToCloud = asyncHandler(async (req, res, next) => {
  if (!req.file) {
    return next(new AppError('Please upload a file', 400));
  }
  
  const result = await uploadToS3(req.file, 'images');
  
  res.status(200).json({
    success: true,
    data: {
      key: result.key,
      url: result.url
    }
  });
});
```

---

## 6.2 Background Jobs (Conceptual)

### Why Background Jobs?

Some tasks shouldn't block the main request:
- Sending emails
- Processing images
- Generating reports
- Data synchronization
- Scheduled tasks

### Job Queue Architecture

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Express API   │────>│   Job Queue     │────>│   Worker        │
│                 │     │   (Redis)       │     │   Process       │
└─────────────────┘     └─────────────────┘     └─────────────────┘
        │                       │                       │
        │ 1. Create job         │ 2. Store job          │ 3. Process job
        │                       │                       │
        ▼                       ▼                       ▼
   Immediate                Persistent              Background
   Response                 Storage                 Execution
```

### Using Bull Queue

```bash
npm install bull
```

```javascript
// src/jobs/emailQueue.js
const Queue = require('bull');

// Create queue
const emailQueue = new Queue('email', {
  redis: {
    host: process.env.REDIS_HOST || 'localhost',
    port: process.env.REDIS_PORT || 6379
  }
});

// Process jobs
emailQueue.process(async (job) => {
  const { to, subject, template, data } = job.data;
  
  // Send email logic here
  console.log(`Sending email to ${to}: ${subject}`);
  
  // Simulate email sending
  await new Promise(resolve => setTimeout(resolve, 1000));
  
  return { sent: true, to };
});

// Event listeners
emailQueue.on('completed', (job, result) => {
  console.log(`Job ${job.id} completed:`, result);
});

emailQueue.on('failed', (job, err) => {
  console.error(`Job ${job.id} failed:`, err.message);
});

// Add job to queue
const addEmailJob = async (emailData) => {
  const job = await emailQueue.add(emailData, {
    attempts: 3,
    backoff: {
      type: 'exponential',
      delay: 2000
    }
  });
  return job;
};

module.exports = { emailQueue, addEmailJob };
```

### Using Jobs in Controllers

```javascript
// src/controllers/authController.js
const { addEmailJob } = require('../jobs/emailQueue');

exports.forgotPassword = asyncHandler(async (req, res, next) => {
  // ... generate reset token
  
  // Add email job to queue (non-blocking)
  await addEmailJob({
    to: user.email,
    subject: 'Password Reset',
    template: 'passwordReset',
    data: {
      name: user.name,
      resetURL
    }
  });
  
  // Respond immediately
  res.status(200).json({
    success: true,
    message: 'Password reset email sent'
  });
});
```

### Scheduled Jobs with Node-Cron

```bash
npm install node-cron
```

```javascript
// src/jobs/scheduler.js
const cron = require('node-cron');
const User = require('../models/User');
const Order = require('../models/Order');

// Run every day at midnight
cron.schedule('0 0 * * *', async () => {
  console.log('Running daily cleanup...');
  
  try {
    // Delete unverified accounts older than 7 days
    const sevenDaysAgo = new Date(Date.now() - 7 * 24 * 60 * 60 * 1000);
    
    const result = await User.deleteMany({
      isVerified: false,
      createdAt: { $lt: sevenDaysAgo }
    });
    
    console.log(`Deleted ${result.deletedCount} unverified accounts`);
  } catch (error) {
    console.error('Daily cleanup failed:', error);
  }
});

// Run every hour
cron.schedule('0 * * * *', async () => {
  console.log('Checking for abandoned carts...');
  
  try {
    const oneHourAgo = new Date(Date.now() - 60 * 60 * 1000);
    
    const abandonedOrders = await Order.find({
      status: 'pending',
      updatedAt: { $lt: oneHourAgo }
    });
    
    // Send reminder emails
    for (const order of abandonedOrders) {
      // await sendCartReminderEmail(order);
    }
    
    console.log(`Processed ${abandonedOrders.length} abandoned carts`);
  } catch (error) {
    console.error('Abandoned cart check failed:', error);
  }
});

// Run every Monday at 9 AM
cron.schedule('0 9 * * 1', async () => {
  console.log('Generating weekly report...');
  // Generate and send weekly report
});

module.exports = { startScheduler: () => console.log('Scheduler started') };
```

### Simple In-Memory Queue (For Small Apps)

```javascript
// src/utils/simpleQueue.js
class SimpleQueue {
  constructor() {
    this.queue = [];
    this.processing = false;
  }
  
  async add(job) {
    this.queue.push(job);
    this.process();
  }
  
  async process() {
    if (this.processing || this.queue.length === 0) return;
    
    this.processing = true;
    
    while (this.queue.length > 0) {
      const job = this.queue.shift();
      
      try {
        await job.handler(job.data);
        console.log(`Job completed: ${job.name}`);
      } catch (error) {
        console.error(`Job failed: ${job.name}`, error);
      }
    }
    
    this.processing = false;
  }
}

const queue = new SimpleQueue();

// Usage
queue.add({
  name: 'sendEmail',
  data: { to: 'user@example.com', subject: 'Welcome' },
  handler: async (data) => {
    // Send email
    await sendEmail(data);
  }
});

module.exports = queue;
```

---

## 6.3 Transactions

### What are Transactions?

Transactions ensure that multiple database operations either **all succeed** or **all fail** together (atomicity).

**Example:** Transferring money between accounts:
1. Deduct from sender
2. Add to receiver

Both must succeed, or neither should happen.

### MongoDB Transactions

```javascript
// src/utils/transaction.js
const mongoose = require('mongoose');

// Transaction wrapper
const withTransaction = async (operations) => {
  const session = await mongoose.startSession();
  session.startTransaction();
  
  try {
    const result = await operations(session);
    await session.commitTransaction();
    return result;
  } catch (error) {
    await session.abortTransaction();
    throw error;
  } finally {
    session.endSession();
  }
};

module.exports = { withTransaction };
```

### Transaction Example: Order Processing

```javascript
// src/controllers/orderController.js
const mongoose = require('mongoose');
const Order = require('../models/Order');
const Product = require('../models/Product');
const User = require('../models/User');
const asyncHandler = require('../utils/asyncHandler');
const AppError = require('../utils/AppError');

// @desc    Create order with transaction
// @route   POST /api/v1/orders
// @access  Private
exports.createOrder = asyncHandler(async (req, res, next) => {
  const { items, shippingAddress, paymentMethod } = req.body;
  
  // Start session for transaction
  const session = await mongoose.startSession();
  session.startTransaction();
  
  try {
    // 1. Validate and calculate order
    let totalAmount = 0;
    const orderItems = [];
    
    for (const item of items) {
      const product = await Product.findById(item.product).session(session);
      
      if (!product) {
        throw new AppError(`Product not found: ${item.product}`, 404);
      }
      
      if (product.stock < item.quantity) {
        throw new AppError(
          `Insufficient stock for ${product.name}. Available: ${product.stock}`,
          400
        );
      }
      
      // Calculate item total
      const itemTotal = product.price * item.quantity;
      totalAmount += itemTotal;
      
      orderItems.push({
        product: product._id,
        name: product.name,
        price: product.price,
        quantity: item.quantity
      });
      
      // 2. Update product stock
      product.stock -= item.quantity;
      await product.save({ session });
    }
    
    // 3. Create order
    const order = await Order.create([{
      user: req.user._id,
      items: orderItems,
      totalAmount,
      shippingAddress,
      paymentMethod,
      status: 'pending'
    }], { session });
    
    // 4. Update user's order history
    await User.findByIdAndUpdate(
      req.user._id,
      { 
        $push: { orders: order[0]._id },
        $inc: { totalSpent: totalAmount }
      },
      { session }
    );
    
    // Commit transaction
    await session.commitTransaction();
    
    res.status(201).json({
      success: true,
      data: order[0]
    });
    
  } catch (error) {
    // Abort transaction on error
    await session.abortTransaction();
    return next(error);
  } finally {
    session.endSession();
  }
});
```

### Transaction Example: Money Transfer

```javascript
// src/controllers/walletController.js
const mongoose = require('mongoose');
const Wallet = require('../models/Wallet');
const Transaction = require('../models/Transaction');
const asyncHandler = require('../utils/asyncHandler');
const AppError = require('../utils/AppError');

// @desc    Transfer money between wallets
// @route   POST /api/v1/wallet/transfer
// @access  Private
exports.transferMoney = asyncHandler(async (req, res, next) => {
  const { recipientId, amount, description } = req.body;
  const senderId = req.user._id;
  
  if (amount <= 0) {
    return next(new AppError('Amount must be positive', 400));
  }
  
  if (senderId.toString() === recipientId) {
    return next(new AppError('Cannot transfer to yourself', 400));
  }
  
  const session = await mongoose.startSession();
  session.startTransaction();
  
  try {
    // 1. Get sender wallet
    const senderWallet = await Wallet.findOne({ user: senderId }).session(session);
    
    if (!senderWallet) {
      throw new AppError('Sender wallet not found', 404);
    }
    
    if (senderWallet.balance < amount) {
      throw new AppError('Insufficient balance', 400);
    }
    
    // 2. Get recipient wallet
    const recipientWallet = await Wallet.findOne({ user: recipientId }).session(session);
    
    if (!recipientWallet) {
      throw new AppError('Recipient wallet not found', 404);
    }
    
    // 3. Deduct from sender
    senderWallet.balance -= amount;
    await senderWallet.save({ session });
    
    // 4. Add to recipient
    recipientWallet.balance += amount;
    await recipientWallet.save({ session });
    
    // 5. Create transaction record
    const transaction = await Transaction.create([{
      sender: senderId,
      recipient: recipientId,
      amount,
      description,
      type: 'transfer',
      status: 'completed'
    }], { session });
    
    // Commit
    await session.commitTransaction();
    
    res.status(200).json({
      success: true,
      message: 'Transfer successful',
      data: {
        transactionId: transaction[0]._id,
        newBalance: senderWallet.balance
      }
    });
    
  } catch (error) {
    await session.abortTransaction();
    return next(error);
  } finally {
    session.endSession();
  }
});
```

### Reusable Transaction Wrapper

```javascript
// src/utils/withTransaction.js
const mongoose = require('mongoose');

const withTransaction = async (callback) => {
  const session = await mongoose.startSession();
  
  try {
    let result;
    
    await session.withTransaction(async () => {
      result = await callback(session);
    });
    
    return result;
  } finally {
    session.endSession();
  }
};

module.exports = withTransaction;

// Usage
const withTransaction = require('../utils/withTransaction');

exports.createOrder = asyncHandler(async (req, res, next) => {
  const order = await withTransaction(async (session) => {
    // All operations here use the same session
    const product = await Product.findById(productId).session(session);
    product.stock -= quantity;
    await product.save({ session });
    
    const order = await Order.create([orderData], { session });
    return order[0];
  });
  
  res.status(201).json({ success: true, data: order });
});
```

---

## 6.4 Indexing

### What are Indexes?

Indexes are special data structures that improve query performance by allowing MongoDB to find documents without scanning every document.

**Without index:** Scan 1,000,000 documents → Slow  
**With index:** Jump directly to matching documents → Fast

### Creating Indexes

```javascript
// In Mongoose Schema
const productSchema = new mongoose.Schema({
  name: String,
  price: Number,
  category: String,
  createdAt: Date
});

// Single field index
productSchema.index({ category: 1 });  // 1 = ascending, -1 = descending

// Compound index
productSchema.index({ category: 1, price: -1 });

// Unique index
productSchema.index({ slug: 1 }, { unique: true });

// Text index for search
productSchema.index({ name: 'text', description: 'text' });

// TTL index (auto-delete after time)
productSchema.index({ expiresAt: 1 }, { expireAfterSeconds: 0 });

// Sparse index (only index documents with the field)
productSchema.index({ optionalField: 1 }, { sparse: true });

// Partial index (index only matching documents)
productSchema.index(
  { status: 1 },
  { partialFilterExpression: { status: 'active' } }
);
```

### Index Strategies

```javascript
// src/models/Product.js
const productSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true
  },
  slug: {
    type: String,
    unique: true
  },
  description: String,
  price: {
    type: Number,
    required: true
  },
  category: {
    type: String,
    required: true
  },
  brand: String,
  tags: [String],
  ratings: {
    average: Number,
    count: Number
  },
  isActive: {
    type: Boolean,
    default: true
  },
  createdAt: {
    type: Date,
    default: Date.now
  }
});

// Indexes based on common query patterns

// 1. Find by slug (unique lookups)
productSchema.index({ slug: 1 }, { unique: true });

// 2. Filter by category and sort by price (common filter + sort)
productSchema.index({ category: 1, price: 1 });

// 3. Filter active products by category (partial index)
productSchema.index(
  { category: 1, createdAt: -1 },
  { 
    partialFilterExpression: { isActive: true },
    name: 'active_products_by_category'
  }
);

// 4. Text search
productSchema.index(
  { name: 'text', description: 'text', tags: 'text' },
  { 
    weights: { name: 10, tags: 5, description: 1 },
    name: 'product_text_search'
  }
);

// 5. Filter by tags (multikey index)
productSchema.index({ tags: 1 });

// 6. Sort by rating
productSchema.index({ 'ratings.average': -1, 'ratings.count': -1 });
```

### Analyzing Query Performance

```javascript
// MongoDB Shell - Explain query
db.products.find({ category: 'electronics' }).explain('executionStats')

// In Mongoose
const result = await Product.find({ category: 'electronics' })
  .explain('executionStats');

console.log({
  executionTimeMillis: result.executionStats.executionTimeMillis,
  totalDocsExamined: result.executionStats.totalDocsExamined,
  totalKeysExamined: result.executionStats.totalKeysExamined,
  indexUsed: result.queryPlanner.winningPlan.inputStage.indexName
});
```

### Index Management Script

```javascript
// scripts/manageIndexes.js
const mongoose = require('mongoose');
const Product = require('../src/models/Product');
const User = require('../src/models/User');
const Order = require('../src/models/Order');

const manageIndexes = async () => {
  try {
    await mongoose.connect(process.env.MONGODB_URI);
    console.log('Connected to MongoDB');
    
    // List all indexes
    console.log('\n--- Current Indexes ---');
    
    const productIndexes = await Product.collection.indexes();
    console.log('Product indexes:', productIndexes);
    
    const userIndexes = await User.collection.indexes();
    console.log('User indexes:', userIndexes);
    
    // Create indexes (if not exist)
    console.log('\n--- Creating Indexes ---');
    
    await Product.syncIndexes();
    console.log('Product indexes synced');
    
    await User.syncIndexes();
    console.log('User indexes synced');
    
    // Get index stats
    console.log('\n--- Index Statistics ---');
    
    const stats = await Product.collection.aggregate([
      { $indexStats: {} }
    ]).toArray();
    
    stats.forEach(stat => {
      console.log(`Index: ${stat.name}`);
      console.log(`  Accesses: ${stat.accesses.ops}`);
      console.log(`  Since: ${stat.accesses.since}`);
    });
    
  } catch (error) {
    console.error('Error:', error);
  } finally {
    await mongoose.disconnect();
  }
};

manageIndexes();
```

### Index Best Practices

```javascript
// DO: Create compound indexes for common filter + sort combinations
productSchema.index({ category: 1, price: -1 });
// Supports: find({ category: 'x' }).sort({ price: -1 })

// DO: Put equality filters before range filters
productSchema.index({ status: 1, price: 1 });
// Good for: find({ status: 'active', price: { $gte: 100 } })

// DO: Use partial indexes to reduce index size
productSchema.index(
  { email: 1 },
  { partialFilterExpression: { isVerified: true } }
);

// DON'T: Over-index (each index has storage and write overhead)
// DON'T: Create indexes you won't use

// DO: Monitor index usage and remove unused indexes
const unusedIndexes = await collection.aggregate([
  { $indexStats: {} },
  { $match: { 'accesses.ops': 0 } }
]).toArray();
```

---

## 6.5 Performance Optimization

### Query Optimization

```javascript
// src/utils/queryOptimizer.js

// 1. Use lean() for read-only queries
const products = await Product.find({ category: 'electronics' }).lean();
// Returns plain JS objects instead of Mongoose documents (faster)

// 2. Select only needed fields
const products = await Product.find()
  .select('name price category')  // Only these fields
  .lean();

// 3. Use pagination
const page = parseInt(req.query.page) || 1;
const limit = parseInt(req.query.limit) || 20;
const skip = (page - 1) * limit;

const products = await Product.find()
  .skip(skip)
  .limit(limit)
  .lean();

// 4. Use cursor for large datasets
const cursor = Product.find().cursor();

for await (const product of cursor) {
  // Process one at a time
  await processProduct(product);
}

// 5. Batch operations
const bulkOps = products.map(product => ({
  updateOne: {
    filter: { _id: product._id },
    update: { $set: { processed: true } }
  }
}));

await Product.bulkWrite(bulkOps);
```

### Caching with Redis

```bash
npm install ioredis
```

```javascript
// src/config/redis.js
const Redis = require('ioredis');

const redis = new Redis({
  host: process.env.REDIS_HOST || 'localhost',
  port: process.env.REDIS_PORT || 6379,
  password: process.env.REDIS_PASSWORD
});

redis.on('connect', () => console.log('Redis connected'));
redis.on('error', (err) => console.error('Redis error:', err));

module.exports = redis;
```

```javascript
// src/middleware/cache.js
const redis = require('../config/redis');

const cache = (duration = 300) => {
  return async (req, res, next) => {
    // Skip cache for non-GET requests
    if (req.method !== 'GET') return next();
    
    const key = `cache:${req.originalUrl}`;
    
    try {
      const cached = await redis.get(key);
      
      if (cached) {
        return res.json(JSON.parse(cached));
      }
      
      // Store original json method
      const originalJson = res.json.bind(res);
      
      // Override json method to cache response
      res.json = async (data) => {
        await redis.setex(key, duration, JSON.stringify(data));
        return originalJson(data);
      };
      
      next();
    } catch (error) {
      console.error('Cache error:', error);
      next();
    }
  };
};

// Clear cache helper
const clearCache = async (pattern) => {
  const keys = await redis.keys(`cache:${pattern}`);
  if (keys.length > 0) {
    await redis.del(...keys);
  }
};

module.exports = { cache, clearCache };
```

### Using Cache

```javascript
// src/routes/productRoutes.js
const { cache, clearCache } = require('../middleware/cache');

// Cache product list for 5 minutes
router.get('/', cache(300), productController.getAllProducts);

// Cache individual products for 10 minutes
router.get('/:id', cache(600), productController.getProduct);

// Clear cache when products change
router.post('/', async (req, res, next) => {
  await productController.createProduct(req, res, next);
  await clearCache('/api/v1/products*');
});
```

### Database Connection Pooling

```javascript
// src/config/db.js
const mongoose = require('mongoose');

const connectDB = async () => {
  const conn = await mongoose.connect(process.env.MONGODB_URI, {
    // Connection pool settings
    maxPoolSize: 10,      // Maximum connections
    minPoolSize: 2,       // Minimum connections
    maxIdleTimeMS: 30000, // Close idle connections after 30s
    
    // Timeouts
    serverSelectionTimeoutMS: 5000,
    socketTimeoutMS: 45000,
    
    // Retry settings
    retryWrites: true,
    retryReads: true
  });
  
  console.log(`MongoDB Connected: ${conn.connection.host}`);
};

module.exports = connectDB;
```

### Response Compression

```bash
npm install compression
```

```javascript
// src/app.js
const compression = require('compression');

// Compress all responses
app.use(compression({
  level: 6,  // Compression level (0-9)
  threshold: 1024,  // Only compress responses > 1KB
  filter: (req, res) => {
    if (req.headers['x-no-compression']) {
      return false;
    }
    return compression.filter(req, res);
  }
}));
```

### Payload Size Optimization

```javascript
// 1. Paginate responses
exports.getProducts = asyncHandler(async (req, res) => {
  const page = parseInt(req.query.page) || 1;
  const limit = Math.min(parseInt(req.query.limit) || 20, 100);
  
  const products = await Product.find()
    .select('name price category thumbnail')  // Minimal fields
    .skip((page - 1) * limit)
    .limit(limit)
    .lean();
  
  res.json({
    success: true,
    page,
    limit,
    data: products
  });
});

// 2. Use field projection in aggregations
const stats = await Order.aggregate([
  { $match: { status: 'completed' } },
  { 
    $group: {
      _id: '$category',
      count: { $sum: 1 },
      total: { $sum: '$amount' }
    }
  },
  {
    $project: {
      _id: 0,
      category: '$_id',
      count: 1,
      total: 1
    }
  }
]);
```

---

## 6.6 Logging

### Winston Logger Setup

```bash
npm install winston winston-daily-rotate-file
```

```javascript
// src/utils/logger.js
const winston = require('winston');
const DailyRotateFile = require('winston-daily-rotate-file');
const path = require('path');

const logDir = 'logs';

// Custom format
const logFormat = winston.format.combine(
  winston.format.timestamp({ format: 'YYYY-MM-DD HH:mm:ss' }),
  winston.format.errors({ stack: true }),
  winston.format.printf(({ timestamp, level, message, stack, ...meta }) => {
    let log = `${timestamp} [${level.toUpperCase()}]: ${message}`;
    
    if (Object.keys(meta).length > 0) {
      log += ` ${JSON.stringify(meta)}`;
    }
    
    if (stack) {
      log += `\n${stack}`;
    }
    
    return log;
  })
);

// Create logger
const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: logFormat,
  transports: [
    // Error logs
    new DailyRotateFile({
      filename: path.join(logDir, 'error-%DATE%.log'),
      datePattern: 'YYYY-MM-DD',
      level: 'error',
      maxFiles: '30d',
      maxSize: '20m'
    }),
    
    // Combined logs
    new DailyRotateFile({
      filename: path.join(logDir, 'combined-%DATE%.log'),
      datePattern: 'YYYY-MM-DD',
      maxFiles: '14d',
      maxSize: '20m'
    })
  ]
});

// Console logging for development
if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.combine(
      winston.format.colorize(),
      logFormat
    )
  }));
}

module.exports = logger;
```

### Request Logging Middleware

```javascript
// src/middleware/requestLogger.js
const logger = require('../utils/logger');

const requestLogger = (req, res, next) => {
  const startTime = Date.now();
  
  // Log request
  logger.info('Incoming request', {
    method: req.method,
    url: req.originalUrl,
    ip: req.ip,
    userAgent: req.get('user-agent'),
    userId: req.user?.id
  });
  
  // Log response when finished
  res.on('finish', () => {
    const duration = Date.now() - startTime;
    
    const logData = {
      method: req.method,
      url: req.originalUrl,
      status: res.statusCode,
      duration: `${duration}ms`,
      userId: req.user?.id
    };
    
    if (res.statusCode >= 400) {
      logger.warn('Request completed with error', logData);
    } else {
      logger.info('Request completed', logData);
    }
  });
  
  next();
};

module.exports = requestLogger;
```

### Error Logging

```javascript
// src/middleware/errorHandler.js
const logger = require('../utils/logger');

module.exports = (err, req, res, next) => {
  // Log error
  logger.error('Error occurred', {
    message: err.message,
    stack: err.stack,
    method: req.method,
    url: req.originalUrl,
    userId: req.user?.id,
    body: req.body,
    params: req.params
  });
  
  // Send response
  res.status(err.statusCode || 500).json({
    success: false,
    error: {
      message: process.env.NODE_ENV === 'production' 
        ? 'Something went wrong' 
        : err.message
    }
  });
};
```

### Logging in Application

```javascript
const logger = require('../utils/logger');

// Info logs
logger.info('Server started', { port: 3000 });
logger.info('User registered', { userId: user._id, email: user.email });

// Warning logs
logger.warn('Rate limit approaching', { ip: req.ip, count: requestCount });
logger.warn('Deprecated API used', { endpoint: req.path });

// Error logs
logger.error('Database connection failed', { error: err.message });
logger.error('Payment processing failed', { 
  orderId: order._id, 
  error: err.message 
});

// Debug logs (only shown in development)
logger.debug('Query executed', { query, duration: '45ms' });
```

---

## 6.7 API Documentation

### Swagger Setup

```bash
npm install swagger-jsdoc swagger-ui-express
```

```javascript
// src/config/swagger.js
const swaggerJsDoc = require('swagger-jsdoc');
const swaggerUi = require('swagger-ui-express');

const options = {
  definition: {
    openapi: '3.0.0',
    info: {
      title: 'My API',
      version: '1.0.0',
      description: 'API Documentation',
      contact: {
        name: 'API Support',
        email: 'support@example.com'
      }
    },
    servers: [
      {
        url: 'http://localhost:3000/api/v1',
        description: 'Development server'
      },
      {
        url: 'https://api.myapp.com/v1',
        description: 'Production server'
      }
    ],
    components: {
      securitySchemes: {
        bearerAuth: {
          type: 'http',
          scheme: 'bearer',
          bearerFormat: 'JWT'
        }
      }
    },
    security: [{ bearerAuth: [] }]
  },
  apis: ['./src/routes/*.js', './src/models/*.js']
};

const specs = swaggerJsDoc(options);

const setupSwagger = (app) => {
  app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(specs, {
    explorer: true,
    customCss: '.swagger-ui .topbar { display: none }'
  }));
  
  // JSON endpoint for specs
  app.get('/api-docs.json', (req, res) => {
    res.json(specs);
  });
};

module.exports = setupSwagger;
```

### Documenting Routes

```javascript
// src/routes/productRoutes.js

/**
 * @swagger
 * components:
 *   schemas:
 *     Product:
 *       type: object
 *       required:
 *         - name
 *         - price
 *         - category
 *       properties:
 *         _id:
 *           type: string
 *           description: Auto-generated MongoDB ID
 *         name:
 *           type: string
 *           description: Product name
 *         price:
 *           type: number
 *           description: Product price
 *         category:
 *           type: string
 *           enum: [electronics, clothing, books, home, sports, other]
 *         description:
 *           type: string
 *         stock:
 *           type: integer
 *           default: 0
 *         createdAt:
 *           type: string
 *           format: date-time
 *       example:
 *         _id: 507f1f77bcf86cd799439011
 *         name: Wireless Headphones
 *         price: 149.99
 *         category: electronics
 *         stock: 50
 */

/**
 * @swagger
 * /products:
 *   get:
 *     summary: Get all products
 *     tags: [Products]
 *     parameters:
 *       - in: query
 *         name: page
 *         schema:
 *           type: integer
 *           default: 1
 *         description: Page number
 *       - in: query
 *         name: limit
 *         schema:
 *           type: integer
 *           default: 10
 *         description: Items per page
 *       - in: query
 *         name: category
 *         schema:
 *           type: string
 *         description: Filter by category
 *       - in: query
 *         name: sort
 *         schema:
 *           type: string
 *         description: Sort field (e.g., -price, name)
 *     responses:
 *       200:
 *         description: List of products
 *         content:
 *           application/json:
 *             schema:
 *               type: object
 *               properties:
 *                 success:
 *                   type: boolean
 *                 count:
 *                   type: integer
 *                 pagination:
 *                   type: object
 *                 data:
 *                   type: array
 *                   items:
 *                     $ref: '#/components/schemas/Product'
 */
router.get('/', productController.getAllProducts);

/**
 * @swagger
 * /products/{id}:
 *   get:
 *     summary: Get product by ID
 *     tags: [Products]
 *     parameters:
 *       - in: path
 *         name: id
 *         required: true
 *         schema:
 *           type: string
 *         description: Product ID
 *     responses:
 *       200:
 *         description: Product details
 *         content:
 *           application/json:
 *             schema:
 *               type: object
 *               properties:
 *                 success:
 *                   type: boolean
 *                 data:
 *                   $ref: '#/components/schemas/Product'
 *       404:
 *         description: Product not found
 */
router.get('/:id', productController.getProduct);

/**
 * @swagger
 * /products:
 *   post:
 *     summary: Create a new product
 *     tags: [Products]
 *     security:
 *       - bearerAuth: []
 *     requestBody:
 *       required: true
 *       content:
 *         application/json:
 *           schema:
 *             type: object
 *             required:
 *               - name
 *               - price
 *               - category
 *             properties:
 *               name:
 *                 type: string
 *               price:
 *                 type: number
 *               category:
 *                 type: string
 *               description:
 *                 type: string
 *               stock:
 *                 type: integer
 *     responses:
 *       201:
 *         description: Product created successfully
 *       400:
 *         description: Validation error
 *       401:
 *         description: Not authenticated
 */
router.post('/', protect, productController.createProduct);

/**
 * @swagger
 * /products/{id}:
 *   put:
 *     summary: Update a product
 *     tags: [Products]
 *     security:
 *       - bearerAuth: []
 *     parameters:
 *       - in: path
 *         name: id
 *         required: true
 *         schema:
 *           type: string
 *     requestBody:
 *       required: true
 *       content:
 *         application/json:
 *           schema:
 *             $ref: '#/components/schemas/Product'
 *     responses:
 *       200:
 *         description: Product updated
 *       404:
 *         description: Product not found
 */
router.put('/:id', protect, productController.updateProduct);

/**
 * @swagger
 * /products/{id}:
 *   delete:
 *     summary: Delete a product
 *     tags: [Products]
 *     security:
 *       - bearerAuth: []
 *     parameters:
 *       - in: path
 *         name: id
 *         required: true
 *         schema:
 *           type: string
 *     responses:
 *       200:
 *         description: Product deleted
 *       404:
 *         description: Product not found
 */
router.delete('/:id', protect, restrictTo('admin'), productController.deleteProduct);
```

### Setup in App

```javascript
// src/app.js
const setupSwagger = require('./config/swagger');

// Setup Swagger docs
if (process.env.NODE_ENV !== 'production') {
  setupSwagger(app);
  console.log('API docs available at /api-docs');
}
```

---

## 📚 Chapter Summary

### What You Learned

✅ File uploads with Multer and cloud storage  
✅ Background job concepts with queues  
✅ MongoDB transactions for atomic operations  
✅ Database indexing for performance  
✅ Query optimization techniques  
✅ Caching with Redis  
✅ Production-ready logging  
✅ API documentation with Swagger  

### Key Concepts

```javascript
// File Upload
const upload = multer({ storage, fileFilter, limits });
router.post('/upload', upload.single('image'), handler);

// Transaction
const session = await mongoose.startSession();
session.startTransaction();
try {
  await Model.create([data], { session });
  await session.commitTransaction();
} catch (error) {
  await session.abortTransaction();
}

// Indexing
schema.index({ field: 1 });
schema.index({ field1: 1, field2: -1 });

// Caching
await redis.setex(key, duration, JSON.stringify(data));
const cached = await redis.get(key);

// Logging
logger.info('Message', { metadata });
logger.error('Error', { error: err.message });
```

---