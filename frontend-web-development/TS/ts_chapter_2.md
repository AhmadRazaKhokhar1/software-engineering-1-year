# Chapter 2: Functions, Interfaces & Advanced Types

## Introduction

In Chapter 1, you learned TypeScript fundamentals — basic types, type inference, arrays, tuples, and type aliases. Now it's time to level up!

This chapter focuses on writing **professional, scalable TypeScript code**. You'll master functions with proper typing, interfaces for defining contracts, union and intersection types for flexibility, enums for constants, type assertions for special cases, and utility types for transforming existing types.

By the end of this chapter, you'll be able to design clean APIs and write TypeScript code that's both safe and elegant.

---

## 1. Functions in TypeScript

Functions are the building blocks of any application. TypeScript makes them safer by enforcing types on parameters and return values.

### Function Parameter Types

Always specify the types of function parameters:

```typescript
// ❌ JavaScript style (unsafe)
function greet(name) {
    return `Hello, ${name}!`;
}

// ✅ TypeScript style (safe)
function greet(name: string) {
    return `Hello, ${name}!`;
}

greet("Alice");   // ✅ OK
greet(42);        // ❌ ERROR: Argument of type 'number' is not assignable to parameter of type 'string'
```

**Multiple parameters:**

```typescript
function add(a: number, b: number) {
    return a + b;
}

add(5, 10);          // ✅ Returns 15
add("5", "10");      // ❌ ERROR
```

### Return Types

TypeScript can infer return types, but explicit return types improve clarity and catch bugs:

```typescript
// Inferred return type (TypeScript figures it out)
function multiply(a: number, b: number) {
    return a * b;  // Inferred as number
}

// Explicit return type (recommended for complex functions)
function multiply(a: number, b: number): number {
    return a * b;
}

// Catches bugs!
function divide(a: number, b: number): number {
    if (b === 0) {
        return "Cannot divide by zero";  // ❌ ERROR: Type 'string' is not assignable to type 'number'
    }
    return a / b;
}
```

**Void return type:**

```typescript
function logMessage(message: string): void {
    console.log(message);
    // No return statement needed
}
```

**Never return type** (function never completes):

```typescript
function throwError(message: string): never {
    throw new Error(message);
    // Function never returns normally
}

function infiniteLoop(): never {
    while (true) {
        // Never exits
    }
}
```

### Optional and Default Parameters

**Optional parameters** use `?` and must come after required parameters:

```typescript
function greet(name: string, greeting?: string): string {
    if (greeting) {
        return `${greeting}, ${name}!`;
    }
    return `Hello, ${name}!`;
}

greet("Alice");              // "Hello, Alice!"
greet("Alice", "Welcome");   // "Welcome, Alice!"
```

**Default parameters** provide a fallback value:

```typescript
function greet(name: string, greeting: string = "Hello"): string {
    return `${greeting}, ${name}!`;
}

greet("Alice");              // "Hello, Alice!"
greet("Alice", "Welcome");   // "Welcome, Alice!"
```

**Combining optional and default:**

```typescript
function createUser(
    name: string,
    age: number,
    role: string = "user",
    isActive?: boolean
) {
    return {
        name,
        age,
        role,
        isActive: isActive ?? true  // Default to true if undefined
    };
}

createUser("Alice", 25);                    // { name: "Alice", age: 25, role: "user", isActive: true }
createUser("Bob", 30, "admin");             // { name: "Bob", age: 30, role: "admin", isActive: true }
createUser("Charlie", 28, "editor", false); // { name: "Charlie", age: 28, role: "editor", isActive: false }
```

### Arrow Functions with Types

Arrow functions follow the same typing rules:

```typescript
// Basic arrow function
const add = (a: number, b: number): number => {
    return a + b;
};

// Implicit return (single expression)
const multiply = (a: number, b: number): number => a * b;

// With optional parameters
const greet = (name: string, greeting?: string): string => 
    greeting ? `${greeting}, ${name}!` : `Hello, ${name}!`;
```

**Typing arrow functions as variables:**

```typescript
// Define the function type first
type MathOperation = (a: number, b: number) => number;

const add: MathOperation = (a, b) => a + b;
const subtract: MathOperation = (a, b) => a - b;
const multiply: MathOperation = (a, b) => a * b;

// All three functions must follow the same signature
```

**Arrow functions as callbacks:**

```typescript
const numbers = [1, 2, 3, 4, 5];

// TypeScript infers parameter types from array
const doubled = numbers.map((num) => num * 2);  // [2, 4, 6, 8, 10]

// Explicit typing
const squared = numbers.map((num: number): number => num ** 2);
```

---

## 2. Interfaces

Interfaces define the **shape** of objects — they act as contracts that objects must follow.

### What Interfaces Are

An interface declares what properties and methods an object must have:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
}

const user: User = {
    id: 1,
    name: "Alice",
    email: "alice@example.com"
};

// Missing property
const badUser: User = {
    id: 2,
    name: "Bob"
    // ❌ ERROR: Property 'email' is missing
};

// Extra property
const anotherBadUser: User = {
    id: 3,
    name: "Charlie",
    email: "charlie@example.com",
    age: 25  // ❌ ERROR: Object literal may only specify known properties
};
```

**Interfaces with methods:**

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    greet(): string;
    updateEmail(newEmail: string): void;
}

const user: User = {
    id: 1,
    name: "Alice",
    email: "alice@example.com",
    greet() {
        return `Hello, I'm ${this.name}!`;
    },
    updateEmail(newEmail: string) {
        this.email = newEmail;
    }
};

console.log(user.greet());  // "Hello, I'm Alice!"
```

### Interfaces vs Type Aliases

Both can describe object shapes, but they have differences:

**Type Alias:**

```typescript
type User = {
    id: number;
    name: string;
    email: string;
};
```

**Interface:**

```typescript
interface User {
    id: number;
    name: string;
    email: string;
}
```

**Key Differences:**

| Feature | Interface | Type Alias |
|---------|-----------|------------|
| Object shapes | ✅ Yes | ✅ Yes |
| Extend/inherit | ✅ `extends` keyword | ✅ Intersection `&` |
| Declaration merging | ✅ Yes | ❌ No |
| Primitives, unions, tuples | ❌ No | ✅ Yes |
| Computed properties | ❌ No | ✅ Yes |

**Declaration merging** (only interfaces):

```typescript
interface User {
    id: number;
    name: string;
}

interface User {
    email: string;  // Merged with the above
}

// User now has: id, name, email
const user: User = {
    id: 1,
    name: "Alice",
    email: "alice@example.com"
};
```

**When to use which:**
- **Interface**: For object shapes, especially when you might need to extend them
- **Type alias**: For primitives, unions, tuples, or complex type transformations

```typescript
// Use type for unions and primitives
type ID = number | string;
type Status = "active" | "inactive" | "pending";

// Use interface for object structures
interface User {
    id: ID;
    name: string;
    status: Status;
}
```

### Object Contracts

Interfaces define contracts that functions can rely on:

```typescript
interface Printable {
    print(): void;
}

interface Savable {
    save(): boolean;
}

// Function accepts anything that is Printable
function printDocument(doc: Printable): void {
    doc.print();
}

// Object that implements the interface
const report = {
    title: "Monthly Report",
    print() {
        console.log(`Printing: ${this.title}`);
    }
};

printDocument(report);  // ✅ Works! report has a print() method
```

### Extending Interfaces

Interfaces can extend other interfaces to inherit properties:

```typescript
interface Person {
    name: string;
    age: number;
}

interface Employee extends Person {
    employeeId: number;
    department: string;
}

const employee: Employee = {
    name: "Alice",
    age: 30,
    employeeId: 12345,
    department: "Engineering"
};
```

**Extending multiple interfaces:**

```typescript
interface Person {
    name: string;
    age: number;
}

interface Contactable {
    email: string;
    phone: string;
}

interface Employee extends Person, Contactable {
    employeeId: number;
    department: string;
}

const employee: Employee = {
    name: "Alice",
    age: 30,
    email: "alice@company.com",
    phone: "555-1234",
    employeeId: 12345,
    department: "Engineering"
};
```

**Overriding properties when extending:**

```typescript
interface Animal {
    name: string;
    age: number;
}

interface Dog extends Animal {
    breed: string;
    age: number;  // Can narrow the type but not widen it
}
```

---

## 3. Union & Intersection Types

Union and intersection types give you flexibility in combining types.

### Union Types ( | )

A union type allows a value to be **one of several types**:

```typescript
// Variable can be string OR number
let id: string | number;

id = 101;        // ✅ OK
id = "ABC123";   // ✅ OK
id = true;       // ❌ ERROR: Type 'boolean' is not assignable

// Function with union parameter
function printId(id: string | number): void {
    console.log(`ID: ${id}`);
}

printId(101);        // "ID: 101"
printId("ABC123");   // "ID: ABC123"
```

**Type narrowing with unions:**

When working with unions, you often need to narrow the type before using type-specific methods:

```typescript
function printId(id: string | number): void {
    // Can't call .toUpperCase() directly — might be number
    console.log(id.toUpperCase());  // ❌ ERROR

    // Narrow the type first
    if (typeof id === "string") {
        console.log(id.toUpperCase());  // ✅ OK — TypeScript knows it's string
    } else {
        console.log(id.toFixed(2));     // ✅ OK — TypeScript knows it's number
    }
}
```

**Union with literal types:**

```typescript
type Status = "pending" | "approved" | "rejected";

function updateStatus(status: Status): void {
    console.log(`Status updated to: ${status}`);
}

updateStatus("approved");   // ✅ OK
updateStatus("unknown");    // ❌ ERROR: Argument of type '"unknown"' is not assignable
```

**Union with objects:**

```typescript
interface Circle {
    kind: "circle";
    radius: number;
}

interface Rectangle {
    kind: "rectangle";
    width: number;
    height: number;
}

type Shape = Circle | Rectangle;

function getArea(shape: Shape): number {
    if (shape.kind === "circle") {
        return Math.PI * shape.radius ** 2;
    } else {
        return shape.width * shape.height;
    }
}

getArea({ kind: "circle", radius: 5 });           // 78.54
getArea({ kind: "rectangle", width: 10, height: 5 }); // 50
```

### Intersection Types ( & )

An intersection type **combines multiple types** into one — the result must have ALL properties from ALL types:

```typescript
interface Person {
    name: string;
    age: number;
}

interface Employee {
    employeeId: number;
    department: string;
}

type EmployeePerson = Person & Employee;

const employee: EmployeePerson = {
    name: "Alice",
    age: 30,
    employeeId: 12345,
    department: "Engineering"
};

// Missing any property causes an error
const badEmployee: EmployeePerson = {
    name: "Bob",
    age: 25,
    employeeId: 67890
    // ❌ ERROR: Property 'department' is missing
};
```

**Intersection with type aliases:**

```typescript
type Timestamps = {
    createdAt: Date;
    updatedAt: Date;
};

type SoftDelete = {
    deletedAt?: Date;
    isDeleted: boolean;
};

type User = {
    id: number;
    name: string;
    email: string;
};

// Combine all three
type TrackedUser = User & Timestamps & SoftDelete;

const user: TrackedUser = {
    id: 1,
    name: "Alice",
    email: "alice@example.com",
    createdAt: new Date(),
    updatedAt: new Date(),
    isDeleted: false
};
```

### Practical Use Cases

**API Response handling:**

```typescript
// Success response
interface SuccessResponse<T> {
    success: true;
    data: T;
}

// Error response
interface ErrorResponse {
    success: false;
    error: string;
}

type ApiResponse<T> = SuccessResponse<T> | ErrorResponse;

function handleResponse(response: ApiResponse<User>): void {
    if (response.success) {
        console.log(`User: ${response.data.name}`);  // Safe access
    } else {
        console.log(`Error: ${response.error}`);
    }
}
```

**Form state:**

```typescript
type FormState = 
    | { status: "idle" }
    | { status: "loading" }
    | { status: "success"; data: any }
    | { status: "error"; message: string };

function renderForm(state: FormState): void {
    switch (state.status) {
        case "idle":
            console.log("Ready to submit");
            break;
        case "loading":
            console.log("Submitting...");
            break;
        case "success":
            console.log("Success!", state.data);
            break;
        case "error":
            console.log("Error:", state.message);
            break;
    }
}
```

---

## 4. Enums & Literal Types

Enums and literal types help you work with fixed sets of values.

### Numeric Enums

Enums define a set of named constants:

```typescript
enum Direction {
    Up,      // 0
    Down,    // 1
    Left,    // 2
    Right    // 3
}

let playerDirection: Direction = Direction.Up;
console.log(playerDirection);  // 0

// Use in functions
function move(direction: Direction): void {
    switch (direction) {
        case Direction.Up:
            console.log("Moving up!");
            break;
        case Direction.Down:
            console.log("Moving down!");
            break;
        // ...
    }
}

move(Direction.Left);  // "Moving left!" (if implemented)
```

**Custom numeric values:**

```typescript
enum StatusCode {
    OK = 200,
    Created = 201,
    BadRequest = 400,
    Unauthorized = 401,
    NotFound = 404,
    ServerError = 500
}

console.log(StatusCode.OK);        // 200
console.log(StatusCode.NotFound);  // 404
```

### String Enums

String enums are more readable and debuggable:

```typescript
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT"
}

console.log(Direction.Up);  // "UP"

enum Status {
    Pending = "PENDING",
    Approved = "APPROVED",
    Rejected = "REJECTED"
}

function handleStatus(status: Status): void {
    console.log(`Current status: ${status}`);
}

handleStatus(Status.Approved);  // "Current status: APPROVED"
```

### Literal Types

Literal types restrict a variable to **specific exact values**:

```typescript
// String literal type
let direction: "up" | "down" | "left" | "right";

direction = "up";      // ✅ OK
direction = "UP";      // ❌ ERROR: Type '"UP"' is not assignable
direction = "forward"; // ❌ ERROR

// Number literal type
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;

let roll: DiceRoll = 4;  // ✅ OK
roll = 7;                // ❌ ERROR

// Boolean literal (less common)
type True = true;
let mustBeTrue: True = true;   // ✅ OK
mustBeTrue = false;            // ❌ ERROR
```

**Literal types with type aliases:**

```typescript
type Theme = "light" | "dark" | "system";
type Size = "small" | "medium" | "large";

interface Button {
    label: string;
    theme: Theme;
    size: Size;
}

const button: Button = {
    label: "Click me",
    theme: "dark",
    size: "medium"
};
```

### When (and When Not) to Use Enums

**When to use enums:**
- When you need reverse mapping (value → name)
- When integrating with code that expects numeric constants
- When you want a named group of related constants

**When to use literal types instead:**
- When you just need a set of string values
- When you want smaller bundle size (enums generate extra JavaScript code)
- When you're working with API values that are already strings

**Comparison:**

```typescript
// Enum - generates JavaScript code
enum StatusEnum {
    Pending = "PENDING",
    Approved = "APPROVED",
    Rejected = "REJECTED"
}

// Literal type - no JavaScript output, just type checking
type StatusLiteral = "PENDING" | "APPROVED" | "REJECTED";

// Both work similarly
function handleStatus1(status: StatusEnum): void {
    console.log(status);
}

function handleStatus2(status: StatusLiteral): void {
    console.log(status);
}

handleStatus1(StatusEnum.Approved);
handleStatus2("APPROVED");
```

**Modern recommendation:** Prefer **literal types** or **const objects** over enums for most cases:

```typescript
// Const object pattern (modern alternative to enums)
const Status = {
    Pending: "PENDING",
    Approved: "APPROVED",
    Rejected: "REJECTED"
} as const;

type Status = typeof Status[keyof typeof Status];
// Type is: "PENDING" | "APPROVED" | "REJECTED"

function handleStatus(status: Status): void {
    console.log(status);
}

handleStatus(Status.Approved);  // ✅ OK
handleStatus("PENDING");        // ✅ OK
```

---

## 5. Type Assertions

Type assertions tell TypeScript "trust me, I know what this type is."

### The `as` Keyword

```typescript
// TypeScript doesn't know what type this is
const input = document.getElementById("username");
// Type: HTMLElement | null

// Assert that it's an HTMLInputElement
const usernameInput = document.getElementById("username") as HTMLInputElement;
usernameInput.value = "Alice";  // ✅ Now we can access .value
```

**Alternative syntax (not for JSX):**

```typescript
const usernameInput = <HTMLInputElement>document.getElementById("username");
```

### When Assertions Are Necessary

**1. DOM elements:**

```typescript
// TypeScript doesn't know the specific element type
const canvas = document.getElementById("myCanvas") as HTMLCanvasElement;
const ctx = canvas.getContext("2d");

const form = document.querySelector("form") as HTMLFormElement;
form.submit();
```

**2. API responses with known structure:**

```typescript
interface User {
    id: number;
    name: string;
    email: string;
}

// You know the API returns a User
const response = await fetch("/api/user");
const user = await response.json() as User;

console.log(user.name);  // ✅ TypeScript knows user has .name
```

**3. Narrowing from a broader type:**

```typescript
interface Bird {
    fly(): void;
    layEggs(): void;
}

interface Fish {
    swim(): void;
    layEggs(): void;
}

function getSmallPet(): Bird | Fish {
    // ...implementation
}

const pet = getSmallPet();

// Assert when you know it's a Fish
(pet as Fish).swim();
```

### Common Mistakes

**Mistake 1: Asserting incompatible types**

```typescript
const value: string = "hello";
const num = value as number;  // ❌ ERROR: Cannot convert string to number directly

// If you really need to (not recommended):
const num = value as unknown as number;  // ⚠️ Dangerous! Bypasses type checking
```

**Mistake 2: Using assertions instead of type guards**

```typescript
// ❌ Bad: Asserting without checking
function processValue(value: string | number): void {
    const str = value as string;
    console.log(str.toUpperCase());  // Will crash if value is number!
}

// ✅ Good: Use type guard
function processValue(value: string | number): void {
    if (typeof value === "string") {
        console.log(value.toUpperCase());  // Safe
    } else {
        console.log(value.toFixed(2));     // Safe
    }
}
```

**Mistake 3: Overusing assertions**

```typescript
// ❌ Bad: Too many assertions
const data = JSON.parse(response) as unknown as User as { name: string };

// ✅ Good: Proper validation
interface User {
    id: number;
    name: string;
}

function isUser(data: unknown): data is User {
    return typeof data === "object" 
        && data !== null 
        && "id" in data 
        && "name" in data;
}

const data = JSON.parse(response);
if (isUser(data)) {
    console.log(data.name);  // Safe and validated
}
```

**Non-null assertion operator (`!`):**

```typescript
// Tells TypeScript: "I know this is not null or undefined"
const element = document.getElementById("app")!;
// Type: HTMLElement (not HTMLElement | null)

// ⚠️ Use sparingly! If element doesn't exist, you'll get a runtime error
```

---

## 6. Utility Types (Introduction)

TypeScript provides built-in utility types that transform existing types. These are incredibly useful for creating variations of types without duplicating code.

### Partial<T>

Makes all properties **optional**:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    age: number;
}

// All properties become optional
type PartialUser = Partial<User>;
// Equivalent to:
// {
//     id?: number;
//     name?: string;
//     email?: string;
//     age?: number;
// }

// Perfect for update functions where you only update some fields
function updateUser(id: number, updates: Partial<User>): void {
    console.log(`Updating user ${id} with:`, updates);
}

updateUser(1, { name: "Alice" });              // ✅ OK
updateUser(2, { email: "new@email.com" });    // ✅ OK
updateUser(3, { name: "Bob", age: 30 });      // ✅ OK
```

### Required<T>

Makes all properties **required** (opposite of Partial):

```typescript
interface Config {
    apiUrl?: string;
    timeout?: number;
    retries?: number;
}

// All properties become required
type RequiredConfig = Required<Config>;
// Equivalent to:
// {
//     apiUrl: string;
//     timeout: number;
//     retries: number;
// }

function initializeApp(config: RequiredConfig): void {
    console.log(`API URL: ${config.apiUrl}`);
    console.log(`Timeout: ${config.timeout}`);
}

initializeApp({
    apiUrl: "https://api.example.com",
    timeout: 5000,
    retries: 3
});

// ❌ ERROR if any property is missing
initializeApp({
    apiUrl: "https://api.example.com"
    // timeout and retries are required
});
```

### Pick<T, K>

Creates a type with **only the specified properties**:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    password: string;
    createdAt: Date;
}

// Pick only id, name, and email
type PublicUser = Pick<User, "id" | "name" | "email">;
// Equivalent to:
// {
//     id: number;
//     name: string;
//     email: string;
// }

// Useful for API responses that shouldn't include sensitive data
function getPublicProfile(userId: number): PublicUser {
    // Fetch user and return only public fields
    return {
        id: userId,
        name: "Alice",
        email: "alice@example.com"
        // No password or createdAt!
    };
}
```

### Omit<T, K>

Creates a type by **excluding specified properties**:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    password: string;
    createdAt: Date;
}

// Omit password (exclude sensitive data)
type SafeUser = Omit<User, "password">;
// Equivalent to:
// {
//     id: number;
//     name: string;
//     email: string;
//     createdAt: Date;
// }

// Omit multiple properties
type UserPreview = Omit<User, "password" | "createdAt">;
// {
//     id: number;
//     name: string;
//     email: string;
// }
```

**Practical example combining utility types:**

```typescript
interface Product {
    id: number;
    name: string;
    description: string;
    price: number;
    stock: number;
    createdAt: Date;
    updatedAt: Date;
}

// For creating new products (no id, timestamps generated automatically)
type CreateProduct = Omit<Product, "id" | "createdAt" | "updatedAt">;

// For updating products (all fields optional except id)
type UpdateProduct = Partial<Omit<Product, "id" | "createdAt" | "updatedAt">>;

// For product listings (minimal data)
type ProductPreview = Pick<Product, "id" | "name" | "price">;

function createProduct(data: CreateProduct): Product {
    return {
        ...data,
        id: Math.random(),
        createdAt: new Date(),
        updatedAt: new Date()
    };
}

function updateProduct(id: number, updates: UpdateProduct): void {
    console.log(`Updating product ${id}:`, updates);
}

function getProductPreviews(): ProductPreview[] {
    return [
        { id: 1, name: "Laptop", price: 999 },
        { id: 2, name: "Phone", price: 699 }
    ];
}
```

**Other useful utility types (preview):**

```typescript
// Readonly<T> - Makes all properties readonly
type ReadonlyUser = Readonly<User>;

// Record<K, T> - Creates an object type with keys K and values T
type UserRoles = Record<string, string[]>;
const roles: UserRoles = {
    admin: ["read", "write", "delete"],
    user: ["read"]
};

// ReturnType<T> - Gets the return type of a function
function getUser() {
    return { id: 1, name: "Alice" };
}
type UserType = ReturnType<typeof getUser>;
// { id: number; name: string; }
```

---

## Summary

In this chapter, you learned:

✅ **Functions** with typed parameters, return types, optional/default parameters, and arrow functions  
✅ **Interfaces** for defining object contracts and extending them  
✅ **Union types** (`|`) for values that can be one of several types  
✅ **Intersection types** (`&`) for combining multiple types  
✅ **Enums and literal types** for fixed sets of values  
✅ **Type assertions** (`as`) for when you know more than TypeScript  
✅ **Utility types** (`Partial`, `Required`, `Pick`, `Omit`) for transforming types  

You can now write well-structured, strongly typed TypeScript code that's maintainable and scalable!

---

## Practice Exercises

1. Create an interface `Product` with `id`, `name`, `price`, and optional `discount`. Write a function `calculateFinalPrice` that takes a `Product` and returns the price after discount.

2. Create a union type `ApiResponse` that can be either `{ status: "success"; data: User }` or `{ status: "error"; message: string }`. Write a function that handles both cases.

3. Create an enum `HttpMethod` with values `GET`, `POST`, `PUT`, `DELETE`. Write a function that accepts only these methods.

4. Using utility types, create:
   - A `CreateUser` type from `User` that omits `id` and `createdAt`
   - An `UpdateUser` type that makes all fields optional

**Next Steps:** In Chapter 3, we'll apply TypeScript in real projects — working with APIs, classes, generics, and building a complete typed application!

---