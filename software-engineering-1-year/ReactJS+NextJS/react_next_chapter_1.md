# Chapter 1: TypeScript & React Foundations (Prep Chapter)

## Introduction

Welcome to the React + Next.js course! You already know **TypeScript** — we'll use it throughout. This chapter reinforces the syntax and patterns you'll see in React and Next.js.

React uses TSX (TypeScript + XML), arrow functions, destructuring, modules, and type-safe patterns. Understanding these will make building with React and Next.js smooth and enjoyable.

By the end of this chapter, you'll understand:
- TypeScript syntax used in React (types, interfaces, generics)
- What TSX is and how it works
- Why React exists and what problems it solves
- The mental model behind React's Virtual DOM

Let's build a solid foundation!

---

## 1. TypeScript Recap (for React)

React and Next.js code in this course is written in **TypeScript**. Here's a quick recap of the patterns we'll use.

### let and const

Always use `const` by default. Use `let` only when you need to reassign a value. Never use `var`.

```typescript
// ❌ Old way (var) — avoid in TypeScript
var name: string = "Alice";
var age: number = 25;

// ✅ Modern way (const and let)
const name = "Alice";  // Cannot be reassigned
let age: number = 25;  // Can be reassigned
age = 26;              // ✅ OK

name = "Bob";          // ❌ ERROR: Assignment to constant variable
```

**In React:**

```tsx
const [count, setCount] = useState<number>(0);  // const + types for state
const handleClick = () => { };                   // const for functions
let filteredItems = items.filter(...);          // let if value changes
```

### Template Literals

Use backticks (`` ` ``) for string interpolation:

```typescript
// ❌ Old way
const name: string = "Alice";
const greeting = "Hello, " + name + "!";

// ✅ Modern way (template literals)
const greeting = `Hello, ${name}!`;
const message = `You have ${count} new messages.`;
```

**In React:**

```tsx
interface GreetingProps {
  name: string;
}
function Greeting({ name }: GreetingProps) {
  return <h1>Hello, {name}!</h1>;  // TSX uses {} for expressions
}

const url = `/api/users/${userId}`;
const className = `btn btn-${variant}`;
```

### Arrow Functions

Arrow functions are the standard in React:

```typescript
// ❌ Old function syntax
function greet(name: string): string {
  return "Hello, " + name;
}

// ✅ Arrow function with types
const greet = (name: string): string => {
  return `Hello, ${name}`;
};

// ✅ Implicit return (single expression)
const greetShort = (name: string): string => `Hello, ${name}`;

// ✅ No parameters
const sayHello = (): void => console.log("Hello!");

// ✅ Single parameter (parentheses optional in TS too)
const double = (x: number): number => x * 2;
```

**In React:**

```tsx
// Event handlers
const handleClick = (): void => {
  console.log("Clicked!");
};

// Array methods (types inferred or explicit)
const doubled = numbers.map((n: number) => n * 2);
const adults = users.filter((user: { age: number }) => user.age >= 18);

// Component with props type
interface ButtonProps {
  label: string;
  onClick: () => void;
}
const Button = ({ label, onClick }: ButtonProps) => {
  return <button onClick={onClick}>{label}</button>;
};
```

**Arrow functions and `this`:**

Arrow functions don't have their own `this` — they inherit it from the surrounding context:

```typescript
// Traditional function
const person = {
  name: "Alice",
  greet: function (this: { name: string }) {
    setTimeout(function () {
      console.log(`Hello, ${this.name}`);  // ❌ this is undefined in strict mode
    }, 1000);
  },
};

// Arrow function (inherits this)
const person2 = {
  name: "Alice",
  greet: function (this: { name: string }) {
    setTimeout(() => {
      console.log(`Hello, ${this.name}`);  // ✅ this refers to person2
    }, 1000);
  },
};
```

In React, this rarely matters because we use functional components and hooks (no `this`).

### Destructuring

Destructuring extracts values from arrays or objects:

**Array Destructuring:**

```typescript
const colors: string[] = ["red", "green", "blue"];

// ❌ Old way
const first = colors[0];
const second = colors[1];

// ✅ Destructuring
const [first, second, third] = colors;
console.log(first);   // "red"
console.log(second);  // "green"

// Skip elements
const [firstItem, , thirdItem] = colors;  // Skip "green"

// Default values
const [a, b, c, fourth = "yellow"] = colors;
```

**In React (hooks):**

```tsx
const [count, setCount] = useState<number>(0);
const [user, setUser] = useState<User | null>(null);
const [loading, error, data] = useFetch<User[]>("/api/users");
```

**Object Destructuring:**

```typescript
interface User {
  name: string;
  age: number;
  email: string;
}
const user: User = {
  name: "Alice",
  age: 25,
  email: "alice@example.com",
};

// ❌ Old way
const name = user.name;
const age = user.age;

// ✅ Destructuring
const { name, age, email } = user;
console.log(name);  // "Alice"

// Rename variables
const { name: userName, age: userAge } = user;
console.log(userName);  // "Alice"

// Default values
const { name: n, age: a, role = "user" } = user;
console.log(role);  // "user" (default)

// Nested destructuring
interface Person {
  name: string;
  address: { city: string; zip: string };
}
const person: Person = {
  name: "Alice",
  address: { city: "New York", zip: "10001" },
};
const { name: personName, address: { city, zip } } = person;
console.log(city);  // "New York"
```

**In React (props):**

```tsx
// ❌ Without destructuring
function Greeting(props: { name: string }) {
  return <h1>Hello, {props.name}!</h1>;
}

// ✅ With destructuring + interface (common pattern)
interface GreetingProps {
  name: string;
  age: number;
}
function Greeting({ name, age }: GreetingProps) {
  return (
    <div>
      <h1>Hello, {name}!</h1>
      <p>Age: {age}</p>
    </div>
  );
}

// ✅ With default values
interface ButtonProps {
  label?: string;
  variant?: string;
  onClick: () => void;
}
function Button({ label = "Click me", variant = "primary", onClick }: ButtonProps) {
  return <button className={`btn-${variant}`} onClick={onClick}>{label}</button>;
}
```

### Spread Operator (...)

The spread operator copies or combines arrays and objects:

**Arrays:**

```typescript
const numbers: number[] = [1, 2, 3];
const moreNumbers: number[] = [4, 5, 6];

// Combine arrays
const allNumbers = [...numbers, ...moreNumbers];
// [1, 2, 3, 4, 5, 6]

// Copy array
const copy = [...numbers];

// Add elements
const extended = [...numbers, 4, 5];
// [1, 2, 3, 4, 5]
```

**Objects:**

```typescript
const user = { name: "Alice", age: 25 };
const updates = { age: 26, city: "NYC" };

// Merge objects (later properties override earlier ones)
const updatedUser = { ...user, ...updates };
// { name: "Alice", age: 26, city: "NYC" }

// Copy object
const copy = { ...user };

// Add/override properties
const withEmail = { ...user, email: "alice@example.com" };
```

**In React (state updates):**

```tsx
interface UserState {
  name: string;
  age: number;
}
const [user, setUser] = useState<UserState>({ name: "Alice", age: 25 });

// ❌ Mutating state (BAD!)
user.age = 26;  // DON'T DO THIS

// ✅ Creating new object with spread (GOOD!)
setUser({ ...user, age: 26 });

// ✅ Updating array state
const [items, setItems] = useState<number[]>([1, 2, 3]);
setItems([...items, 4]);  // Add item
setItems(items.filter((item) => item !== 2));  // Remove item
```

### Rest Parameters

The rest operator collects multiple arguments into an array:

```typescript
// Collect remaining arguments
function sum(...numbers: number[]): number {
  return numbers.reduce((total, num) => total + num, 0);
}

sum(1, 2, 3, 4);  // 10

// Destructure with rest
const [first, second, ...rest] = [1, 2, 3, 4, 5];
console.log(first);  // 1
console.log(second); // 2
console.log(rest);   // [3, 4, 5]

const { name, age, ...otherProps } = { name: "Alice", age: 25, city: "NYC", role: "admin" };
console.log(otherProps);  // { city: "NYC", role: "admin" }
```

**In React:**

```tsx
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  label: string;
  onClick: () => void;
}
function Button({ label, onClick, ...otherProps }: ButtonProps) {
  return <button onClick={onClick} {...otherProps}>{label}</button>;
}

<Button label="Click" onClick={handleClick} className="btn" disabled={true} />
// otherProps becomes { className: "btn", disabled: true }
```

---

## 2. Modules & Imports

React applications are organized using ES6 modules.

### Export and Import

**Named Exports:**

```typescript
// utils.ts
export const formatDate = (date: Date): string => {
  return date.toLocaleDateString();
};

export const capitalize = (str: string): string => {
  return str.charAt(0).toUpperCase() + str.slice(1);
};

export const API_URL = "https://api.example.com";
```

```typescript
// App.ts
import { formatDate, capitalize, API_URL } from './utils';

console.log(formatDate(new Date()));
console.log(capitalize("hello"));
```

**Default Exports:**

```tsx
// Button.tsx
interface ButtonProps {
  label: string;
  onClick: () => void;
}
function Button({ label, onClick }: ButtonProps) {
  return <button onClick={onClick}>{label}</button>;
}

export default Button;
```

```tsx
// App.tsx
import Button from './Button';

function App() {
  return <Button label="Click me" onClick={() => console.log("Clicked")} />;
}
```

**Mixing Named and Default:**

```typescript
// utils.ts
export const formatDate = (date: Date): string => date.toLocaleDateString();

export default function helper(): string {
  return "Helper function";
}
```

```typescript
// App.ts
import helper, { formatDate } from './utils';
```

**In React:**

```tsx
// components/Button.tsx
interface ButtonProps {
  label: string;
}
export default function Button({ label }: ButtonProps) {
  return <button>{label}</button>;
}

// components/Card.tsx
interface CardProps {
  title: string;
  content: string;
}
export function Card({ title, content }: CardProps) {
  return (
    <div>
      <h2>{title}</h2>
      <p>{content}</p>
    </div>
  );
}

// App.tsx
import Button from './components/Button';
import { Card } from './components/Card';
```

---

## 3. TSX Fundamentals

TSX is TypeScript + JSX: the same HTML-like syntax with full type checking. Files use the `.tsx` extension.

### What is TSX?

```tsx
// This TSX:
const element = <h1>Hello, world!</h1>;

// Compiles to:
const element = React.createElement('h1', null, 'Hello, world!');
```

TSX makes React code readable and type-safe:

```tsx
// ❌ Without TSX (verbose!)
const element = React.createElement(
  'div',
  { className: 'container' },
  React.createElement('h1', null, 'Hello'),
  React.createElement('p', null, 'Welcome to React')
);

// ✅ With TSX (readable + typed!)
const element = (
  <div className="container">
    <h1>Hello</h1>
    <p>Welcome to React</p>
  </div>
);
```

### JSX Rules

**1. Must return a single parent element:**

```tsx
// ❌ Multiple root elements (invalid!)
function App() {
  return (
    <h1>Hello</h1>
    <p>World</p>
  );
}

// ✅ Wrapped in a parent
function App() {
  return (
    <div>
      <h1>Hello</h1>
      <p>World</p>
    </div>
  );
}

// ✅ Using React Fragment (no extra DOM element)
function App() {
  return (
    <>
      <h1>Hello</h1>
      <p>World</p>
    </>
  );
}
```

**2. Use `className` instead of `class`:**

```tsx
// ❌ Wrong
<div class="container">Hello</div>

// ✅ Correct
<div className="container">Hello</div>
```

**3. Close all tags:**

```tsx
// ❌ Wrong
<img src="logo.png">
<input type="text">

// ✅ Correct
<img src="logo.png" alt="" />
<input type="text" />
```

**4. Use camelCase for attributes:**

```tsx
// ❌ Wrong
<button onclick="handleClick">Click</button>

// ✅ Correct
<button onClick={handleClick}>Click</button>
```

**5. TypeScript/JavaScript expressions in curly braces `{}`:**

```tsx
const name: string = "Alice";
const age: number = 25;

function App() {
  return (
    <div>
      <h1>Hello, {name}!</h1>
      <p>You are {age} years old.</p>
      <p>Next year you'll be {age + 1}.</p>
      <p>{age >= 18 ? "Adult" : "Minor"}</p>
    </div>
  );
}
```

**6. Inline styles use objects:**

```tsx
// ❌ Wrong (string)
<div style="color: red; font-size: 20px;">Hello</div>

// ✅ Correct (object with camelCase properties)
<div style={{ color: "red", fontSize: "20px" }}>Hello</div>

const styles: React.CSSProperties = {
  color: "blue",
  fontSize: "24px",
  fontWeight: "bold",
};

<div style={styles}>Styled text</div>
```

---

## 4. Why React Exists

### The Problem React Solves

**Traditional JavaScript (DOM manipulation hell):**

```typescript
// Vanilla JS - tedious and error-prone
const button = document.getElementById('btn');
const counter = document.getElementById('counter');
let count = 0;

button?.addEventListener('click', () => {
  count++;
  if (counter) counter.textContent = String(count);
});

// What if you need to update multiple elements?
// What if state changes from multiple sources?
// Managing this becomes a nightmare!
```

**React approach (declarative):**

```tsx
function Counter() {
  const [count, setCount] = useState<number>(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

// React handles DOM updates automatically
// You just describe what the UI should look like
```

### React's Core Philosophy

**Declarative:** You describe **what** the UI should look like, not **how** to update it.

```tsx
// Declarative - describe the desired state
{isLoggedIn ? <Dashboard /> : <Login />}

// vs Imperative (manual DOM manipulation)
if (isLoggedIn) {
  dashboard.style.display = 'block';
  login.style.display = 'none';
} else {
  dashboard.style.display = 'none';
  login.style.display = 'block';
}
```

**Component-Based:** UI is built from reusable, composable pieces.

```tsx
function App() {
  return (
    <div>
      <Header />
      <Sidebar />
      <MainContent />
      <Footer />
    </div>
  );
}
```

**Unidirectional Data Flow:** Data flows down from parent to child components (predictable).

```tsx
function App() {
  const [user, setUser] = useState<User | null>(null);

  return (
    <div>
      <Header user={user} />  {/* Data flows down */}
      <Profile user={user} />
    </div>
  );
}
```

---

## 5. SPA vs MPA

### Multi-Page Application (MPA)

Traditional web apps — server renders a new HTML page for every route:

```
User clicks "About" → Browser requests /about → Server sends new HTML → Full page reload
```

**Characteristics:**
- ❌ Full page reload on navigation
- ❌ Slower (network request for each page)
- ✅ Better for SEO (each page is server-rendered)
- ✅ Simpler server-side architecture

### Single-Page Application (SPA)

React apps — server sends one HTML page, JavaScript handles routing:

```
User clicks "About" → JavaScript updates the DOM → No page reload
```

**Characteristics:**
- ✅ Fast navigation (no full reload)
- ✅ Feels like a native app
- ❌ Initial load can be slower
- ❌ SEO challenges (JavaScript must render content)

**React is typically used for SPAs.** Next.js solves the SPA limitations (SEO, initial load) by combining SPA benefits with server-side rendering.

---

## 6. Virtual DOM Mental Model

### The Problem

Updating the real DOM is **slow**:

```typescript
// Every change re-renders everything
for (let i = 0; i < 1000; i++) {
  const div = document.createElement('div');
  div.textContent = String(i);
  document.body.appendChild(div);  // Slow!
}
```

### React's Solution: Virtual DOM

1. **React keeps a virtual copy of the DOM in memory** (JavaScript object)
2. When state changes, React creates a **new virtual DOM**
3. React **compares** (diffs) old vs new virtual DOM
4. React updates **only what changed** in the real DOM

**Example:**

```tsx
function Counter() {
  const [count, setCount] = useState<number>(0);

  return (
    <div>
      <h1>Counter App</h1>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  );
}
```

**When button is clicked:**
1. `count` changes from `0` to `1`
2. React creates new virtual DOM with `<p>Count: 1</p>`
3. React compares: Only the `<p>` text changed
4. React updates **only** that `<p>` element in the real DOM

**Mental Model:**

```
State Change → Virtual DOM Update → Diff → Minimal Real DOM Update
```

This makes React **fast and efficient** even with complex UIs.

---

## Summary

In this chapter, you learned:

✅ **TypeScript in React** — Types, interfaces, arrow functions, destructuring, spread, template literals  
✅ **Modules** — Import/export for organizing code (.ts / .tsx)  
✅ **TSX** — HTML-like syntax in TypeScript  
✅ **Why React exists** — Declarative UI, component-based architecture  
✅ **SPA vs MPA** — React's single-page approach  
✅ **Virtual DOM** — React's secret to performance  

You're now ready to start building with React and TypeScript!

---

## Practice Exercises

1. Convert this to an arrow function with implicit return and types:
   ```typescript
   function double(x: number): number {
     return x * 2;
   }
   ```

2. Destructure this object and add an interface:
   ```typescript
   const user = { name: "Alice", age: 25, email: "alice@example.com" };
   ```

3. Merge these objects using spread:
   ```typescript
   const defaults = { theme: "light", fontSize: 14 };
   const userPrefs = { fontSize: 16, language: "en" };
   ```

4. Write a TSX component with typed props that displays your name and age.

5. Explain in your own words: Why is the Virtual DOM faster than manipulating the real DOM directly?

---