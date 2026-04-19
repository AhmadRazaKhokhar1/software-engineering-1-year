# Chapter 1: TypeScript Fundamentals & Core Types

## Introduction

Welcome to TypeScript! In this chapter, you'll discover why TypeScript has become the industry standard for building scalable JavaScript applications. We'll start from the very basics and build a solid foundation of core types, type inference, and essential TypeScript concepts.

By the end of this chapter, you'll understand how TypeScript prevents bugs, improves code quality, and makes development faster through intelligent autocompletion and type checking.

---

## 1. Introduction to TypeScript

### What is TypeScript?

TypeScript is a **superset of JavaScript** — that means every valid JavaScript code is also valid TypeScript code. TypeScript adds **optional static typing** to JavaScript, allowing you to specify what type of data a variable, parameter, or return value should hold.

Think of TypeScript as "JavaScript with guardrails." It catches errors **before you run your code**, during development.

**Key Points:**
- TypeScript compiles down to JavaScript
- Browsers don't run TypeScript directly — it must be transpiled to JavaScript
- TypeScript adds type annotations, interfaces, and modern JavaScript features
- TypeScript is developed and maintained by Microsoft

### Why TypeScript Over JavaScript?

JavaScript is dynamically typed, meaning you can assign any value to any variable at any time:

```javascript
// JavaScript - No errors until runtime
let userId = 123;
userId = "abc123";  // Allowed, but might cause bugs later
userId.toFixed(2);  // ERROR at runtime! Strings don't have .toFixed()
```

TypeScript prevents this:

```typescript
// TypeScript - Error caught during development
let userId: number = 123;
userId = "abc123";  // ❌ ERROR: Type 'string' is not assignable to type 'number'
```

**Benefits of TypeScript:**

1. **Catch errors early** — before code runs
2. **Better IDE support** — intelligent autocomplete, inline documentation
3. **Refactoring safety** — renaming or restructuring code is much safer
4. **Self-documenting code** — types serve as inline documentation
5. **Team collaboration** — clear contracts between different parts of the codebase
6. **Scales better** — essential for large projects with multiple developers

### TypeScript vs JavaScript: Compile-time vs Runtime

| Aspect | JavaScript | TypeScript |
|--------|-----------|-----------|
| Type checking | Runtime (errors appear when code runs) | Compile-time (errors appear while coding) |
| Catches bugs | Only when code executes | Before code even runs |
| Tooling | Basic | Advanced (autocomplete, refactoring) |
| Learning curve | Easier initially | Slight learning curve |
| Browser support | Native | Must be compiled to JavaScript |

**Example:**

```typescript
// TypeScript catches this BEFORE running
function greet(name: string) {
    return `Hello, ${name.toUpperCase()}`;
}

greet(42);  // ❌ ERROR during development
// Argument of type 'number' is not assignable to parameter of type 'string'
```

```javascript
// JavaScript only catches this WHEN running
function greet(name) {
    return `Hello, ${name.toUpperCase()}`;
}

greet(42);  // ✅ No error until runtime
// Runtime error: name.toUpperCase is not a function
```

### Real-World Problems TypeScript Solves

**Problem 1: API Response Handling**

```javascript
// JavaScript - No type safety
fetch('/api/user')
    .then(res => res.json())
    .then(user => {
        console.log(user.naem);  // Typo! But JavaScript won't warn you
        // undefined printed, bug goes unnoticed
    });
```

```typescript
// TypeScript - Type safety
interface User {
    name: string;
    email: string;
    age: number;
}

fetch('/api/user')
    .then(res => res.json())
    .then((user: User) => {
        console.log(user.naem);  // ❌ ERROR: Property 'naem' does not exist
        console.log(user.name);  // ✅ Correct
    });
```

**Problem 2: Function Parameters**

```javascript
// JavaScript - Easy to pass wrong arguments
function calculatePrice(price, tax, discount) {
    return price + (price * tax) - discount;
}

calculatePrice("100", 0.2, 10);  // "100" is a string! Bug!
// Returns "1000.2-10" = NaN (silently fails)
```

```typescript
// TypeScript - Prevents wrong types
function calculatePrice(price: number, tax: number, discount: number): number {
    return price + (price * tax) - discount;
}

calculatePrice("100", 0.2, 10);  // ❌ ERROR at development time
// Argument of type 'string' is not assignable to parameter of type 'number'
```

**Problem 3: Refactoring**

When you rename a property in JavaScript, you must manually find and update every reference. TypeScript does this automatically through IDE integration.

---

## 2. Setting Up TypeScript

### Installing TypeScript

TypeScript requires Node.js to be installed. Once you have Node.js, install TypeScript globally:

```bash
# Install TypeScript globally
npm install -g typescript

# Check installation
tsc --version
```

For project-specific installation (recommended for real projects):

```bash
# Initialize a new Node project
npm init -y

# Install TypeScript as a dev dependency
npm install --save-dev typescript

# Check version
npx tsc --version
```

### TypeScript Compiler (tsc)

The TypeScript compiler (`tsc`) converts `.ts` files into `.js` files that browsers and Node.js can execute.

**Basic Commands:**

```bash
# Compile a single file
tsc filename.ts

# Compile all TypeScript files in a project
tsc

# Watch mode (recompile on file changes)
tsc --watch

# or
tsc -w
```

### Your First TypeScript File

Create a file called `index.ts`:

```typescript
// index.ts
const message: string = "Hello, TypeScript!";
console.log(message);

function add(a: number, b: number): number {
    return a + b;
}

console.log(add(5, 10));  // 15
```

Compile it:

```bash
tsc index.ts
```

This creates `index.js`:

```javascript
// index.js (compiled output)
var message = "Hello, TypeScript!";
console.log(message);

function add(a, b) {
    return a + b;
}

console.log(add(5, 10));
```

Run the JavaScript file:

```bash
node index.js
```

**Output:**
```
Hello, TypeScript!
15
```

### Understanding tsconfig.json

For real projects, you don't want to manually specify compiler options every time. The `tsconfig.json` file stores TypeScript configuration.

**Generate tsconfig.json:**

```bash
tsc --init
```

This creates a `tsconfig.json` file with default settings.

**Basic tsconfig.json:**

```json
{
  "compilerOptions": {
    "target": "ES6",                    // JavaScript version to compile to
    "module": "commonjs",               // Module system
    "outDir": "./dist",                 // Output directory for compiled files
    "rootDir": "./src",                 // Source directory
    "strict": true,                     // Enable all strict type checking
    "esModuleInterop": true,            // Better module compatibility
    "skipLibCheck": true,               // Skip type checking of library files
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],              // Which files to compile
  "exclude": ["node_modules"]           // Which files to ignore
}
```

**Project Structure:**

```
my-typescript-project/
│
├── src/
│   └── index.ts          # Your TypeScript files
│
├── dist/                 # Compiled JavaScript (auto-generated)
│   └── index.js
│
├── tsconfig.json         # TypeScript configuration
└── package.json
```

Now just run `tsc` and all `.ts` files in `src/` will compile to `dist/`.

---

## 3. Basic Types

TypeScript provides several built-in types. Let's explore the most important ones.

### number

Represents all numbers — integers, decimals, negative, etc.

```typescript
let age: number = 25;
let price: number = 99.99;
let temperature: number = -10;
let hex: number = 0xff;        // Hexadecimal
let binary: number = 0b1010;   // Binary
let octal: number = 0o744;     // Octal

age = "25";  // ❌ ERROR: Type 'string' is not assignable to type 'number'
```

### string

Represents text.

```typescript
let firstName: string = "John";
let lastName: string = 'Doe';
let greeting: string = `Hello, ${firstName}!`;  // Template literals

firstName = 123;  // ❌ ERROR
```

### boolean

Represents `true` or `false`.

```typescript
let isActive: boolean = true;
let hasPermission: boolean = false;

isActive = "true";  // ❌ ERROR: Type 'string' is not assignable to type 'boolean'
```

### any (Why to Avoid It)

`any` disables type checking — it's essentially JavaScript without TypeScript's safety.

```typescript
let randomValue: any = 10;
randomValue = "Hello";        // ✅ Allowed
randomValue = true;           // ✅ Allowed
randomValue.toUpperCase();    // ✅ No error (but might crash at runtime!)

// TypeScript won't protect you
randomValue.somethingRandom();  // ✅ No error, but will crash at runtime
```

**Why avoid `any`?**
- Defeats the purpose of TypeScript
- No autocomplete or type safety
- Easy to introduce bugs

**When is `any` acceptable?**
- When migrating JavaScript to TypeScript incrementally
- When working with truly dynamic data (very rare)

**Better alternative: `unknown`** (explained next)

### unknown

`unknown` is a type-safe alternative to `any`. It forces you to check the type before using it.

```typescript
let userInput: unknown;

userInput = 5;
userInput = "Hello";

// ❌ ERROR: Can't use it directly
let userName: string = userInput;

// ✅ Type checking required
if (typeof userInput === "string") {
    let userName: string = userInput;  // Now safe
}
```

**Rule:** Use `unknown` instead of `any` when you don't know the type ahead of time but want to maintain type safety.

### void

`void` means "no return value" — used for functions that don't return anything.

```typescript
function logMessage(message: string): void {
    console.log(message);
    // No return statement
}

const result = logMessage("Hello");
console.log(result);  // undefined
```

**When to use `void`:**
- Functions that perform actions (logging, updating UI) but don't return data
- Event handlers
- Callback functions that don't return values

### null and undefined

TypeScript treats `null` and `undefined` as distinct types.

```typescript
let nothing: null = null;
let notDefined: undefined = undefined;

nothing = undefined;  // ❌ ERROR (in strict mode)
```

**In strict mode** (`"strict": true` in tsconfig), variables cannot be `null` or `undefined` unless explicitly allowed:

```typescript
let name: string = "Alice";
name = null;  // ❌ ERROR

// Allow null or undefined
let name: string | null = "Alice";
name = null;  // ✅ OK
```

**Best Practice:** Enable strict mode and explicitly handle `null`/`undefined` using union types.

---

## 4. Variables & Type Inference

### Explicit Typing

You explicitly tell TypeScript what type a variable should be:

```typescript
let username: string = "Alice";
let score: number = 100;
let isLoggedIn: boolean = true;
```

### Implicit Typing (Type Inference)

TypeScript can **automatically infer** types based on the assigned value:

```typescript
let username = "Alice";      // TypeScript infers: string
let score = 100;             // TypeScript infers: number
let isLoggedIn = true;       // TypeScript infers: boolean

username = 42;  // ❌ ERROR: Type 'number' is not assignable to type 'string'
```

**Type Inference in Action:**

```typescript
let message = "Hello";       // inferred as string
message.toUpperCase();       // ✅ Works, TypeScript knows it's a string

let count = 10;              // inferred as number
count.toFixed(2);            // ✅ Works, TypeScript knows it's a number
```

### Type Inference Behavior

**Best Practice:** Let TypeScript infer types when obvious — only add explicit types when needed.

```typescript
// ❌ Redundant
let name: string = "Alice";

// ✅ Better (TypeScript infers string)
let name = "Alice";

// ✅ Explicit when needed
let age: number;  // Declared but not initialized
age = 25;
```

**When to use explicit typing:**
1. When declaring a variable without initializing it
2. When the inferred type is too broad
3. For function parameters (always)
4. For better readability in complex code

**Example where explicit typing is needed:**

```typescript
let userId;  // Type: any (bad!)
userId = 123;
userId = "abc";  // ✅ Allowed, but dangerous

// Better:
let userId: number;
userId = 123;
userId = "abc";  // ❌ ERROR
```

---

## 5. Arrays & Tuples

### Typed Arrays

Arrays in TypeScript can be typed to hold only specific types.

**Syntax 1: Type[]**

```typescript
let numbers: number[] = [1, 2, 3, 4, 5];
let names: string[] = ["Alice", "Bob", "Charlie"];

numbers.push(6);        // ✅ OK
numbers.push("7");      // ❌ ERROR

names.push("David");    // ✅ OK
names.push(42);         // ❌ ERROR
```

**Syntax 2: Array<Type>**

```typescript
let numbers: Array<number> = [1, 2, 3];
let names: Array<string> = ["Alice", "Bob"];
```

Both syntaxes are equivalent. `Type[]` is more common and concise.

### Union Types in Arrays

An array can hold **multiple types** using union types:

```typescript
let mixed: (number | string)[] = [1, "two", 3, "four"];

mixed.push(5);          // ✅ OK
mixed.push("six");      // ✅ OK
mixed.push(true);       // ❌ ERROR: boolean not allowed
```

**Real-world example:**

```typescript
// Array of user IDs (can be number or string depending on source)
let userIds: (number | string)[] = [101, "abc123", 202, "xyz789"];
```

### Tuples

A **tuple** is an array with a **fixed number of elements** where **each element has a specific type**.

```typescript
// Tuple: [string, number]
let person: [string, number] = ["Alice", 25];

person = [25, "Alice"];         // ❌ ERROR: Wrong order
person = ["Alice", 25, true];   // ❌ ERROR: Too many elements
```

**Accessing tuple elements:**

```typescript
let person: [string, number] = ["Alice", 25];

console.log(person[0].toUpperCase());  // "ALICE"
console.log(person[1].toFixed(1));     // "25.0"
```

**When to use tuples:**
- When you need a fixed structure (e.g., coordinates, key-value pairs)
- Function return values with multiple pieces of data
- React's `useState`: `const [count, setCount] = useState(0);`

**Example: RGB color**

```typescript
let color: [number, number, number] = [255, 128, 0];

function setColor(rgb: [number, number, number]) {
    console.log(`rgb(${rgb[0]}, ${rgb[1]}, ${rgb[2]})`);
}

setColor(color);  // rgb(255, 128, 0)
```

**Optional tuple elements:**

```typescript
let person: [string, number, boolean?] = ["Alice", 25];
// Third element (boolean) is optional
```

**Tuple with rest elements:**

```typescript
let scores: [string, ...number[]] = ["Math", 90, 85, 95];
// First element is string, rest are numbers
```

---

## 6. Objects & Type Aliases

### Typed Objects

You can specify the structure of an object:

```typescript
let user: { name: string; age: number; isActive: boolean } = {
    name: "Alice",
    age: 25,
    isActive: true
};

user.name = "Bob";      // ✅ OK
user.age = "30";        // ❌ ERROR: Type 'string' is not assignable to type 'number'
user.email = "a@b.c";   // ❌ ERROR: Property 'email' does not exist
```

### Optional Properties

Use `?` to make a property optional:

```typescript
let user: { name: string; age: number; email?: string } = {
    name: "Alice",
    age: 25
    // email is optional, so it can be omitted
};

user.email = "alice@example.com";  // ✅ OK
```

**Accessing optional properties:**

```typescript
console.log(user.email?.toUpperCase());  // Safe access with optional chaining
```

### Readonly Properties

Use `readonly` to prevent modification after initialization:

```typescript
let user: { readonly id: number; name: string } = {
    id: 101,
    name: "Alice"
};

user.name = "Bob";   // ✅ OK
user.id = 202;       // ❌ ERROR: Cannot assign to 'id' because it is a read-only property
```

**Real-world use case:**

```typescript
type Config = {
    readonly apiUrl: string;
    readonly timeout: number;
};

const config: Config = {
    apiUrl: "https://api.example.com",
    timeout: 5000
};

config.apiUrl = "https://newapi.com";  // ❌ ERROR: Can't change config
```

### Type Aliases

Repeating complex types is tedious. **Type aliases** let you create reusable type names.

**Without type alias (repetitive):**

```typescript
let user1: { name: string; age: number; email: string } = {
    name: "Alice",
    age: 25,
    email: "alice@example.com"
};

let user2: { name: string; age: number; email: string } = {
    name: "Bob",
    age: 30,
    email: "bob@example.com"
};
```

**With type alias (clean and reusable):**

```typescript
type User = {
    name: string;
    age: number;
    email: string;
};

let user1: User = {
    name: "Alice",
    age: 25,
    email: "alice@example.com"
};

let user2: User = {
    name: "Bob",
    age: 30,
    email: "bob@example.com"
};
```

**Type alias for primitives:**

```typescript
type ID = number | string;

let userId: ID = 101;
userId = "abc123";  // ✅ Both work
```

**Nested type aliases:**

```typescript
type Address = {
    street: string;
    city: string;
    zipCode: string;
};

type User = {
    name: string;
    age: number;
    address: Address;
};

const user: User = {
    name: "Alice",
    age: 25,
    address: {
        street: "123 Main St",
        city: "New York",
        zipCode: "10001"
    }
};
```

**Combining type aliases:**

```typescript
type Admin = {
    name: string;
    role: "admin";
    permissions: string[];
};

type RegularUser = {
    name: string;
    role: "user";
};

type User = Admin | RegularUser;

const admin: User = {
    name: "Alice",
    role: "admin",
    permissions: ["read", "write", "delete"]
};
```

---

## Summary

In this chapter, you learned:

✅ **What TypeScript is** and why it's better than JavaScript for large projects  
✅ **How to set up TypeScript** with `tsc` and `tsconfig.json`  
✅ **Basic types**: `number`, `string`, `boolean`, `any`, `unknown`, `void`, `null`, `undefined`  
✅ **Type inference** vs explicit typing  
✅ **Arrays and tuples** for structured collections  
✅ **Typed objects** with optional and readonly properties  
✅ **Type aliases** for reusable, clean type definitions  

You now have a solid foundation in TypeScript fundamentals. You can confidently add types to variables, functions, and objects, making your code safer and more maintainable.

---

## Practice Exercises

1. Create a `Product` type with properties: `id` (number), `name` (string), `price` (number), `inStock` (boolean)
2. Write a function that takes an array of numbers and returns the sum (with proper typing)
3. Create a tuple representing a book: `[title: string, author: string, year: number]`
4. Define a type alias for a user with optional `phoneNumber`

**Next Steps:** In Chapter 2, we'll dive into functions, interfaces, union/intersection types, and utility types to write professional-grade TypeScript.

---
