# Chapter 3: TypeScript in Real Projects (Frontend & Backend)

## Introduction

You've mastered TypeScript fundamentals and advanced types. Now it's time to apply everything in **real-world projects**!

This chapter bridges the gap between learning TypeScript and using it professionally. You'll learn how to integrate TypeScript with modern build tools, type API responses safely, build classes with proper OOP patterns, leverage the power of generics, handle errors gracefully, and follow best practices used in production codebases.

By the end of this chapter, you'll be **job-ready** with TypeScript and confident in using it for both frontend and backend development.

---

## 1. TypeScript with Modern Tooling

### TypeScript with Bundlers (Vite / Webpack)

Modern projects rarely use `tsc` directly. Instead, they use **bundlers** that handle TypeScript compilation automatically.

**Vite (Recommended for new projects):**

Vite is a fast, modern build tool with first-class TypeScript support.

```bash
# Create a new Vite project with TypeScript
npm create vite@latest my-app -- --template vanilla-ts

# For React + TypeScript
npm create vite@latest my-app -- --template react-ts

# For Vue + TypeScript
npm create vite@latest my-app -- --template vue-ts

cd my-app
npm install
npm run dev
```

Vite handles TypeScript compilation automatically — no extra configuration needed!

**Project structure (Vite + TypeScript):**

```
my-app/
├── src/
│   ├── main.ts           # Entry point
│   ├── types/            # Type definitions
│   │   └── index.ts
│   └── utils/
│       └── helpers.ts
├── index.html
├── package.json
├── tsconfig.json         # TypeScript config
└── vite.config.ts        # Vite config (TypeScript!)
```

**Webpack with TypeScript:**

For existing Webpack projects, add TypeScript support:

```bash
npm install --save-dev typescript ts-loader
```

**webpack.config.js:**

```javascript
module.exports = {
    entry: './src/index.ts',
    module: {
        rules: [
            {
                test: /\.ts$/,
                use: 'ts-loader',
                exclude: /node_modules/
            }
        ]
    },
    resolve: {
        extensions: ['.ts', '.js']
    },
    output: {
        filename: 'bundle.js',
        path: path.resolve(__dirname, 'dist')
    }
};
```

### TypeScript in Frontend Projects

**React + TypeScript:**

```typescript
// src/components/Button.tsx
import React from 'react';

interface ButtonProps {
    label: string;
    onClick: () => void;
    variant?: 'primary' | 'secondary' | 'danger';
    disabled?: boolean;
}

const Button: React.FC<ButtonProps> = ({ 
    label, 
    onClick, 
    variant = 'primary',
    disabled = false 
}) => {
    return (
        <button 
            className={`btn btn-${variant}`}
            onClick={onClick}
            disabled={disabled}
        >
            {label}
        </button>
    );
};

export default Button;
```

**Using the component:**

```typescript
// src/App.tsx
import Button from './components/Button';

function App() {
    const handleClick = () => {
        console.log('Button clicked!');
    };

    return (
        <div>
            <Button 
                label="Click Me" 
                onClick={handleClick} 
                variant="primary"
            />
            {/* TypeScript catches errors! */}
            <Button 
                label="Submit" 
                onClick={handleClick}
                variant="invalid"  // ❌ ERROR: Type '"invalid"' is not assignable
            />
        </div>
    );
}
```

**Typing React hooks:**

```typescript
import { useState, useEffect } from 'react';

interface User {
    id: number;
    name: string;
    email: string;
}

function UserProfile() {
    // useState with type
    const [user, setUser] = useState<User | null>(null);
    const [loading, setLoading] = useState<boolean>(true);
    const [error, setError] = useState<string | null>(null);

    useEffect(() => {
        fetchUser()
            .then(data => setUser(data))
            .catch(err => setError(err.message))
            .finally(() => setLoading(false));
    }, []);

    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    if (!user) return <div>No user found</div>;

    return (
        <div>
            <h1>{user.name}</h1>
            <p>{user.email}</p>
        </div>
    );
}
```

### TypeScript in Backend (Node.js / APIs)

**Setting up Node.js with TypeScript:**

```bash
mkdir my-api
cd my-api
npm init -y
npm install typescript ts-node @types/node --save-dev
npx tsc --init
```

**tsconfig.json for Node.js:**

```json
{
    "compilerOptions": {
        "target": "ES2020",
        "module": "commonjs",
        "lib": ["ES2020"],
        "outDir": "./dist",
        "rootDir": "./src",
        "strict": true,
        "esModuleInterop": true,
        "skipLibCheck": true,
        "forceConsistentCasingInFileNames": true,
        "resolveJsonModule": true
    },
    "include": ["src/**/*"],
    "exclude": ["node_modules"]
}
```

**Express with TypeScript:**

```bash
npm install express
npm install --save-dev @types/express
```

```typescript
// src/index.ts
import express, { Request, Response, NextFunction } from 'express';

const app = express();
const PORT = 3000;

app.use(express.json());

// Typed route handler
app.get('/api/health', (req: Request, res: Response) => {
    res.json({ status: 'OK', timestamp: new Date().toISOString() });
});

// Typed request body
interface CreateUserBody {
    name: string;
    email: string;
    age?: number;
}

app.post('/api/users', (req: Request<{}, {}, CreateUserBody>, res: Response) => {
    const { name, email, age } = req.body;
    
    // TypeScript knows the types!
    console.log(`Creating user: ${name}, ${email}`);
    
    res.status(201).json({
        id: Math.random(),
        name,
        email,
        age: age ?? null
    });
});

app.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
});
```

**Running TypeScript in Node.js:**

```bash
# Development (with ts-node)
npx ts-node src/index.ts

# Or add to package.json
{
    "scripts": {
        "dev": "ts-node src/index.ts",
        "build": "tsc",
        "start": "node dist/index.js"
    }
}
```

---

## 2. Working with APIs

One of TypeScript's biggest strengths is safely typing API data.

### Typing API Responses

**Define interfaces for your API data:**

```typescript
// src/types/api.ts

// User entity
interface User {
    id: number;
    name: string;
    email: string;
    avatar?: string;
    createdAt: string;
}

// API response wrapper
interface ApiResponse<T> {
    success: boolean;
    data: T;
    message?: string;
}

// Paginated response
interface PaginatedResponse<T> {
    data: T[];
    pagination: {
        page: number;
        limit: number;
        total: number;
        totalPages: number;
    };
}

// Error response
interface ApiError {
    success: false;
    error: {
        code: string;
        message: string;
        details?: Record<string, string>;
    };
}

export type { User, ApiResponse, PaginatedResponse, ApiError };
```

### Creating Request/Response Interfaces

**Complete API typing example:**

```typescript
// src/types/api.ts

// Request types
interface LoginRequest {
    email: string;
    password: string;
}

interface RegisterRequest {
    name: string;
    email: string;
    password: string;
    confirmPassword: string;
}

interface UpdateUserRequest {
    name?: string;
    email?: string;
    avatar?: string;
}

// Response types
interface AuthResponse {
    user: User;
    token: string;
    expiresIn: number;
}

interface UserListResponse {
    users: User[];
    total: number;
}
```

**Typed fetch function:**

```typescript
// src/api/client.ts

async function apiRequest<T>(
    endpoint: string,
    options?: RequestInit
): Promise<T> {
    const baseUrl = 'https://api.example.com';
    
    const response = await fetch(`${baseUrl}${endpoint}`, {
        ...options,
        headers: {
            'Content-Type': 'application/json',
            ...options?.headers
        }
    });

    if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
    }

    return response.json() as Promise<T>;
}

// Usage
async function getUsers(): Promise<User[]> {
    return apiRequest<User[]>('/users');
}

async function getUser(id: number): Promise<User> {
    return apiRequest<User>(`/users/${id}`);
}

async function createUser(data: RegisterRequest): Promise<User> {
    return apiRequest<User>('/users', {
        method: 'POST',
        body: JSON.stringify(data)
    });
}

async function login(credentials: LoginRequest): Promise<AuthResponse> {
    return apiRequest<AuthResponse>('/auth/login', {
        method: 'POST',
        body: JSON.stringify(credentials)
    });
}
```

### Handling Unknown or Dynamic Data Safely

Real-world APIs can return unexpected data. TypeScript's `unknown` type helps you handle this safely.

**The problem with trusting API data:**

```typescript
// ❌ DANGEROUS: Assuming API returns correct data
async function getUser(id: number): Promise<User> {
    const response = await fetch(`/api/users/${id}`);
    const data = await response.json();  // Type: any
    return data as User;  // Trusting blindly!
}

// What if API returns: { error: "Not found" }?
const user = await getUser(999);
console.log(user.name.toUpperCase());  // Runtime crash!
```

**Safe approach with validation:**

```typescript
// Type guard function
function isUser(data: unknown): data is User {
    return (
        typeof data === 'object' &&
        data !== null &&
        'id' in data &&
        'name' in data &&
        'email' in data &&
        typeof (data as User).id === 'number' &&
        typeof (data as User).name === 'string' &&
        typeof (data as User).email === 'string'
    );
}

// Safe fetch function
async function getUser(id: number): Promise<User | null> {
    const response = await fetch(`/api/users/${id}`);
    const data: unknown = await response.json();
    
    if (isUser(data)) {
        return data;  // TypeScript knows this is User
    }
    
    console.error('Invalid user data received:', data);
    return null;
}

// Usage
const user = await getUser(1);
if (user) {
    console.log(user.name);  // Safe!
}
```

**Using Zod for runtime validation (recommended):**

```bash
npm install zod
```

```typescript
import { z } from 'zod';

// Define schema
const UserSchema = z.object({
    id: z.number(),
    name: z.string(),
    email: z.string().email(),
    avatar: z.string().optional(),
    createdAt: z.string()
});

// Infer TypeScript type from schema
type User = z.infer<typeof UserSchema>;

// Validate API response
async function getUser(id: number): Promise<User> {
    const response = await fetch(`/api/users/${id}`);
    const data = await response.json();
    
    // Throws if validation fails
    return UserSchema.parse(data);
}

// Safe parse (doesn't throw)
async function getUserSafe(id: number): Promise<User | null> {
    const response = await fetch(`/api/users/${id}`);
    const data = await response.json();
    
    const result = UserSchema.safeParse(data);
    
    if (result.success) {
        return result.data;
    }
    
    console.error('Validation failed:', result.error);
    return null;
}
```

---

## 3. Classes & OOP in TypeScript

TypeScript enhances JavaScript classes with proper typing and access modifiers.

### Classes with Types

```typescript
class User {
    // Properties with types
    id: number;
    name: string;
    email: string;
    
    // Constructor
    constructor(id: number, name: string, email: string) {
        this.id = id;
        this.name = name;
        this.email = email;
    }
    
    // Method with return type
    getDisplayName(): string {
        return `${this.name} (${this.email})`;
    }
    
    // Method with parameters
    updateEmail(newEmail: string): void {
        this.email = newEmail;
    }
}

const user = new User(1, 'Alice', 'alice@example.com');
console.log(user.getDisplayName());  // "Alice (alice@example.com)"
```

### Access Modifiers (public, private, protected)

TypeScript provides three access modifiers:

| Modifier | Class | Subclass | Outside |
|----------|-------|----------|---------|
| `public` | ✅ | ✅ | ✅ |
| `protected` | ✅ | ✅ | ❌ |
| `private` | ✅ | ❌ | ❌ |

```typescript
class BankAccount {
    public accountNumber: string;      // Accessible everywhere
    protected balance: number;         // Accessible in class and subclasses
    private pin: string;               // Only accessible in this class

    constructor(accountNumber: string, initialBalance: number, pin: string) {
        this.accountNumber = accountNumber;
        this.balance = initialBalance;
        this.pin = pin;
    }

    public getBalance(): number {
        return this.balance;
    }

    public withdraw(amount: number, enteredPin: string): boolean {
        if (!this.validatePin(enteredPin)) {
            return false;
        }
        
        if (amount > this.balance) {
            return false;
        }
        
        this.balance -= amount;
        return true;
    }

    private validatePin(enteredPin: string): boolean {
        return this.pin === enteredPin;
    }
}

const account = new BankAccount('123456', 1000, '1234');

console.log(account.accountNumber);  // ✅ OK (public)
console.log(account.getBalance());   // ✅ OK (public method)
console.log(account.balance);        // ❌ ERROR (protected)
console.log(account.pin);            // ❌ ERROR (private)
account.validatePin('1234');         // ❌ ERROR (private method)
```

**Subclass accessing protected members:**

```typescript
class SavingsAccount extends BankAccount {
    private interestRate: number;

    constructor(accountNumber: string, initialBalance: number, pin: string, interestRate: number) {
        super(accountNumber, initialBalance, pin);
        this.interestRate = interestRate;
    }

    public addInterest(): void {
        // Can access protected 'balance' from parent
        const interest = this.balance * this.interestRate;
        this.balance += interest;
    }

    public showBalance(): void {
        console.log(`Balance: $${this.balance}`);  // ✅ OK (protected)
        // console.log(this.pin);  // ❌ ERROR (private in parent)
    }
}
```

### Constructor Shorthand

TypeScript allows declaring and initializing properties directly in the constructor:

```typescript
// ❌ Verbose way
class User {
    public id: number;
    public name: string;
    private email: string;

    constructor(id: number, name: string, email: string) {
        this.id = id;
        this.name = name;
        this.email = email;
    }
}

// ✅ Shorthand (same result!)
class User {
    constructor(
        public id: number,
        public name: string,
        private email: string
    ) {
        // Properties are automatically created and assigned
    }
}

const user = new User(1, 'Alice', 'alice@example.com');
console.log(user.id);    // 1
console.log(user.name);  // "Alice"
```

### Getters and Setters

```typescript
class Product {
    private _price: number;
    private _discount: number = 0;

    constructor(
        public name: string,
        price: number
    ) {
        this._price = price;
    }

    // Getter
    get price(): number {
        return this._price - (this._price * this._discount);
    }

    // Setter with validation
    set price(value: number) {
        if (value < 0) {
            throw new Error('Price cannot be negative');
        }
        this._price = value;
    }

    get discount(): number {
        return this._discount * 100;  // Return as percentage
    }

    set discount(percentage: number) {
        if (percentage < 0 || percentage > 100) {
            throw new Error('Discount must be between 0 and 100');
        }
        this._discount = percentage / 100;
    }
}

const product = new Product('Laptop', 1000);

console.log(product.price);    // 1000

product.discount = 20;         // Set 20% discount
console.log(product.price);    // 800

product.price = 1200;          // Update price
console.log(product.price);    // 960 (1200 - 20%)

product.price = -100;          // ❌ Throws error
```

### Implementing Interfaces

Classes can implement interfaces to ensure they follow a contract:

```typescript
interface Loggable {
    log(): void;
}

interface Serializable {
    toJSON(): string;
}

class User implements Loggable, Serializable {
    constructor(
        public id: number,
        public name: string,
        public email: string
    ) {}

    log(): void {
        console.log(`User: ${this.name} (${this.email})`);
    }

    toJSON(): string {
        return JSON.stringify({
            id: this.id,
            name: this.name,
            email: this.email
        });
    }
}

const user = new User(1, 'Alice', 'alice@example.com');
user.log();                    // "User: Alice (alice@example.com)"
console.log(user.toJSON());    // '{"id":1,"name":"Alice","email":"alice@example.com"}'
```

---

## 4. Generics

Generics allow you to write **flexible, reusable code** that works with multiple types while maintaining type safety.

### What Generics Are

Without generics, you'd need to write separate functions for each type:

```typescript
// ❌ Without generics - repetitive
function getFirstNumber(arr: number[]): number | undefined {
    return arr[0];
}

function getFirstString(arr: string[]): string | undefined {
    return arr[0];
}

function getFirstUser(arr: User[]): User | undefined {
    return arr[0];
}
```

With generics, one function handles all types:

```typescript
// ✅ With generics - reusable
function getFirst<T>(arr: T[]): T | undefined {
    return arr[0];
}

// Usage - TypeScript infers the type
const firstNumber = getFirst([1, 2, 3]);        // Type: number | undefined
const firstString = getFirst(['a', 'b', 'c']);  // Type: string | undefined
const firstUser = getFirst(users);              // Type: User | undefined

// Or explicitly specify
const first = getFirst<number>([1, 2, 3]);
```

The `<T>` is a **type parameter** — a placeholder for a type that's determined when the function is called.

### Generic Functions

```typescript
// Identity function
function identity<T>(value: T): T {
    return value;
}

const num = identity(42);        // Type: number (inferred as 42)
const str = identity('hello');   // Type: string (inferred as "hello")

// Generic function with multiple type parameters
function pair<T, U>(first: T, second: U): [T, U] {
    return [first, second];
}

const result = pair('age', 25);  // Type: [string, number]
const result2 = pair(true, { name: 'Alice' });  // Type: [boolean, { name: string }]

// Generic with constraints
function getLength<T extends { length: number }>(item: T): number {
    return item.length;
}

getLength('hello');      // ✅ 5 (strings have .length)
getLength([1, 2, 3]);    // ✅ 3 (arrays have .length)
getLength({ length: 10 }); // ✅ 10 (objects with .length)
getLength(123);          // ❌ ERROR: number doesn't have .length
```

### Generic Interfaces

```typescript
// Generic interface
interface Container<T> {
    value: T;
    getValue(): T;
    setValue(value: T): void;
}

// Implementation
class Box<T> implements Container<T> {
    constructor(public value: T) {}

    getValue(): T {
        return this.value;
    }

    setValue(value: T): void {
        this.value = value;
    }
}

const numberBox = new Box<number>(100);
numberBox.setValue(200);
console.log(numberBox.getValue());  // 200

const stringBox = new Box<string>('Hello');
console.log(stringBox.getValue());  // "Hello"
```

**Generic interface for API responses:**

```typescript
interface ApiResponse<T> {
    success: boolean;
    data: T;
    error?: string;
    timestamp: number;
}

interface User {
    id: number;
    name: string;
}

interface Product {
    id: number;
    name: string;
    price: number;
}

// Same interface, different data types
const userResponse: ApiResponse<User> = {
    success: true,
    data: { id: 1, name: 'Alice' },
    timestamp: Date.now()
};

const productResponse: ApiResponse<Product[]> = {
    success: true,
    data: [
        { id: 1, name: 'Laptop', price: 999 },
        { id: 2, name: 'Phone', price: 699 }
    ],
    timestamp: Date.now()
};
```

### Real-World Use Cases

**1. API Helper Functions:**

```typescript
class ApiClient {
    private baseUrl: string;

    constructor(baseUrl: string) {
        this.baseUrl = baseUrl;
    }

    async get<T>(endpoint: string): Promise<T> {
        const response = await fetch(`${this.baseUrl}${endpoint}`);
        return response.json() as Promise<T>;
    }

    async post<TRequest, TResponse>(
        endpoint: string, 
        data: TRequest
    ): Promise<TResponse> {
        const response = await fetch(`${this.baseUrl}${endpoint}`, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(data)
        });
        return response.json() as Promise<TResponse>;
    }

    async put<TRequest, TResponse>(
        endpoint: string, 
        data: TRequest
    ): Promise<TResponse> {
        const response = await fetch(`${this.baseUrl}${endpoint}`, {
            method: 'PUT',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(data)
        });
        return response.json() as Promise<TResponse>;
    }

    async delete<T>(endpoint: string): Promise<T> {
        const response = await fetch(`${this.baseUrl}${endpoint}`, {
            method: 'DELETE'
        });
        return response.json() as Promise<T>;
    }
}

// Usage
const api = new ApiClient('https://api.example.com');

// TypeScript knows the return types!
const users = await api.get<User[]>('/users');
const user = await api.get<User>('/users/1');

const newUser = await api.post<CreateUserRequest, User>(
    '/users',
    { name: 'Alice', email: 'alice@example.com' }
);
```

**2. Reusable Storage Utility:**

```typescript
class LocalStorage<T> {
    constructor(private key: string) {}

    get(): T | null {
        const item = localStorage.getItem(this.key);
        return item ? JSON.parse(item) : null;
    }

    set(value: T): void {
        localStorage.setItem(this.key, JSON.stringify(value));
    }

    remove(): void {
        localStorage.removeItem(this.key);
    }
}

// Type-safe local storage
interface UserPreferences {
    theme: 'light' | 'dark';
    fontSize: number;
    notifications: boolean;
}

const preferences = new LocalStorage<UserPreferences>('user-prefs');

preferences.set({
    theme: 'dark',
    fontSize: 14,
    notifications: true
});

const prefs = preferences.get();
if (prefs) {
    console.log(prefs.theme);  // TypeScript knows this is 'light' | 'dark'
}
```

**3. Generic State Management:**

```typescript
type Listener<T> = (state: T) => void;

class Store<T> {
    private state: T;
    private listeners: Listener<T>[] = [];

    constructor(initialState: T) {
        this.state = initialState;
    }

    getState(): T {
        return this.state;
    }

    setState(newState: Partial<T>): void {
        this.state = { ...this.state, ...newState };
        this.notify();
    }

    subscribe(listener: Listener<T>): () => void {
        this.listeners.push(listener);
        return () => {
            this.listeners = this.listeners.filter(l => l !== listener);
        };
    }

    private notify(): void {
        this.listeners.forEach(listener => listener(this.state));
    }
}

// Usage
interface AppState {
    user: User | null;
    isLoading: boolean;
    error: string | null;
}

const store = new Store<AppState>({
    user: null,
    isLoading: false,
    error: null
});

store.subscribe(state => {
    console.log('State updated:', state);
});

store.setState({ isLoading: true });
store.setState({ user: { id: 1, name: 'Alice', email: 'alice@example.com' }, isLoading: false });
```

---

## 5. Error Handling & Best Practices

### Typing Errors

TypeScript doesn't automatically type caught errors:

```typescript
// ❌ Problem: error is 'unknown'
try {
    throw new Error('Something went wrong');
} catch (error) {
    console.log(error.message);  // ❌ ERROR: 'error' is of type 'unknown'
}
```

**Solution 1: Type assertion**

```typescript
try {
    throw new Error('Something went wrong');
} catch (error) {
    if (error instanceof Error) {
        console.log(error.message);  // ✅ OK
    }
}
```

**Solution 2: Custom error classes**

```typescript
class AppError extends Error {
    constructor(
        message: string,
        public code: string,
        public statusCode: number = 500
    ) {
        super(message);
        this.name = 'AppError';
    }
}

class NotFoundError extends AppError {
    constructor(resource: string) {
        super(`${resource} not found`, 'NOT_FOUND', 404);
        this.name = 'NotFoundError';
    }
}

class ValidationError extends AppError {
    constructor(
        message: string,
        public fields: Record<string, string>
    ) {
        super(message, 'VALIDATION_ERROR', 400);
        this.name = 'ValidationError';
    }
}

// Usage
function getUser(id: number): User {
    const user = users.find(u => u.id === id);
    if (!user) {
        throw new NotFoundError('User');
    }
    return user;
}

try {
    const user = getUser(999);
} catch (error) {
    if (error instanceof NotFoundError) {
        console.log(`Error ${error.statusCode}: ${error.message}`);
        // "Error 404: User not found"
    } else if (error instanceof ValidationError) {
        console.log('Validation failed:', error.fields);
    } else if (error instanceof Error) {
        console.log('Unknown error:', error.message);
    }
}
```

### Safe Error Handling with Result Type

Instead of throwing errors, return a Result type:

```typescript
type Result<T, E = Error> = 
    | { success: true; data: T }
    | { success: false; error: E };

function divide(a: number, b: number): Result<number, string> {
    if (b === 0) {
        return { success: false, error: 'Cannot divide by zero' };
    }
    return { success: true, data: a / b };
}

const result = divide(10, 2);

if (result.success) {
    console.log(`Result: ${result.data}`);  // TypeScript knows data exists
} else {
    console.log(`Error: ${result.error}`);  // TypeScript knows error exists
}

// With API calls
async function fetchUser(id: number): Promise<Result<User, AppError>> {
    try {
        const response = await fetch(`/api/users/${id}`);
        
        if (!response.ok) {
            if (response.status === 404) {
                return { success: false, error: new NotFoundError('User') };
            }
            return { success: false, error: new AppError('Fetch failed', 'FETCH_ERROR', response.status) };
        }
        
        const user = await response.json();
        return { success: true, data: user };
    } catch (err) {
        return { success: false, error: new AppError('Network error', 'NETWORK_ERROR') };
    }
}
```

### Avoiding `any`

**Common places where `any` sneaks in:**

```typescript
// ❌ Event handlers
element.addEventListener('click', (e: any) => { });

// ✅ Proper typing
element.addEventListener('click', (e: MouseEvent) => { });

// ❌ JSON parsing
const data = JSON.parse(jsonString);  // Type: any

// ✅ Type assertion or validation
const data = JSON.parse(jsonString) as User;
// or better: validate with Zod

// ❌ Third-party libraries without types
import something from 'untyped-library';

// ✅ Install types or create declaration
npm install @types/untyped-library
// or create: untyped-library.d.ts
```

**Configuring strict mode to catch `any`:**

```json
// tsconfig.json
{
    "compilerOptions": {
        "strict": true,
        "noImplicitAny": true,
        "noExplicitAny": true  // (custom rule, use ESLint)
    }
}
```

### Organizing Types

**Folder structure for TypeScript projects:**

```
src/
├── types/                    # Shared type definitions
│   ├── index.ts              # Re-exports all types
│   ├── api.ts                # API-related types
│   ├── models.ts             # Domain models
│   └── common.ts             # Common/utility types
│
├── api/                      # API client and helpers
│   ├── client.ts
│   └── endpoints/
│       ├── users.ts
│       └── products.ts
│
├── components/               # UI components
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.types.ts   # Component-specific types
│   │   └── index.ts
│   └── ...
│
├── hooks/                    # Custom hooks
│   └── useUser.ts
│
├── utils/                    # Utility functions
│   ├── validation.ts
│   └── formatting.ts
│
├── services/                 # Business logic
│   └── userService.ts
│
└── index.ts                  # App entry point
```

**Type organization patterns:**

```typescript
// src/types/index.ts - Re-export all types
export * from './api';
export * from './models';
export * from './common';

// src/types/models.ts - Domain models
export interface User {
    id: number;
    name: string;
    email: string;
}

export interface Product {
    id: number;
    name: string;
    price: number;
}

// src/types/api.ts - API types
import type { User, Product } from './models';

export interface ApiResponse<T> {
    success: boolean;
    data: T;
}

export interface PaginatedResponse<T> extends ApiResponse<T[]> {
    pagination: Pagination;
}

// src/types/common.ts - Utility types
export type Nullable<T> = T | null;
export type Optional<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>;
```

**Using the types:**

```typescript
// src/services/userService.ts
import type { User, ApiResponse, CreateUserRequest } from '../types';
import { apiClient } from '../api/client';

export async function getUser(id: number): Promise<User> {
    const response = await apiClient.get<ApiResponse<User>>(`/users/${id}`);
    return response.data;
}
```

---

## 6. Mini Project: Task Manager API

Let's build a simple, fully-typed Task Manager to demonstrate everything we've learned.

### Project Structure

```
task-manager/
├── src/
│   ├── types/
│   │   └── index.ts
│   ├── models/
│   │   └── Task.ts
│   ├── services/
│   │   └── TaskService.ts
│   ├── utils/
│   │   └── validation.ts
│   └── index.ts
├── package.json
└── tsconfig.json
```

### Type Definitions

```typescript
// src/types/index.ts

export type TaskStatus = 'pending' | 'in-progress' | 'completed';
export type TaskPriority = 'low' | 'medium' | 'high';

export interface Task {
    id: string;
    title: string;
    description?: string;
    status: TaskStatus;
    priority: TaskPriority;
    dueDate?: Date;
    createdAt: Date;
    updatedAt: Date;
}

export interface CreateTaskInput {
    title: string;
    description?: string;
    priority?: TaskPriority;
    dueDate?: Date;
}

export interface UpdateTaskInput {
    title?: string;
    description?: string;
    status?: TaskStatus;
    priority?: TaskPriority;
    dueDate?: Date;
}

export interface TaskFilters {
    status?: TaskStatus;
    priority?: TaskPriority;
    search?: string;
}

export type Result<T, E = Error> = 
    | { success: true; data: T }
    | { success: false; error: E };
```

### Task Model

```typescript
// src/models/Task.ts
import { Task, CreateTaskInput, UpdateTaskInput, TaskStatus } from '../types';

export class TaskModel implements Task {
    public id: string;
    public title: string;
    public description?: string;
    public status: TaskStatus;
    public priority: Task['priority'];
    public dueDate?: Date;
    public createdAt: Date;
    public updatedAt: Date;

    constructor(input: CreateTaskInput) {
        this.id = this.generateId();
        this.title = input.title;
        this.description = input.description;
        this.status = 'pending';
        this.priority = input.priority ?? 'medium';
        this.dueDate = input.dueDate;
        this.createdAt = new Date();
        this.updatedAt = new Date();
    }

    private generateId(): string {
        return `task_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`;
    }

    update(input: UpdateTaskInput): void {
        if (input.title !== undefined) this.title = input.title;
        if (input.description !== undefined) this.description = input.description;
        if (input.status !== undefined) this.status = input.status;
        if (input.priority !== undefined) this.priority = input.priority;
        if (input.dueDate !== undefined) this.dueDate = input.dueDate;
        this.updatedAt = new Date();
    }

    isOverdue(): boolean {
        if (!this.dueDate || this.status === 'completed') {
            return false;
        }
        return new Date() > this.dueDate;
    }

    toJSON(): Task {
        return {
            id: this.id,
            title: this.title,
            description: this.description,
            status: this.status,
            priority: this.priority,
            dueDate: this.dueDate,
            createdAt: this.createdAt,
            updatedAt: this.updatedAt
        };
    }
}
```

### Task Service

```typescript
// src/services/TaskService.ts
import { TaskModel } from '../models/Task';
import { 
    Task, 
    CreateTaskInput, 
    UpdateTaskInput, 
    TaskFilters, 
    Result 
} from '../types';

export class TaskService {
    private tasks: Map<string, TaskModel> = new Map();

    create(input: CreateTaskInput): Result<Task, string> {
        if (!input.title.trim()) {
            return { success: false, error: 'Title is required' };
        }

        const task = new TaskModel(input);
        this.tasks.set(task.id, task);
        
        return { success: true, data: task.toJSON() };
    }

    getById(id: string): Result<Task, string> {
        const task = this.tasks.get(id);
        
        if (!task) {
            return { success: false, error: `Task with id ${id} not found` };
        }
        
        return { success: true, data: task.toJSON() };
    }

    getAll(filters?: TaskFilters): Task[] {
        let tasks = Array.from(this.tasks.values());

        if (filters) {
            if (filters.status) {
                tasks = tasks.filter(t => t.status === filters.status);
            }
            if (filters.priority) {
                tasks = tasks.filter(t => t.priority === filters.priority);
            }
            if (filters.search) {
                const search = filters.search.toLowerCase();
                tasks = tasks.filter(t => 
                    t.title.toLowerCase().includes(search) ||
                    t.description?.toLowerCase().includes(search)
                );
            }
        }

        return tasks.map(t => t.toJSON());
    }

    update(id: string, input: UpdateTaskInput): Result<Task, string> {
        const task = this.tasks.get(id);
        
        if (!task) {
            return { success: false, error: `Task with id ${id} not found` };
        }

        if (input.title !== undefined && !input.title.trim()) {
            return { success: false, error: 'Title cannot be empty' };
        }

        task.update(input);
        return { success: true, data: task.toJSON() };
    }

    delete(id: string): Result<void, string> {
        if (!this.tasks.has(id)) {
            return { success: false, error: `Task with id ${id} not found` };
        }

        this.tasks.delete(id);
        return { success: true, data: undefined };
    }

    getOverdueTasks(): Task[] {
        return Array.from(this.tasks.values())
            .filter(t => t.isOverdue())
            .map(t => t.toJSON());
    }

    getStats(): TaskStats {
        const tasks = Array.from(this.tasks.values());
        
        return {
            total: tasks.length,
            pending: tasks.filter(t => t.status === 'pending').length,
            inProgress: tasks.filter(t => t.status === 'in-progress').length,
            completed: tasks.filter(t => t.status === 'completed').length,
            overdue: tasks.filter(t => t.isOverdue()).length
        };
    }
}

interface TaskStats {
    total: number;
    pending: number;
    inProgress: number;
    completed: number;
    overdue: number;
}
```

### Main Application

```typescript
// src/index.ts
import { TaskService } from './services/TaskService';

const taskService = new TaskService();

// Create tasks
const result1 = taskService.create({
    title: 'Learn TypeScript',
    description: 'Complete the TypeScript course',
    priority: 'high'
});

if (result1.success) {
    console.log('Created task:', result1.data);
}

taskService.create({
    title: 'Build a project',
    description: 'Apply TypeScript knowledge',
    priority: 'medium',
    dueDate: new Date('2024-12-31')
});

taskService.create({
    title: 'Review code',
    priority: 'low'
});

// Get all tasks
console.log('\nAll tasks:');
const allTasks = taskService.getAll();
allTasks.forEach(task => {
    console.log(`- [${task.status}] ${task.title} (${task.priority})`);
});

// Filter tasks
console.log('\nHigh priority tasks:');
const highPriority = taskService.getAll({ priority: 'high' });
highPriority.forEach(task => console.log(`- ${task.title}`));

// Update a task
if (result1.success) {
    const updateResult = taskService.update(result1.data.id, {
        status: 'in-progress'
    });
    
    if (updateResult.success) {
        console.log('\nUpdated task:', updateResult.data);
    }
}

// Get statistics
console.log('\nTask Statistics:');
const stats = taskService.getStats();
console.log(`Total: ${stats.total}`);
console.log(`Pending: ${stats.pending}`);
console.log(`In Progress: ${stats.inProgress}`);
console.log(`Completed: ${stats.completed}`);
```

### Output

```
Created task: {
    id: 'task_1234567890_abc123',
    title: 'Learn TypeScript',
    description: 'Complete the TypeScript course',
    status: 'pending',
    priority: 'high',
    createdAt: 2024-01-15T10:30:00.000Z,
    updatedAt: 2024-01-15T10:30:00.000Z
}

All tasks:
- [pending] Learn TypeScript (high)
- [pending] Build a project (medium)
- [pending] Review code (low)

High priority tasks:
- Learn TypeScript

Updated task: {
    id: 'task_1234567890_abc123',
    title: 'Learn TypeScript',
    status: 'in-progress',
    ...
}

Task Statistics:
Total: 3
Pending: 2
In Progress: 1
Completed: 0
```

---

## Summary

In this chapter, you learned:

✅ **Modern tooling** — Setting up TypeScript with Vite, Webpack, React, and Node.js  
✅ **API handling** — Typing requests, responses, and handling unknown data safely  
✅ **Classes & OOP** — Access modifiers, constructors, getters/setters, and implementing interfaces  
✅ **Generics** — Creating flexible, reusable, type-safe functions and classes  
✅ **Error handling** — Custom error classes, Result types, and safe error patterns  
✅ **Best practices** — Avoiding `any`, organizing types, and project structure  
✅ **Mini project** — Building a complete typed application  

You are now **job-ready with TypeScript**! You can confidently use it in real frontend and backend projects.

---

## Final Practice Exercises

1. Extend the Task Manager with categories/tags (many-to-many relationship)
2. Add a generic `Repository<T>` class for CRUD operations
3. Create a typed Express API for the Task Manager
4. Add Zod validation for all inputs
5. Implement proper error handling with custom error classes

**Congratulations!** You've completed the TypeScript course. Keep building projects to solidify your knowledge! 🎉

---

🎉 **Congratulations!** You have completed all 3 chapters of the TypeScript course!

You are now equipped with:
- ✅ TypeScript fundamentals and core types
- ✅ Functions, interfaces, and advanced type features
- ✅ Real-world application skills for frontend and backend

Keep practicing by building projects, and you'll become a TypeScript expert in no time!