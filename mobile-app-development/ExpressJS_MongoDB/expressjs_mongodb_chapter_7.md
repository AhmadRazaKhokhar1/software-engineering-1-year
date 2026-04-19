# Chapter 7: Deployment, Testing & Capstone Project

## 🎯 Learning Objectives

By the end of this chapter, you will:
- Configure different environments (development, staging, production)
- Deploy Express applications to cloud platforms
- Set up cloud databases (MongoDB Atlas)
- Write unit and integration tests
- Integrate backend with frontend applications
- Build a complete capstone project from scratch

---

## 7.1 Environment Separation

### Understanding Environments

```
┌─────────────────────────────────────────────────────────────┐
│                    DEVELOPMENT WORKFLOW                      │
└─────────────────────────────────────────────────────────────┘

    Development        Staging            Production
    ┌─────────┐       ┌─────────┐        ┌─────────┐
    │  Local  │  ──>  │  Test   │  ──>   │  Live   │
    │ Machine │       │ Server  │        │ Server  │
    └─────────┘       └─────────┘        └─────────┘
         │                 │                  │
         ▼                 ▼                  ▼
    Local MongoDB     Test Database      Production DB
    Fake Services     Real Services      Real Services
    Debug Mode        Testing Mode       Optimized Mode
```

### Environment Configuration Files

```
project/
├── .env                    # Default/development (git-ignored)
├── .env.example            # Template (committed to git)
├── .env.development        # Development overrides
├── .env.staging            # Staging environment
├── .env.production         # Production environment
└── src/
    └── config/
        └── index.js        # Configuration loader
```

### Environment Files

```env
# .env.example (commit this)
NODE_ENV=development
PORT=3000

# Database
MONGODB_URI=

# JWT
JWT_SECRET=
JWT_EXPIRES_IN=7d

# Email
SMTP_HOST=
SMTP_PORT=
SMTP_USER=
SMTP_PASS=

# Cloud Storage
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_BUCKET_NAME=
AWS_REGION=

# Frontend URL
CLIENT_URL=

# API Keys
STRIPE_SECRET_KEY=
```

```env
# .env.development
NODE_ENV=development
PORT=3000

MONGODB_URI=mongodb://localhost:27017/myapp_dev

JWT_SECRET=dev-secret-key-not-for-production
JWT_EXPIRES_IN=30d

SMTP_HOST=smtp.mailtrap.io
SMTP_PORT=2525
SMTP_USER=your-mailtrap-user
SMTP_PASS=your-mailtrap-pass

CLIENT_URL=http://localhost:5173

LOG_LEVEL=debug
```

```env
# .env.production
NODE_ENV=production
PORT=3000

MONGODB_URI=mongodb+srv://user:pass@cluster.mongodb.net/myapp_prod

JWT_SECRET=super-secure-production-secret-min-32-chars
JWT_EXPIRES_IN=7d

SMTP_HOST=smtp.sendgrid.net
SMTP_PORT=587
SMTP_USER=apikey
SMTP_PASS=your-sendgrid-api-key

AWS_ACCESS_KEY_ID=your-aws-key
AWS_SECRET_ACCESS_KEY=your-aws-secret
AWS_BUCKET_NAME=myapp-prod-bucket
AWS_REGION=us-east-1

CLIENT_URL=https://myapp.com

LOG_LEVEL=info
```

### Configuration Module

```javascript
// src/config/index.js
const dotenv = require('dotenv');
const path = require('path');

// Load environment-specific .env file
const envFile = `.env.${process.env.NODE_ENV || 'development'}`;
dotenv.config({ path: path.resolve(process.cwd(), envFile) });

// Fallback to default .env
dotenv.config();

const config = {
  env: process.env.NODE_ENV || 'development',
  port: parseInt(process.env.PORT, 10) || 3000,
  
  // Database
  db: {
    uri: process.env.MONGODB_URI,
    options: {
      maxPoolSize: process.env.NODE_ENV === 'production' ? 50 : 10
    }
  },
  
  // JWT
  jwt: {
    secret: process.env.JWT_SECRET,
    expiresIn: process.env.JWT_EXPIRES_IN || '7d',
    cookieExpiresIn: parseInt(process.env.JWT_COOKIE_EXPIRES_IN, 10) || 7
  },
  
  // Email
  email: {
    host: process.env.SMTP_HOST,
    port: parseInt(process.env.SMTP_PORT, 10),
    user: process.env.SMTP_USER,
    pass: process.env.SMTP_PASS,
    from: process.env.EMAIL_FROM || 'noreply@myapp.com'
  },
  
  // AWS
  aws: {
    accessKeyId: process.env.AWS_ACCESS_KEY_ID,
    secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY,
    bucketName: process.env.AWS_BUCKET_NAME,
    region: process.env.AWS_REGION || 'us-east-1'
  },
  
  // URLs
  clientUrl: process.env.CLIENT_URL || 'http://localhost:5173',
  
  // Logging
  logLevel: process.env.LOG_LEVEL || 'info',
  
  // Feature flags
  features: {
    emailVerification: process.env.ENABLE_EMAIL_VERIFICATION === 'true',
    rateLimit: process.env.ENABLE_RATE_LIMIT !== 'false'
  }
};

// Validate required config
const requiredEnvVars = ['MONGODB_URI', 'JWT_SECRET'];

const missingVars = requiredEnvVars.filter(varName => !process.env[varName]);

if (missingVars.length > 0) {
  throw new Error(`Missing required environment variables: ${missingVars.join(', ')}`);
}

// Freeze config to prevent modifications
module.exports = Object.freeze(config);
```

### Environment-Specific Behavior

```javascript
// src/app.js
const express = require('express');
const config = require('./config');

const app = express();

// Development-only middleware
if (config.env === 'development') {
  const morgan = require('morgan');
  app.use(morgan('dev'));
  
  // Enable detailed error messages
  app.set('showStackError', true);
}

// Production-only middleware
if (config.env === 'production') {
  const compression = require('compression');
  const helmet = require('helmet');
  
  app.use(compression());
  app.use(helmet());
  
  // Trust proxy (for rate limiting behind load balancer)
  app.set('trust proxy', 1);
}

// Rate limiting (configurable)
if (config.features.rateLimit) {
  const rateLimit = require('express-rate-limit');
  
  const limiter = rateLimit({
    windowMs: 15 * 60 * 1000,
    max: config.env === 'production' ? 100 : 1000
  });
  
  app.use('/api', limiter);
}

module.exports = app;
```

---

## 7.2 Production Deployment

### Preparing for Production

#### 1. Package.json Scripts

```json
{
  "name": "my-express-api",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "start:prod": "NODE_ENV=production node server.js",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "lint": "eslint src/",
    "lint:fix": "eslint src/ --fix"
  },
  "engines": {
    "node": ">=18.0.0"
  },
  "dependencies": {
    "express": "^4.18.2",
    "mongoose": "^8.0.0",
    "dotenv": "^16.3.1",
    "cors": "^2.8.5",
    "helmet": "^7.1.0",
    "compression": "^1.7.4",
    "bcryptjs": "^2.4.3",
    "jsonwebtoken": "^9.0.2",
    "express-rate-limit": "^7.1.5",
    "express-mongo-sanitize": "^2.2.0",
    "xss-clean": "^0.1.4",
    "hpp": "^0.2.3",
    "winston": "^3.11.0"
  },
  "devDependencies": {
    "nodemon": "^3.0.2",
    "jest": "^29.7.0",
    "supertest": "^6.3.3",
    "eslint": "^8.56.0"
  }
}
```

#### 2. Production Server File

```javascript
// server.js
require('dotenv').config();

const app = require('./src/app');
const config = require('./src/config');
const connectDB = require('./src/config/db');
const logger = require('./src/utils/logger');

const startServer = async () => {
  try {
    // Connect to database
    await connectDB();
    
    const server = app.listen(config.port, () => {
      logger.info(`Server running in ${config.env} mode on port ${config.port}`);
    });
    
    // Graceful shutdown
    const gracefulShutdown = async (signal) => {
      logger.info(`${signal} received. Starting graceful shutdown...`);
      
      server.close(async () => {
        logger.info('HTTP server closed');
        
        // Close database connection
        const mongoose = require('mongoose');
        await mongoose.connection.close();
        logger.info('Database connection closed');
        
        process.exit(0);
      });
      
      // Force shutdown after 30 seconds
      setTimeout(() => {
        logger.error('Forced shutdown due to timeout');
        process.exit(1);
      }, 30000);
    };
    
    process.on('SIGTERM', () => gracefulShutdown('SIGTERM'));
    process.on('SIGINT', () => gracefulShutdown('SIGINT'));
    
  } catch (error) {
    logger.error('Failed to start server:', error);
    process.exit(1);
  }
};

// Handle unhandled rejections
process.on('unhandledRejection', (err) => {
  logger.error('Unhandled Rejection:', err);
  process.exit(1);
});

// Handle uncaught exceptions
process.on('uncaughtException', (err) => {
  logger.error('Uncaught Exception:', err);
  process.exit(1);
});

startServer();
```

### Deploying to Render

#### 1. Create render.yaml

```yaml
# render.yaml
services:
  - type: web
    name: my-express-api
    env: node
    plan: free
    buildCommand: npm install
    startCommand: npm start
    envVars:
      - key: NODE_ENV
        value: production
      - key: MONGODB_URI
        sync: false
      - key: JWT_SECRET
        generateValue: true
      - key: CLIENT_URL
        value: https://my-frontend.onrender.com
    healthCheckPath: /health
```

#### 2. Deployment Steps

```bash
# 1. Create a Render account at render.com

# 2. Connect your GitHub repository

# 3. Create a new Web Service
#    - Select your repository
#    - Choose Node environment
#    - Build command: npm install
#    - Start command: npm start

# 4. Add environment variables in Render dashboard

# 5. Deploy
```

### Deploying to Railway

```bash
# 1. Install Railway CLI
npm install -g @railway/cli

# 2. Login
railway login

# 3. Initialize project
railway init

# 4. Add MongoDB plugin (or use Atlas)
railway add

# 5. Set environment variables
railway variables set NODE_ENV=production
railway variables set JWT_SECRET=your-secret

# 6. Deploy
railway up
```

### Deploying to Heroku

```bash
# 1. Install Heroku CLI and login
heroku login

# 2. Create app
heroku create my-express-api

# 3. Set environment variables
heroku config:set NODE_ENV=production
heroku config:set MONGODB_URI=your-mongodb-uri
heroku config:set JWT_SECRET=your-secret

# 4. Add Procfile
echo "web: node server.js" > Procfile

# 5. Deploy
git push heroku main

# 6. Open app
heroku open
```

### Deploying with Docker

```dockerfile
# Dockerfile
FROM node:18-alpine

# Create app directory
WORKDIR /usr/src/app

# Install dependencies
COPY package*.json ./
RUN npm ci --only=production

# Copy app source
COPY . .

# Create non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nodejs -u 1001
USER nodejs

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:3000/health || exit 1

# Start app
CMD ["node", "server.js"]
```

```yaml
# docker-compose.yml
version: '3.8'

services:
  api:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - MONGODB_URI=mongodb://mongo:27017/myapp
      - JWT_SECRET=${JWT_SECRET}
    depends_on:
      - mongo
    restart: unless-stopped

  mongo:
    image: mongo:7
    volumes:
      - mongo-data:/data/db
    restart: unless-stopped

volumes:
  mongo-data:
```

```bash
# Build and run
docker-compose up -d

# View logs
docker-compose logs -f api

# Stop
docker-compose down
```

---

## 7.3 Cloud Databases (MongoDB Atlas)

### Setting Up MongoDB Atlas

```
1. Create account at mongodb.com/atlas

2. Create a new cluster (free tier available)

3. Configure database access:
   - Create database user with password
   - Add IP address to whitelist (0.0.0.0/0 for any IP)

4. Get connection string:
   - Click "Connect"
   - Choose "Connect your application"
   - Copy connection string
```

### Connection String Format

```
mongodb+srv://<username>:<password>@<cluster>.mongodb.net/<database>?retryWrites=true&w=majority
```

### Production Database Configuration

```javascript
// src/config/db.js
const mongoose = require('mongoose');
const config = require('./index');
const logger = require('../utils/logger');

const connectDB = async () => {
  try {
    const conn = await mongoose.connect(config.db.uri, {
      // Connection pool
      maxPoolSize: config.env === 'production' ? 50 : 10,
      minPoolSize: config.env === 'production' ? 10 : 2,
      
      // Timeouts
      serverSelectionTimeoutMS: 5000,
      socketTimeoutMS: 45000,
      
      // Retry
      retryWrites: true,
      retryReads: true
    });
    
    logger.info(`MongoDB Connected: ${conn.connection.host}`);
    
    // Connection event handlers
    mongoose.connection.on('error', (err) => {
      logger.error('MongoDB connection error:', err);
    });
    
    mongoose.connection.on('disconnected', () => {
      logger.warn('MongoDB disconnected. Attempting to reconnect...');
    });
    
    mongoose.connection.on('reconnected', () => {
      logger.info('MongoDB reconnected');
    });
    
  } catch (error) {
    logger.error('Failed to connect to MongoDB:', error);
    throw error;
  }
};

module.exports = connectDB;
```

### Database Backup Script

```javascript
// scripts/backup.js
const { exec } = require('child_process');
const path = require('path');
const fs = require('fs');

const BACKUP_DIR = path.join(__dirname, '../backups');
const MONGODB_URI = process.env.MONGODB_URI;

// Create backup directory if not exists
if (!fs.existsSync(BACKUP_DIR)) {
  fs.mkdirSync(BACKUP_DIR, { recursive: true });
}

const timestamp = new Date().toISOString().replace(/[:.]/g, '-');
const backupPath = path.join(BACKUP_DIR, `backup-${timestamp}`);

const command = `mongodump --uri="${MONGODB_URI}" --out="${backupPath}"`;

console.log('Starting backup...');

exec(command, (error, stdout, stderr) => {
  if (error) {
    console.error('Backup failed:', error);
    process.exit(1);
  }
  
  console.log('Backup completed successfully');
  console.log(`Backup location: ${backupPath}`);
});
```

---

## 7.4 API Testing

### Testing Setup

```bash
npm install --save-dev jest supertest mongodb-memory-server
```

### Jest Configuration

```javascript
// jest.config.js
module.exports = {
  testEnvironment: 'node',
  testMatch: ['**/__tests__/**/*.js', '**/*.test.js'],
  collectCoverageFrom: [
    'src/**/*.js',
    '!src/config/**',
    '!src/utils/logger.js'
  ],
  coverageDirectory: 'coverage',
  coverageThreshold: {
    global: {
      branches: 70,
      functions: 70,
      lines: 70,
      statements: 70
    }
  },
  setupFilesAfterEnv: ['./tests/setup.js'],
  testTimeout: 30000,
  verbose: true
};
```

### Test Setup File

```javascript
// tests/setup.js
const mongoose = require('mongoose');
const { MongoMemoryServer } = require('mongodb-memory-server');

let mongoServer;

// Connect to in-memory database before all tests
beforeAll(async () => {
  mongoServer = await MongoMemoryServer.create();
  const mongoUri = mongoServer.getUri();
  
  await mongoose.connect(mongoUri);
});

// Clear database between tests
afterEach(async () => {
  const collections = mongoose.connection.collections;
  
  for (const key in collections) {
    await collections[key].deleteMany({});
  }
});

// Disconnect and stop server after all tests
afterAll(async () => {
  await mongoose.disconnect();
  await mongoServer.stop();
});
```

### Unit Tests

```javascript
// tests/unit/models/User.test.js
const User = require('../../../src/models/User');

describe('User Model', () => {
  describe('Validation', () => {
    it('should create a valid user', async () => {
      const userData = {
        name: 'John Doe',
        email: 'john@example.com',
        password: 'Password123!'
      };
      
      const user = new User(userData);
      const savedUser = await user.save();
      
      expect(savedUser._id).toBeDefined();
      expect(savedUser.name).toBe(userData.name);
      expect(savedUser.email).toBe(userData.email);
      expect(savedUser.password).not.toBe(userData.password); // Should be hashed
    });
    
    it('should require name', async () => {
      const user = new User({
        email: 'test@example.com',
        password: 'Password123!'
      });
      
      await expect(user.save()).rejects.toThrow(/name.*required/i);
    });
    
    it('should require valid email', async () => {
      const user = new User({
        name: 'Test User',
        email: 'invalid-email',
        password: 'Password123!'
      });
      
      await expect(user.save()).rejects.toThrow(/valid email/i);
    });
    
    it('should require password with minimum length', async () => {
      const user = new User({
        name: 'Test User',
        email: 'test@example.com',
        password: 'short'
      });
      
      await expect(user.save()).rejects.toThrow(/at least 8 characters/i);
    });
    
    it('should not allow duplicate emails', async () => {
      const userData = {
        name: 'User 1',
        email: 'duplicate@example.com',
        password: 'Password123!'
      };
      
      await User.create(userData);
      
      const duplicateUser = new User({
        ...userData,
        name: 'User 2'
      });
      
      await expect(duplicateUser.save()).rejects.toThrow();
    });
  });
  
  describe('Methods', () => {
    it('should hash password before saving', async () => {
      const password = 'Password123!';
      const user = await User.create({
        name: 'Test User',
        email: 'test@example.com',
        password
      });
      
      expect(user.password).not.toBe(password);
      expect(user.password.length).toBeGreaterThan(password.length);
    });
    
    it('should compare password correctly', async () => {
      const password = 'Password123!';
      const user = await User.create({
        name: 'Test User',
        email: 'test@example.com',
        password
      });
      
      // Need to fetch with password since select: false
      const userWithPassword = await User.findById(user._id).select('+password');
      
      const isMatch = await userWithPassword.comparePassword(password);
      expect(isMatch).toBe(true);
      
      const isWrongMatch = await userWithPassword.comparePassword('wrongpassword');
      expect(isWrongMatch).toBe(false);
    });
  });
});
```

### Integration Tests

```javascript
// tests/integration/auth.test.js
const request = require('supertest');
const app = require('../../src/app');
const User = require('../../src/models/User');

describe('Auth Endpoints', () => {
  describe('POST /api/v1/auth/register', () => {
    it('should register a new user', async () => {
      const res = await request(app)
        .post('/api/v1/auth/register')
        .send({
          name: 'Test User',
          email: 'test@example.com',
          password: 'Password123!',
          passwordConfirm: 'Password123!'
        });
      
      expect(res.status).toBe(201);
      expect(res.body.success).toBe(true);
      expect(res.body.token).toBeDefined();
      expect(res.body.data.user.email).toBe('test@example.com');
      expect(res.body.data.user.password).toBeUndefined();
    });
    
    it('should not register with existing email', async () => {
      // Create user first
      await User.create({
        name: 'Existing User',
        email: 'existing@example.com',
        password: 'Password123!'
      });
      
      const res = await request(app)
        .post('/api/v1/auth/register')
        .send({
          name: 'New User',
          email: 'existing@example.com',
          password: 'Password123!',
          passwordConfirm: 'Password123!'
        });
      
      expect(res.status).toBe(409);
      expect(res.body.success).toBe(false);
    });
    
    it('should not register with mismatched passwords', async () => {
      const res = await request(app)
        .post('/api/v1/auth/register')
        .send({
          name: 'Test User',
          email: 'test@example.com',
          password: 'Password123!',
          passwordConfirm: 'DifferentPassword!'
        });
      
      expect(res.status).toBe(400);
      expect(res.body.success).toBe(false);
    });
  });
  
  describe('POST /api/v1/auth/login', () => {
    beforeEach(async () => {
      await User.create({
        name: 'Test User',
        email: 'test@example.com',
        password: 'Password123!'
      });
    });
    
    it('should login with valid credentials', async () => {
      const res = await request(app)
        .post('/api/v1/auth/login')
        .send({
          email: 'test@example.com',
          password: 'Password123!'
        });
      
      expect(res.status).toBe(200);
      expect(res.body.success).toBe(true);
      expect(res.body.token).toBeDefined();
    });
    
    it('should not login with wrong password', async () => {
      const res = await request(app)
        .post('/api/v1/auth/login')
        .send({
          email: 'test@example.com',
          password: 'WrongPassword!'
        });
      
      expect(res.status).toBe(401);
      expect(res.body.success).toBe(false);
    });
    
    it('should not login with non-existent email', async () => {
      const res = await request(app)
        .post('/api/v1/auth/login')
        .send({
          email: 'nonexistent@example.com',
          password: 'Password123!'
        });
      
      expect(res.status).toBe(401);
      expect(res.body.success).toBe(false);
    });
  });
  
  describe('GET /api/v1/auth/me', () => {
    let token;
    
    beforeEach(async () => {
      const user = await User.create({
        name: 'Test User',
        email: 'test@example.com',
        password: 'Password123!'
      });
      
      const res = await request(app)
        .post('/api/v1/auth/login')
        .send({
          email: 'test@example.com',
          password: 'Password123!'
        });
      
      token = res.body.token;
    });
    
    it('should get current user with valid token', async () => {
      const res = await request(app)
        .get('/api/v1/auth/me')
        .set('Authorization', `Bearer ${token}`);
      
      expect(res.status).toBe(200);
      expect(res.body.success).toBe(true);
      expect(res.body.data.email).toBe('test@example.com');
    });
    
    it('should not access without token', async () => {
      const res = await request(app)
        .get('/api/v1/auth/me');
      
      expect(res.status).toBe(401);
    });
    
    it('should not access with invalid token', async () => {
      const res = await request(app)
        .get('/api/v1/auth/me')
        .set('Authorization', 'Bearer invalid-token');
      
      expect(res.status).toBe(401);
    });
  });
});
```

### Product API Tests

```javascript
// tests/integration/products.test.js
const request = require('supertest');
const app = require('../../src/app');
const User = require('../../src/models/User');
const Product = require('../../src/models/Product');

describe('Product Endpoints', () => {
  let adminToken;
  let userToken;
  
  beforeEach(async () => {
    // Create admin user
    await User.create({
      name: 'Admin',
      email: 'admin@example.com',
      password: 'Password123!',
      role: 'admin'
    });
    
    const adminRes = await request(app)
      .post('/api/v1/auth/login')
      .send({ email: 'admin@example.com', password: 'Password123!' });
    adminToken = adminRes.body.token;
    
    // Create regular user
    await User.create({
      name: 'User',
      email: 'user@example.com',
      password: 'Password123!'
    });
    
    const userRes = await request(app)
      .post('/api/v1/auth/login')
      .send({ email: 'user@example.com', password: 'Password123!' });
    userToken = userRes.body.token;
  });
  
  describe('GET /api/v1/products', () => {
    beforeEach(async () => {
      await Product.create([
        { name: 'Product 1', description: 'Description 1', price: 100, category: 'electronics', stock: 10 },
        { name: 'Product 2', description: 'Description 2', price: 200, category: 'clothing', stock: 20 },
        { name: 'Product 3', description: 'Description 3', price: 300, category: 'electronics', stock: 30 }
      ]);
    });
    
    it('should get all products', async () => {
      const res = await request(app).get('/api/v1/products');
      
      expect(res.status).toBe(200);
      expect(res.body.success).toBe(true);
      expect(res.body.count).toBe(3);
      expect(res.body.data).toHaveLength(3);
    });
    
    it('should filter by category', async () => {
      const res = await request(app)
        .get('/api/v1/products?category=electronics');
      
      expect(res.status).toBe(200);
      expect(res.body.count).toBe(2);
      expect(res.body.data.every(p => p.category === 'electronics')).toBe(true);
    });
    
    it('should filter by price range', async () => {
      const res = await request(app)
        .get('/api/v1/products?price[gte]=150&price[lte]=250');
      
      expect(res.status).toBe(200);
      expect(res.body.count).toBe(1);
      expect(res.body.data[0].price).toBe(200);
    });
    
    it('should sort by price', async () => {
      const res = await request(app)
        .get('/api/v1/products?sort=-price');
      
      expect(res.status).toBe(200);
      expect(res.body.data[0].price).toBe(300);
      expect(res.body.data[2].price).toBe(100);
    });
    
    it('should paginate results', async () => {
      const res = await request(app)
        .get('/api/v1/products?page=1&limit=2');
      
      expect(res.status).toBe(200);
      expect(res.body.data).toHaveLength(2);
      expect(res.body.pagination.currentPage).toBe(1);
      expect(res.body.pagination.totalPages).toBe(2);
    });
  });
  
  describe('POST /api/v1/products', () => {
    const validProduct = {
      name: 'Test Product',
      description: 'Test description for the product',
      price: 99.99,
      category: 'electronics',
      stock: 50
    };
    
    it('should create product as admin', async () => {
      const res = await request(app)
        .post('/api/v1/products')
        .set('Authorization', `Bearer ${adminToken}`)
        .send(validProduct);
      
      expect(res.status).toBe(201);
      expect(res.body.success).toBe(true);
      expect(res.body.data.name).toBe(validProduct.name);
      expect(res.body.data.slug).toBe('test-product');
    });
    
    it('should not create product without authentication', async () => {
      const res = await request(app)
        .post('/api/v1/products')
        .send(validProduct);
      
      expect(res.status).toBe(401);
    });
    
    it('should not create product as regular user', async () => {
      const res = await request(app)
        .post('/api/v1/products')
        .set('Authorization', `Bearer ${userToken}`)
        .send(validProduct);
      
      expect(res.status).toBe(403);
    });
    
    it('should validate required fields', async () => {
      const res = await request(app)
        .post('/api/v1/products')
        .set('Authorization', `Bearer ${adminToken}`)
        .send({ name: 'Only Name' });
      
      expect(res.status).toBe(400);
    });
  });
  
  describe('PUT /api/v1/products/:id', () => {
    let productId;
    
    beforeEach(async () => {
      const product = await Product.create({
        name: 'Original Product',
        description: 'Original description',
        price: 100,
        category: 'electronics',
        stock: 10
      });
      productId = product._id.toString();
    });
    
    it('should update product as admin', async () => {
      const res = await request(app)
        .put(`/api/v1/products/${productId}`)
        .set('Authorization', `Bearer ${adminToken}`)
        .send({ price: 150, stock: 20 });
      
      expect(res.status).toBe(200);
      expect(res.body.data.price).toBe(150);
      expect(res.body.data.stock).toBe(20);
    });
    
    it('should return 404 for non-existent product', async () => {
      const fakeId = '507f1f77bcf86cd799439011';
      
      const res = await request(app)
        .put(`/api/v1/products/${fakeId}`)
        .set('Authorization', `Bearer ${adminToken}`)
        .send({ price: 150 });
      
      expect(res.status).toBe(404);
    });
  });
  
  describe('DELETE /api/v1/products/:id', () => {
    let productId;
    
    beforeEach(async () => {
      const product = await Product.create({
        name: 'Product to Delete',
        description: 'Will be deleted',
        price: 100,
        category: 'electronics',
        stock: 10
      });
      productId = product._id.toString();
    });
    
    it('should delete product as admin', async () => {
      const res = await request(app)
        .delete(`/api/v1/products/${productId}`)
        .set('Authorization', `Bearer ${adminToken}`);
      
      expect(res.status).toBe(200);
      
      // Verify deletion
      const product = await Product.findById(productId);
      expect(product).toBeNull();
    });
    
    it('should not delete as regular user', async () => {
      const res = await request(app)
        .delete(`/api/v1/products/${productId}`)
        .set('Authorization', `Bearer ${userToken}`);
      
      expect(res.status).toBe(403);
    });
  });
});
```

### Running Tests

```bash
# Run all tests
npm test

# Run tests in watch mode
npm run test:watch

# Run with coverage
npm run test:coverage

# Run specific test file
npm test -- tests/integration/auth.test.js

# Run tests matching pattern
npm test -- --testNamePattern="login"
```

---

## 7.5 Frontend Integration

### CORS Configuration for Frontend

```javascript
// src/app.js
const cors = require('cors');

const corsOptions = {
  origin: function(origin, callback) {
    const allowedOrigins = [
      process.env.CLIENT_URL,
      'http://localhost:5173',  // Vite dev server
      'http://localhost:3001'   // React dev server
    ];
    
    // Allow requests with no origin (mobile apps, curl)
    if (!origin || allowedOrigins.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE', 'OPTIONS'],
  allowedHeaders: ['Content-Type', 'Authorization']
};

app.use(cors(corsOptions));
```

### Frontend API Client (React Example)

```javascript
// frontend/src/api/client.js
import axios from 'axios';

const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000/api/v1';

const apiClient = axios.create({
  baseURL: API_URL,
  headers: {
    'Content-Type': 'application/json'
  },
  withCredentials: true
});

// Request interceptor - add auth token
apiClient.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// Response interceptor - handle errors
apiClient.interceptors.response.use(
  (response) => response.data,
  (error) => {
    if (error.response?.status === 401) {
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error.response?.data || error);
  }
);

export default apiClient;
```

### API Services

```javascript
// frontend/src/api/authService.js
import apiClient from './client';

export const authService = {
  register: async (userData) => {
    const response = await apiClient.post('/auth/register', userData);
    if (response.token) {
      localStorage.setItem('token', response.token);
    }
    return response;
  },
  
  login: async (credentials) => {
    const response = await apiClient.post('/auth/login', credentials);
    if (response.token) {
      localStorage.setItem('token', response.token);
    }
    return response;
  },
  
  logout: () => {
    localStorage.removeItem('token');
  },
  
  getMe: async () => {
    return await apiClient.get('/auth/me');
  },
  
  updatePassword: async (passwords) => {
    return await apiClient.put('/auth/update-password', passwords);
  }
};
```

```javascript
// frontend/src/api/productService.js
import apiClient from './client';

export const productService = {
  getAll: async (params = {}) => {
    const queryString = new URLSearchParams(params).toString();
    return await apiClient.get(`/products?${queryString}`);
  },
  
  getById: async (id) => {
    return await apiClient.get(`/products/${id}`);
  },
  
  create: async (productData) => {
    return await apiClient.post('/products', productData);
  },
  
  update: async (id, productData) => {
    return await apiClient.put(`/products/${id}`, productData);
  },
  
  delete: async (id) => {
    return await apiClient.delete(`/products/${id}`);
  }
};
```

### React Hook Example

```javascript
// frontend/src/hooks/useProducts.js
import { useState, useEffect } from 'react';
import { productService } from '../api/productService';

export const useProducts = (initialParams = {}) => {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  const [pagination, setPagination] = useState({});
  
  const fetchProducts = async (params = initialParams) => {
    try {
      setLoading(true);
      setError(null);
      
      const response = await productService.getAll(params);
      
      setProducts(response.data);
      setPagination(response.pagination);
    } catch (err) {
      setError(err.error?.message || 'Failed to fetch products');
    } finally {
      setLoading(false);
    }
  };
  
  useEffect(() => {
    fetchProducts();
  }, []);
  
  return {
    products,
    loading,
    error,
    pagination,
    refetch: fetchProducts
  };
};
```

### React Component Example

```jsx
// frontend/src/pages/Products.jsx
import { useState } from 'react';
import { useProducts } from '../hooks/useProducts';

export default function Products() {
  const [filters, setFilters] = useState({
    page: 1,
    limit: 10,
    category: '',
    sort: '-createdAt'
  });
  
  const { products, loading, error, pagination, refetch } = useProducts(filters);
  
  const handleFilterChange = (newFilters) => {
    const updatedFilters = { ...filters, ...newFilters, page: 1 };
    setFilters(updatedFilters);
    refetch(updatedFilters);
  };
  
  const handlePageChange = (page) => {
    const updatedFilters = { ...filters, page };
    setFilters(updatedFilters);
    refetch(updatedFilters);
  };
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  
  return (
    <div>
      <h1>Products</h1>
      
      {/* Filters */}
      <div>
        <select
          value={filters.category}
          onChange={(e) => handleFilterChange({ category: e.target.value })}
        >
          <option value="">All Categories</option>
          <option value="electronics">Electronics</option>
          <option value="clothing">Clothing</option>
          <option value="books">Books</option>
        </select>
        
        <select
          value={filters.sort}
          onChange={(e) => handleFilterChange({ sort: e.target.value })}
        >
          <option value="-createdAt">Newest</option>
          <option value="price">Price: Low to High</option>
          <option value="-price">Price: High to Low</option>
        </select>
      </div>
      
      {/* Product Grid */}
      <div className="product-grid">
        {products.map(product => (
          <div key={product._id} className="product-card">
            <h3>{product.name}</h3>
            <p>${product.price}</p>
            <p>{product.category}</p>
          </div>
        ))}
      </div>
      
      {/* Pagination */}
      <div>
        <button
          disabled={!pagination.hasPrevPage}
          onClick={() => handlePageChange(pagination.currentPage - 1)}
        >
          Previous
        </button>
        
        <span>
          Page {pagination.currentPage} of {pagination.totalPages}
        </span>
        
        <button
          disabled={!pagination.hasNextPage}
          onClick={() => handlePageChange(pagination.currentPage + 1)}
        >
          Next
        </button>
      </div>
    </div>
  );
}
```

---

## 7.6 Capstone Project: E-Commerce API

### Project Overview

Build a complete e-commerce backend with:
- User authentication and authorization
- Product management
- Shopping cart
- Order processing
- Payment integration (conceptual)
- Admin dashboard API

### Project Structure

```
ecommerce-api/
├── src/
│   ├── config/
│   │   ├── index.js
│   │   ├── db.js
│   │   └── swagger.js
│   │
│   ├── controllers/
│   │   ├── authController.js
│   │   ├── userController.js
│   │   ├── productController.js
│   │   ├── cartController.js
│   │   ├── orderController.js
│   │   └── adminController.js
│   │
│   ├── middleware/
│   │   ├── auth.js
│   │   ├── errorHandler.js
│   │   ├── validate.js
│   │   └── upload.js
│   │
│   ├── models/
│   │   ├── User.js
│   │   ├── Product.js
│   │   ├── Cart.js
│   │   ├── Order.js
│   │   └── Review.js
│   │
│   ├── routes/
│   │   ├── index.js
│   │   ├── authRoutes.js
│   │   ├── userRoutes.js
│   │   ├── productRoutes.js
│   │   ├── cartRoutes.js
│   │   ├── orderRoutes.js
│   │   └── adminRoutes.js
│   │
│   ├── services/
│   │   ├── emailService.js
│   │   └── paymentService.js
│   │
│   ├── utils/
│   │   ├── AppError.js
│   │   ├── asyncHandler.js
│   │   ├── APIFeatures.js
│   │   └── logger.js
│   │
│   └── app.js
│
├── tests/
│   ├── setup.js
│   ├── unit/
│   └── integration/
│
├── .env.example
├── .gitignore
├── package.json
├── server.js
└── README.md
```

### Core Models

```javascript
// src/models/Cart.js
const mongoose = require('mongoose');

const cartItemSchema = new mongoose.Schema({
  product: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Product',
    required: true
  },
  quantity: {
    type: Number,
    required: true,
    min: [1, 'Quantity must be at least 1'],
    default: 1
  },
  price: {
    type: Number,
    required: true
  }
});

const cartSchema = new mongoose.Schema({
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true,
    unique: true
  },
  items: [cartItemSchema],
  totalAmount: {
    type: Number,
    default: 0
  }
}, {
  timestamps: true
});

// Calculate total before saving
cartSchema.pre('save', function(next) {
  this.totalAmount = this.items.reduce((total, item) => {
    return total + (item.price * item.quantity);
  }, 0);
  next();
});

module.exports = mongoose.model('Cart', cartSchema);
```

```javascript
// src/models/Order.js
const mongoose = require('mongoose');

const orderItemSchema = new mongoose.Schema({
  product: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Product',
    required: true
  },
  name: String,
  price: Number,
  quantity: {
    type: Number,
    required: true,
    min: 1
  }
});

const orderSchema = new mongoose.Schema({
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true
  },
  items: [orderItemSchema],
  shippingAddress: {
    street: { type: String, required: true },
    city: { type: String, required: true },
    state: { type: String, required: true },
    zipCode: { type: String, required: true },
    country: { type: String, required: true }
  },
  paymentMethod: {
    type: String,
    enum: ['card', 'paypal', 'cod'],
    required: true
  },
  paymentStatus: {
    type: String,
    enum: ['pending', 'paid', 'failed', 'refunded'],
    default: 'pending'
  },
  orderStatus: {
    type: String,
    enum: ['pending', 'processing', 'shipped', 'delivered', 'cancelled'],
    default: 'pending'
  },
  subtotal: {
    type: Number,
    required: true
  },
  shippingCost: {
    type: Number,
    default: 0
  },
  tax: {
    type: Number,
    default: 0
  },
  totalAmount: {
    type: Number,
    required: true
  },
  paidAt: Date,
  deliveredAt: Date,
  trackingNumber: String,
  notes: String
}, {
  timestamps: true
});

// Indexes
orderSchema.index({ user: 1, createdAt: -1 });
orderSchema.index({ orderStatus: 1 });
orderSchema.index({ paymentStatus: 1 });

module.exports = mongoose.model('Order', orderSchema);
```

### Cart Controller

```javascript
// src/controllers/cartController.js
const Cart = require('../models/Cart');
const Product = require('../models/Product');
const asyncHandler = require('../utils/asyncHandler');
const AppError = require('../utils/AppError');

// @desc    Get user's cart
// @route   GET /api/v1/cart
// @access  Private
exports.getCart = asyncHandler(async (req, res, next) => {
  let cart = await Cart.findOne({ user: req.user._id })
    .populate('items.product', 'name price images stock');
  
  if (!cart) {
    cart = await Cart.create({ user: req.user._id, items: [] });
  }
  
  res.status(200).json({
    success: true,
    data: cart
  });
});

// @desc    Add item to cart
// @route   POST /api/v1/cart/items
// @access  Private
exports.addToCart = asyncHandler(async (req, res, next) => {
  const { productId, quantity = 1 } = req.body;
  
  // Find product
  const product = await Product.findById(productId);
  
  if (!product) {
    return next(new AppError('Product not found', 404));
  }
  
  if (product.stock < quantity) {
    return next(new AppError(`Only ${product.stock} items available`, 400));
  }
  
  // Find or create cart
  let cart = await Cart.findOne({ user: req.user._id });
  
  if (!cart) {
    cart = new Cart({ user: req.user._id, items: [] });
  }
  
  // Check if product already in cart
  const existingItemIndex = cart.items.findIndex(
    item => item.product.toString() === productId
  );
  
  if (existingItemIndex > -1) {
    // Update quantity
    const newQuantity = cart.items[existingItemIndex].quantity + quantity;
    
    if (newQuantity > product.stock) {
      return next(new AppError(`Only ${product.stock} items available`, 400));
    }
    
    cart.items[existingItemIndex].quantity = newQuantity;
  } else {
    // Add new item
    cart.items.push({
      product: productId,
      quantity,
      price: product.discountPrice || product.price
    });
  }
  
  await cart.save();
  
  // Populate product details
  await cart.populate('items.product', 'name price images stock');
  
  res.status(200).json({
    success: true,
    data: cart
  });
});

// @desc    Update cart item quantity
// @route   PUT /api/v1/cart/items/:productId
// @access  Private
exports.updateCartItem = asyncHandler(async (req, res, next) => {
  const { productId } = req.params;
  const { quantity } = req.body;
  
  if (quantity < 1) {
    return next(new AppError('Quantity must be at least 1', 400));
  }
  
  // Check product stock
  const product = await Product.findById(productId);
  
  if (!product) {
    return next(new AppError('Product not found', 404));
  }
  
  if (quantity > product.stock) {
    return next(new AppError(`Only ${product.stock} items available`, 400));
  }
  
  const cart = await Cart.findOne({ user: req.user._id });
  
  if (!cart) {
    return next(new AppError('Cart not found', 404));
  }
  
  const itemIndex = cart.items.findIndex(
    item => item.product.toString() === productId
  );
  
  if (itemIndex === -1) {
    return next(new AppError('Item not in cart', 404));
  }
  
  cart.items[itemIndex].quantity = quantity;
  await cart.save();
  
  await cart.populate('items.product', 'name price images stock');
  
  res.status(200).json({
    success: true,
    data: cart
  });
});

// @desc    Remove item from cart
// @route   DELETE /api/v1/cart/items/:productId
// @access  Private
exports.removeFromCart = asyncHandler(async (req, res, next) => {
  const { productId } = req.params;
  
  const cart = await Cart.findOne({ user: req.user._id });
  
  if (!cart) {
    return next(new AppError('Cart not found', 404));
  }
  
  cart.items = cart.items.filter(
    item => item.product.toString() !== productId
  );
  
  await cart.save();
  
  await cart.populate('items.product', 'name price images stock');
  
  res.status(200).json({
    success: true,
    data: cart
  });
});

// @desc    Clear cart
// @route   DELETE /api/v1/cart
// @access  Private
exports.clearCart = asyncHandler(async (req, res, next) => {
  const cart = await Cart.findOne({ user: req.user._id });
  
  if (cart) {
    cart.items = [];
    await cart.save();
  }
  
  res.status(200).json({
    success: true,
    data: cart
  });
});
```

### Order Controller

```javascript
// src/controllers/orderController.js
const mongoose = require('mongoose');
const Order = require('../models/Order');
const Cart = require('../models/Cart');
const Product = require('../models/Product');
const asyncHandler = require('../utils/asyncHandler');
const AppError = require('../utils/AppError');

// @desc    Create order from cart
// @route   POST /api/v1/orders
// @access  Private
exports.createOrder = asyncHandler(async (req, res, next) => {
  const { shippingAddress, paymentMethod } = req.body;
  
  // Get user's cart
  const cart = await Cart.findOne({ user: req.user._id })
    .populate('items.product');
  
  if (!cart || cart.items.length === 0) {
    return next(new AppError('Cart is empty', 400));
  }
  
  // Start transaction
  const session = await mongoose.startSession();
  session.startTransaction();
  
  try {
    // Validate stock and prepare order items
    const orderItems = [];
    
    for (const item of cart.items) {
      const product = await Product.findById(item.product._id).session(session);
      
      if (!product) {
        throw new AppError(`Product ${item.product.name} not found`, 404);
      }
      
      if (product.stock < item.quantity) {
        throw new AppError(
          `Insufficient stock for ${product.name}. Available: ${product.stock}`,
          400
        );
      }
      
      // Reduce stock
      product.stock -= item.quantity;
      await product.save({ session });
      
      orderItems.push({
        product: product._id,
        name: product.name,
        price: item.price,
        quantity: item.quantity
      });
    }
    
    // Calculate totals
    const subtotal = cart.totalAmount;
    const shippingCost = subtotal >= 100 ? 0 : 10; // Free shipping over $100
    const tax = subtotal * 0.1; // 10% tax
    const totalAmount = subtotal + shippingCost + tax;
    
    // Create order
    const [order] = await Order.create([{
      user: req.user._id,
      items: orderItems,
      shippingAddress,
      paymentMethod,
      subtotal,
      shippingCost,
      tax,
      totalAmount
    }], { session });
    
    // Clear cart
    cart.items = [];
    await cart.save({ session });
    
    await session.commitTransaction();
    
    res.status(201).json({
      success: true,
      data: order
    });
    
  } catch (error) {
    await session.abortTransaction();
    return next(error);
  } finally {
    session.endSession();
  }
});

// @desc    Get user's orders
// @route   GET /api/v1/orders
// @access  Private
exports.getMyOrders = asyncHandler(async (req, res, next) => {
  const page = parseInt(req.query.page, 10) || 1;
  const limit = parseInt(req.query.limit, 10) || 10;
  const skip = (page - 1) * limit;
  
  const orders = await Order.find({ user: req.user._id })
    .sort('-createdAt')
    .skip(skip)
    .limit(limit);
  
  const total = await Order.countDocuments({ user: req.user._id });
  
  res.status(200).json({
    success: true,
    count: orders.length,
    pagination: {
      currentPage: page,
      totalPages: Math.ceil(total / limit),
      totalItems: total
    },
    data: orders
  });
});

// @desc    Get single order
// @route   GET /api/v1/orders/:id
// @access  Private
exports.getOrder = asyncHandler(async (req, res, next) => {
  const order = await Order.findById(req.params.id)
    .populate('items.product', 'name images');
  
  if (!order) {
    return next(new AppError('Order not found', 404));
  }
  
  // Check ownership (unless admin)
  if (order.user.toString() !== req.user._id.toString() && req.user.role !== 'admin') {
    return next(new AppError('Not authorized to view this order', 403));
  }
  
  res.status(200).json({
    success: true,
    data: order
  });
});

// @desc    Cancel order
// @route   PUT /api/v1/orders/:id/cancel
// @access  Private
exports.cancelOrder = asyncHandler(async (req, res, next) => {
  const order = await Order.findById(req.params.id);
  
  if (!order) {
    return next(new AppError('Order not found', 404));
  }
  
  // Check ownership
  if (order.user.toString() !== req.user._id.toString()) {
    return next(new AppError('Not authorized', 403));
  }
  
  // Can only cancel pending orders
  if (order.orderStatus !== 'pending') {
    return next(new AppError('Can only cancel pending orders', 400));
  }
  
  const session = await mongoose.startSession();
  session.startTransaction();
  
  try {
    // Restore product stock
    for (const item of order.items) {
      await Product.findByIdAndUpdate(
        item.product,
        { $inc: { stock: item.quantity } },
        { session }
      );
    }
    
    // Update order status
    order.orderStatus = 'cancelled';
    await order.save({ session });
    
    await session.commitTransaction();
    
    res.status(200).json({
      success: true,
      data: order
    });
    
  } catch (error) {
    await session.abortTransaction();
    return next(error);
  } finally {
    session.endSession();
  }
});

// @desc    Update order status (Admin)
// @route   PUT /api/v1/orders/:id/status
// @access  Private/Admin
exports.updateOrderStatus = asyncHandler(async (req, res, next) => {
  const { orderStatus, trackingNumber } = req.body;
  
  const order = await Order.findById(req.params.id);
  
  if (!order) {
    return next(new AppError('Order not found', 404));
  }
  
  order.orderStatus = orderStatus;
  
  if (trackingNumber) {
    order.trackingNumber = trackingNumber;
  }
  
  if (orderStatus === 'delivered') {
    order.deliveredAt = new Date();
  }
  
  await order.save();
  
  res.status(200).json({
    success: true,
    data: order
  });
});
```

### API Routes

```javascript
// src/routes/index.js
const express = require('express');
const router = express.Router();

const authRoutes = require('./authRoutes');
const userRoutes = require('./userRoutes');
const productRoutes = require('./productRoutes');
const cartRoutes = require('./cartRoutes');
const orderRoutes = require('./orderRoutes');
const adminRoutes = require('./adminRoutes');

router.use('/auth', authRoutes);
router.use('/users', userRoutes);
router.use('/products', productRoutes);
router.use('/cart', cartRoutes);
router.use('/orders', orderRoutes);
router.use('/admin', adminRoutes);

module.exports = router;
```

```javascript
// src/routes/cartRoutes.js
const express = require('express');
const router = express.Router();
const { protect } = require('../middleware/auth');
const {
  getCart,
  addToCart,
  updateCartItem,
  removeFromCart,
  clearCart
} = require('../controllers/cartController');

router.use(protect);

router.route('/')
  .get(getCart)
  .delete(clearCart);

router.route('/items')
  .post(addToCart);

router.route('/items/:productId')
  .put(updateCartItem)
  .delete(removeFromCart);

module.exports = router;
```

```javascript
// src/routes/orderRoutes.js
const express = require('express');
const router = express.Router();
const { protect, restrictTo } = require('../middleware/auth');
const {
  createOrder,
  getMyOrders,
  getOrder,
  cancelOrder,
  updateOrderStatus
} = require('../controllers/orderController');

router.use(protect);

router.route('/')
  .get(getMyOrders)
  .post(createOrder);

router.route('/:id')
  .get(getOrder);

router.put('/:id/cancel', cancelOrder);

router.put(
  '/:id/status',
  restrictTo('admin'),
  updateOrderStatus
);

module.exports = router;
```

### Admin Dashboard API

```javascript
// src/controllers/adminController.js
const User = require('../models/User');
const Product = require('../models/Product');
const Order = require('../models/Order');
const asyncHandler = require('../utils/asyncHandler');

// @desc    Get dashboard statistics
// @route   GET /api/v1/admin/dashboard
// @access  Private/Admin
exports.getDashboardStats = asyncHandler(async (req, res, next) => {
  const [
    totalUsers,
    totalProducts,
    totalOrders,
    revenueStats,
    recentOrders,
    topProducts
  ] = await Promise.all([
    // Total users
    User.countDocuments({ role: 'user' }),
    
    // Total products
    Product.countDocuments({ isActive: true }),
    
    // Total orders
    Order.countDocuments(),
    
    // Revenue statistics
    Order.aggregate([
      { $match: { paymentStatus: 'paid' } },
      {
        $group: {
          _id: null,
          totalRevenue: { $sum: '$totalAmount' },
          averageOrderValue: { $avg: '$totalAmount' },
          orderCount: { $sum: 1 }
        }
      }
    ]),
    
    // Recent orders
    Order.find()
      .sort('-createdAt')
      .limit(10)
      .populate('user', 'name email'),
    
    // Top selling products
    Order.aggregate([
      { $unwind: '$items' },
      {
        $group: {
          _id: '$items.product',
          name: { $first: '$items.name' },
          totalSold: { $sum: '$items.quantity' },
          revenue: { $sum: { $multiply: ['$items.price', '$items.quantity'] } }
        }
      },
      { $sort: { totalSold: -1 } },
      { $limit: 5 }
    ])
  ]);
  
  res.status(200).json({
    success: true,
    data: {
      overview: {
        totalUsers,
        totalProducts,
        totalOrders,
        totalRevenue: revenueStats[0]?.totalRevenue || 0,
        averageOrderValue: revenueStats[0]?.averageOrderValue || 0
      },
      recentOrders,
      topProducts
    }
  });
});

// @desc    Get sales report
// @route   GET /api/v1/admin/reports/sales
// @access  Private/Admin
exports.getSalesReport = asyncHandler(async (req, res, next) => {
  const { startDate, endDate } = req.query;
  
  const matchStage = {
    paymentStatus: 'paid'
  };
  
  if (startDate || endDate) {
    matchStage.createdAt = {};
    if (startDate) matchStage.createdAt.$gte = new Date(startDate);
    if (endDate) matchStage.createdAt.$lte = new Date(endDate);
  }
  
  const salesByDay = await Order.aggregate([
    { $match: matchStage },
    {
      $group: {
        _id: { $dateToString: { format: '%Y-%m-%d', date: '$createdAt' } },
        orders: { $sum: 1 },
        revenue: { $sum: '$totalAmount' }
      }
    },
    { $sort: { _id: 1 } }
  ]);
  
  const salesByCategory = await Order.aggregate([
    { $match: matchStage },
    { $unwind: '$items' },
    {
      $lookup: {
        from: 'products',
        localField: 'items.product',
        foreignField: '_id',
        as: 'product'
      }
    },
    { $unwind: '$product' },
    {
      $group: {
        _id: '$product.category',
        orders: { $sum: 1 },
        revenue: { $sum: { $multiply: ['$items.price', '$items.quantity'] } }
      }
    },
    { $sort: { revenue: -1 } }
  ]);
  
  res.status(200).json({
    success: true,
    data: {
      salesByDay,
      salesByCategory
    }
  });
});
```

---

## 📚 Chapter Summary

### What You Learned

✅ Configure environment-specific settings  
✅ Deploy to cloud platforms (Render, Railway, Heroku)  
✅ Use Docker for containerization  
✅ Set up MongoDB Atlas for production  
✅ Write comprehensive unit and integration tests  
✅ Integrate backend with frontend applications  
✅ Build a complete e-commerce API  

### Deployment Checklist

```
✅ Environment variables configured
✅ Production database connection
✅ Security middleware enabled
✅ Error handling in place
✅ Logging configured
✅ Health check endpoint
✅ Graceful shutdown handling
✅ Tests passing
✅ API documentation ready
```

### Project Completion

Congratulations! You've completed the Express.js + MongoDB course. You now have the skills to:

- Build production-ready REST APIs
- Implement authentication and authorization
- Design efficient database schemas
- Handle file uploads and background jobs
- Deploy to cloud platforms
- Test your applications thoroughly

---

## 🎉 Course Complete!

Congratulations on completing the **Express.js + MongoDB Course**! You now have the knowledge and skills to:

✅ Build production-ready REST APIs  
✅ Design efficient MongoDB schemas  
✅ Implement secure authentication & authorization  
✅ Handle file uploads and background jobs  
✅ Optimize performance with caching and indexing  
✅ Write comprehensive tests  
✅ Deploy to cloud platforms  

### What's Next?

1. **Build Projects** - Apply what you've learned
2. **Explore GraphQL** - Alternative to REST APIs
3. **Learn TypeScript** - Type-safe Node.js development
4. **Study Microservices** - Scale your architecture
5. **Master DevOps** - CI/CD, Kubernetes, monitoring

**Happy coding! 🚀**