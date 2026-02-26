# Chapter 3: State, Events & Conditional Rendering

## Introduction

In Chapter 2, you built **static** UIs using components and props. Now it's time to make them **interactive**!

This chapter covers the three pillars of interactivity in React:

1. **State** — Data that changes over time
2. **Events** — Responding to user actions (clicks, typing, etc.)
3. **Conditional Rendering** — Showing/hiding UI based on conditions

By the end of this chapter, you'll build fully interactive React applications with dynamic, responsive UIs.

---

## 1. useState Hook

**State** is data that changes over time and triggers re-renders when updated.

### Basic useState

```tsx
import { useState } from 'react';

function Counter() {
    // Declare state variable (TypeScript: useState<number>)
    const [count, setCount] = useState<number>(0);
    //      ↑       ↑           ↑
    //   current  updater    initial
    //   value   function     value

    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>
                Increment
            </button>
        </div>
    );
}
```

**How it works:**

1. `useState(0)` creates state with initial value `0`
2. Returns an array: `[currentValue, updaterFunction]`
3. We destructure: `const [count, setCount] = useState(0)`
4. When `setCount` is called, React **re-renders** the component with the new value

### Multiple State Variables

```tsx
function UserForm() {
    const [name, setName] = useState<string>('');
    const [email, setEmail] = useState<string>('');
    const [age, setAge] = useState<number>(0);
    const [isSubscribed, setIsSubscribed] = useState<boolean>(false);

    return (
        <form>
            <input 
                type="text" 
                value={name}
                onChange={(e) => setName(e.target.value)}
            />
            <input 
                type="email" 
                value={email}
                onChange={(e) => setEmail(e.target.value)}
            />
            <input 
                type="number" 
                value={age}
                onChange={(e) => setAge(Number(e.target.value))}
            />
            <label>
                <input 
                    type="checkbox"
                    checked={isSubscribed}
                    onChange={(e) => setIsSubscribed(e.target.checked)}
                />
                Subscribe to newsletter
            </label>
        </form>
    );
}
```

### State with Objects

When state is an object, you must create a **new object** when updating:

```tsx
interface UserState {
    name: string;
    age: number;
    email: string;
}
function UserProfile() {
    const [user, setUser] = useState<UserState>({
        name: 'Alice',
        age: 25,
        email: 'alice@example.com'
    });

    const updateName = (newName: string) => {
        // ❌ WRONG: Mutating state
        user.name = newName;  // DON'T DO THIS

        // ✅ CORRECT: Create new object with spread
        setUser({ ...user, name: newName });
    };

    const updateEmail = (newEmail) => {
        setUser({ ...user, email: newEmail });
    };

    const celebrateBirthday = () => {
        setUser({ ...user, age: user.age + 1 });
    };

    return (
        <div>
            <p>Name: {user.name}</p>
            <p>Age: {user.age}</p>
            <p>Email: {user.email}</p>
            <button onClick={() => updateName('Bob')}>Change Name</button>
            <button onClick={celebrateBirthday}>Birthday!</button>
        </div>
    );
}
```

### State with Arrays

```tsx
function TodoList() {
    const [todos, setTodos] = useState<string[]>(['Buy milk', 'Walk dog']);

    const addTodo = (text: string) => {
        // ✅ Add item: create new array
        setTodos([...todos, text]);
    };

    const removeTodo = (index) => {
        // ✅ Remove item: filter creates new array
        setTodos(todos.filter((_, i) => i !== index));
    };

    const updateTodo = (index, newText) => {
        // ✅ Update item: map creates new array
        setTodos(todos.map((todo, i) => 
            i === index ? newText : todo
        ));
    };

    return (
        <div>
            <ul>
                {todos.map((todo, index) => (
                    <li key={index}>
                        {todo}
                        <button onClick={() => removeTodo(index)}>Delete</button>
                    </li>
                ))}
            </ul>
            <button onClick={() => addTodo('New todo')}>Add Todo</button>
        </div>
    );
}
```

### Functional Updates

When new state depends on previous state, use the **functional form**:

```tsx
function Counter() {
    const [count, setCount] = useState(0);

    const increment = () => {
        // ❌ Can cause issues with async updates
        setCount(count + 1);

        // ✅ BETTER: Use functional update
        setCount(prevCount => prevCount + 1);
    };

    const incrementBy5 = () => {
        // ❌ This won't work as expected
        setCount(count + 1);
        setCount(count + 1);
        setCount(count + 1);
        setCount(count + 1);
        setCount(count + 1);
        // Result: count increases by 1 (React batches updates)

        // ✅ This works correctly
        setCount(prev => prev + 1);
        setCount(prev => prev + 1);
        setCount(prev => prev + 1);
        setCount(prev => prev + 1);
        setCount(prev => prev + 1);
        // Result: count increases by 5
    };

    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={increment}>+1</button>
            <button onClick={incrementBy5}>+5</button>
        </div>
    );
}
```

**Rule:** Use functional updates when new state depends on previous state.

---

## 2. Event Handling

React events are similar to DOM events but use **camelCase** and pass functions (not strings).

### Click Events

```tsx
function Button() {
    const handleClick = () => {
        console.log('Button clicked!');
    };

    return (
        <div>
            {/* ✅ Correct: Pass function reference */}
            <button onClick={handleClick}>Click me</button>

            {/* ✅ Correct: Inline arrow function */}
            <button onClick={() => console.log('Clicked!')}>
                Click me
            </button>

            {/* ❌ Wrong: Calls function immediately */}
            <button onClick={handleClick()}>
                Don't do this
            </button>

            {/* ❌ Wrong: String (not React) */}
            <button onClick="handleClick()">
                This is HTML, not React
            </button>
        </div>
    );
}
```

### Event Object

React provides a **synthetic event** object (cross-browser wrapper):

```tsx
function Form() {
    const handleSubmit = (event) => {
        event.preventDefault();  // Prevent form submission
        console.log('Form submitted!');
    };

    const handleClick = (event) => {
        console.log('Button clicked!');
        console.log('Target:', event.target);
        console.log('Type:', event.type);
    };

    return (
        <form onSubmit={handleSubmit}>
            <button onClick={handleClick}>Submit</button>
        </form>
    );
}
```

### Passing Arguments to Event Handlers

```tsx
function ProductList() {
    const products = [
        { id: 1, name: 'Laptop', price: 999 },
        { id: 2, name: 'Phone', price: 699 },
        { id: 3, name: 'Tablet', price: 499 }
    ];

    const handleBuy = (productId, productName) => {
        console.log(`Buying product ${productId}: ${productName}`);
    };

    return (
        <div>
            {products.map(product => (
                <div key={product.id}>
                    <h3>{product.name}</h3>
                    <p>${product.price}</p>
                    
                    {/* Method 1: Inline arrow function */}
                    <button onClick={() => handleBuy(product.id, product.name)}>
                        Buy
                    </button>

                    {/* Method 2: Return a function */}
                    <button onClick={handleBuy.bind(null, product.id, product.name)}>
                        Buy
                    </button>
                </div>
            ))}
        </div>
    );
}
```

### Common Events

```tsx
function EventExamples() {
    return (
        <div>
            {/* Click events */}
            <button onClick={() => console.log('Clicked')}>Click</button>
            <button onDoubleClick={() => console.log('Double clicked')}>
                Double Click
            </button>

            {/* Form events */}
            <input onChange={(e) => console.log(e.target.value)} />
            <input onFocus={() => console.log('Focused')} />
            <input onBlur={() => console.log('Blurred')} />
            <form onSubmit={(e) => e.preventDefault()}>
                <button type="submit">Submit</button>
            </form>

            {/* Mouse events */}
            <div 
                onMouseEnter={() => console.log('Mouse entered')}
                onMouseLeave={() => console.log('Mouse left')}
            >
                Hover over me
            </div>

            {/* Keyboard events */}
            <input 
                onKeyDown={(e) => console.log('Key down:', e.key)}
                onKeyUp={(e) => console.log('Key up:', e.key)}
                onKeyPress={(e) => console.log('Key press:', e.key)}
            />
        </div>
    );
}
```

---

## 3. Controlled Inputs

In React, form inputs should be **controlled** — their value is controlled by React state.

### Controlled Input

```tsx
function ControlledInput() {
    const [text, setText] = useState('');

    return (
        <div>
            <input 
                type="text"
                value={text}                          // Controlled by state
                onChange={(e) => setText(e.target.value)}
            />
            <p>You typed: {text}</p>
        </div>
    );
}
```

**How it works:**

1. Input's `value` is set to state (`text`)
2. User types → `onChange` fires
3. `setText` updates state
4. Component re-renders with new value
5. Input shows updated value

### Multiple Inputs

```tsx
function UserForm() {
    const [formData, setFormData] = useState({
        firstName: '',
        lastName: '',
        email: '',
        age: ''
    });

    const handleChange = (e) => {
        const { name, value } = e.target;
        setFormData({
            ...formData,
            [name]: value  // Computed property name
        });
    };

    const handleSubmit = (e) => {
        e.preventDefault();
        console.log('Form submitted:', formData);
    };

    return (
        <form onSubmit={handleSubmit}>
            <input
                type="text"
                name="firstName"
                value={formData.firstName}
                onChange={handleChange}
                placeholder="First Name"
            />
            <input
                type="text"
                name="lastName"
                value={formData.lastName}
                onChange={handleChange}
                placeholder="Last Name"
            />
            <input
                type="email"
                name="email"
                value={formData.email}
                onChange={handleChange}
                placeholder="Email"
            />
            <input
                type="number"
                name="age"
                value={formData.age}
                onChange={handleChange}
                placeholder="Age"
            />
            <button type="submit">Submit</button>
        </form>
    );
}
```

### Checkbox and Radio Inputs

```tsx
function FormControls() {
    const [isChecked, setIsChecked] = useState(false);
    const [selectedOption, setSelectedOption] = useState('');

    return (
        <div>
            {/* Checkbox */}
            <label>
                <input
                    type="checkbox"
                    checked={isChecked}
                    onChange={(e) => setIsChecked(e.target.checked)}
                />
                I agree to terms
            </label>

            {/* Radio buttons */}
            <div>
                <label>
                    <input
                        type="radio"
                        name="option"
                        value="option1"
                        checked={selectedOption === 'option1'}
                        onChange={(e) => setSelectedOption(e.target.value)}
                    />
                    Option 1
                </label>
                <label>
                    <input
                        type="radio"
                        name="option"
                        value="option2"
                        checked={selectedOption === 'option2'}
                        onChange={(e) => setSelectedOption(e.target.value)}
                    />
                    Option 2
                </label>
            </div>

            {/* Select dropdown */}
            <select 
                value={selectedOption}
                onChange={(e) => setSelectedOption(e.target.value)}
            >
                <option value="">Choose...</option>
                <option value="option1">Option 1</option>
                <option value="option2">Option 2</option>
                <option value="option3">Option 3</option>
            </select>
        </div>
    );
}
```

---

## 4. Conditional Rendering

Render different UI based on state or props.

### Using Ternary Operator

```tsx
function Greeting({ isLoggedIn }: { isLoggedIn: boolean }) {
    return (
        <div>
            {isLoggedIn ? (
                <h1>Welcome back!</h1>
            ) : (
                <h1>Please sign in.</h1>
            )}
        </div>
    );
}
```

### Using Logical AND (&&)

```tsx
function Notifications({ count }: { count: number }) {
    return (
        <div>
            <h1>Notifications</h1>
            {count > 0 && (
                <p>You have {count} new notifications</p>
            )}
        </div>
    );
}

// If count is 0, the <p> doesn't render
// If count > 0, the <p> renders
```

**Warning:** Be careful with falsy values:

```tsx
function Messages({ count }) {
    return (
        <div>
            {/* ❌ BAD: Shows "0" when count is 0 */}
            {count && <p>{count} messages</p>}

            {/* ✅ GOOD: Explicitly check > 0 */}
            {count > 0 && <p>{count} messages</p>}
        </div>
    );
}
```

### Using if/else

```tsx
function Dashboard({ user }) {
    if (!user) {
        return <p>Loading...</p>;
    }

    if (user.role === 'admin') {
        return <AdminDashboard />;
    }

    if (user.role === 'editor') {
        return <EditorDashboard />;
    }

    return <UserDashboard />;
}
```

### Switch Statement

```tsx
function StatusBadge({ status }) {
    let badgeColor;
    let badgeText;

    switch (status) {
        case 'pending':
            badgeColor = 'yellow';
            badgeText = 'Pending';
            break;
        case 'approved':
            badgeColor = 'green';
            badgeText = 'Approved';
            break;
        case 'rejected':
            badgeColor = 'red';
            badgeText = 'Rejected';
            break;
        default:
            badgeColor = 'gray';
            badgeText = 'Unknown';
    }

    return (
        <span className={`badge bg-${badgeColor}`}>
            {badgeText}
        </span>
    );
}
```

### Object Mapping (Clean Pattern)

```tsx
function StatusBadge({ status }) {
    const statusConfig = {
        pending: { color: 'yellow', text: 'Pending' },
        approved: { color: 'green', text: 'Approved' },
        rejected: { color: 'red', text: 'Rejected' }
    };

    const config = statusConfig[status] || { color: 'gray', text: 'Unknown' };

    return (
        <span className={`badge bg-${config.color}`}>
            {config.text}
        </span>
    );
}
```

### Conditional Classes

```tsx
function Button({ variant = 'primary', size = 'medium', disabled }) {
    // Method 1: Template literal
    const className = `btn btn-${variant} btn-${size} ${disabled ? 'disabled' : ''}`;

    // Method 2: Array join
    const classNames = [
        'btn',
        `btn-${variant}`,
        `btn-${size}`,
        disabled && 'disabled'
    ].filter(Boolean).join(' ');

    return <button className={className}>Click me</button>;
}
```

---

## 5. Lists & Keys

Rendering arrays of data is common in React.

### Basic List Rendering

```tsx
function TodoList() {
    const todos = [
        'Buy groceries',
        'Walk the dog',
        'Finish homework'
    ];

    return (
        <ul>
            {todos.map((todo, index) => (
                <li key={index}>{todo}</li>
            ))}
        </ul>
    );
}
```

### Keys are Required

**Why keys matter:**

```tsx
// ❌ Without key (React warning)
{items.map(item => <li>{item}</li>)}

// ✅ With key
{items.map((item, index) => <li key={index}>{item}</li>)}
```

Keys help React identify which items changed, were added, or removed.

### Using Unique IDs as Keys

```tsx
function ProductList() {
    const products = [
        { id: 1, name: 'Laptop', price: 999 },
        { id: 2, name: 'Phone', price: 699 },
        { id: 3, name: 'Tablet', price: 499 }
    ];

    return (
        <div>
            {products.map(product => (
                <div key={product.id} className="product">
                    <h3>{product.name}</h3>
                    <p>${product.price}</p>
                </div>
            ))}
        </div>
    );
}
```

**Key Rules:**

1. ✅ Use unique, stable IDs when available
2. ⚠️ Use index only if items never reorder and have no IDs
3. ❌ Don't use random values (`Math.random()`)
4. ❌ Don't use array index for dynamic lists (causes bugs)

### When Index as Key Causes Bugs

```tsx
function TodoList() {
    const [todos, setTodos] = useState([
        { id: 1, text: 'Task 1' },
        { id: 2, text: 'Task 2' },
        { id: 3, text: 'Task 3' }
    ]);

    const deleteTodo = (id) => {
        setTodos(todos.filter(todo => todo.id !== id));
    };

    return (
        <ul>
            {/* ❌ BAD: Using index as key */}
            {todos.map((todo, index) => (
                <li key={index}>
                    {todo.text}
                    <button onClick={() => deleteTodo(todo.id)}>Delete</button>
                </li>
            ))}

            {/* ✅ GOOD: Using unique ID as key */}
            {todos.map(todo => (
                <li key={todo.id}>
                    {todo.text}
                    <button onClick={() => deleteTodo(todo.id)}>Delete</button>
                </li>
            ))}
        </ul>
    );
}
```

### Filtering and Mapping Lists

```tsx
function ProductCatalog() {
    const [searchTerm, setSearchTerm] = useState('');
    const [selectedCategory, setSelectedCategory] = useState('all');

    const products = [
        { id: 1, name: 'Laptop', category: 'electronics', price: 999 },
        { id: 2, name: 'Shirt', category: 'clothing', price: 29 },
        { id: 3, name: 'Phone', category: 'electronics', price: 699 },
        { id: 4, name: 'Pants', category: 'clothing', price: 49 }
    ];

    const filteredProducts = products
        .filter(product => 
            (selectedCategory === 'all' || product.category === selectedCategory) &&
            product.name.toLowerCase().includes(searchTerm.toLowerCase())
        );

    return (
        <div>
            <input
                type="text"
                placeholder="Search products..."
                value={searchTerm}
                onChange={(e) => setSearchTerm(e.target.value)}
            />

            <select 
                value={selectedCategory}
                onChange={(e) => setSelectedCategory(e.target.value)}
            >
                <option value="all">All Categories</option>
                <option value="electronics">Electronics</option>
                <option value="clothing">Clothing</option>
            </select>

            <div className="products">
                {filteredProducts.length > 0 ? (
                    filteredProducts.map(product => (
                        <div key={product.id} className="product-card">
                            <h3>{product.name}</h3>
                            <p>Category: {product.category}</p>
                            <p>${product.price}</p>
                        </div>
                    ))
                ) : (
                    <p>No products found</p>
                )}
            </div>
        </div>
    );
}
```

---

## 6. Component Re-rendering Behavior

Understanding when and why components re-render is crucial for performance.

### When Components Re-render

A component re-renders when:

1. **State changes** (via `setState`)
2. **Props change** (parent passes new props)
3. **Parent re-renders** (by default, all children re-render)
4. **Context changes** (if using Context API)

```tsx
function Parent() {
    const [count, setCount] = useState(0);

    console.log('Parent rendered');

    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
            <Child />  {/* Re-renders every time Parent re-renders */}
        </div>
    );
}

function Child() {
    console.log('Child rendered');
    return <p>I'm a child component</p>;
}

// Clicking button logs:
// Parent rendered
// Child rendered
```

### State Updates are Asynchronous

```tsx
function Counter() {
    const [count, setCount] = useState(0);

    const handleClick = () => {
        setCount(count + 1);
        console.log(count);  // Still shows old value!
        
        // State updates are batched and asynchronous
        // Component re-renders AFTER this function completes
    };

    // To see updated value, use useEffect (next chapter)

    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={handleClick}>Increment</button>
        </div>
    );
}
```

### Batching State Updates

React batches multiple state updates for performance:

```tsx
function Counter() {
    const [count, setCount] = useState(0);
    const [text, setText] = useState('');

    const handleClick = () => {
        // These three updates are batched into one re-render
        setCount(count + 1);
        setText('Updated');
        setCount(count + 2);  // This overrides the first setCount

        // Result: Only ONE re-render happens
    };

    return <button onClick={handleClick}>Click</button>;
}
```

---

## 7. Practical Example: Todo App

Let's build a complete todo app using everything we've learned:

```tsx
import { useState } from 'react';

function TodoApp() {
    const [todos, setTodos] = useState([]);
    const [inputValue, setInputValue] = useState('');
    const [filter, setFilter] = useState('all'); // 'all', 'active', 'completed'

    // Add todo
    const addTodo = (e) => {
        e.preventDefault();
        if (inputValue.trim() === '') return;

        const newTodo = {
            id: Date.now(),
            text: inputValue,
            completed: false
        };

        setTodos([...todos, newTodo]);
        setInputValue('');
    };

    // Toggle todo completion
    const toggleTodo = (id) => {
        setTodos(todos.map(todo =>
            todo.id === id ? { ...todo, completed: !todo.completed } : todo
        ));
    };

    // Delete todo
    const deleteTodo = (id) => {
        setTodos(todos.filter(todo => todo.id !== id));
    };

    // Filter todos
    const getFilteredTodos = () => {
        switch (filter) {
            case 'active':
                return todos.filter(todo => !todo.completed);
            case 'completed':
                return todos.filter(todo => todo.completed);
            default:
                return todos;
        }
    };

    const filteredTodos = getFilteredTodos();
    const activeCount = todos.filter(todo => !todo.completed).length;

    return (
        <div className="todo-app">
            <h1>Todo App</h1>

            {/* Add todo form */}
            <form onSubmit={addTodo}>
                <input
                    type="text"
                    value={inputValue}
                    onChange={(e) => setInputValue(e.target.value)}
                    placeholder="What needs to be done?"
                />
                <button type="submit">Add</button>
            </form>

            {/* Filter buttons */}
            <div className="filters">
                <button 
                    className={filter === 'all' ? 'active' : ''}
                    onClick={() => setFilter('all')}
                >
                    All
                </button>
                <button 
                    className={filter === 'active' ? 'active' : ''}
                    onClick={() => setFilter('active')}
                >
                    Active
                </button>
                <button 
                    className={filter === 'completed' ? 'active' : ''}
                    onClick={() => setFilter('completed')}
                >
                    Completed
                </button>
            </div>

            {/* Todo list */}
            {filteredTodos.length > 0 ? (
                <ul className="todo-list">
                    {filteredTodos.map(todo => (
                        <li key={todo.id} className={todo.completed ? 'completed' : ''}>
                            <input
                                type="checkbox"
                                checked={todo.completed}
                                onChange={() => toggleTodo(todo.id)}
                            />
                            <span>{todo.text}</span>
                            <button onClick={() => deleteTodo(todo.id)}>Delete</button>
                        </li>
                    ))}
                </ul>
            ) : (
                <p className="empty-state">
                    {filter === 'all' ? 'No todos yet!' : `No ${filter} todos`}
                </p>
            )}

            {/* Footer */}
            {todos.length > 0 && (
                <div className="footer">
                    <span>{activeCount} item{activeCount !== 1 ? 's' : ''} left</span>
                </div>
            )}
        </div>
    );
}

export default TodoApp;
```

**CSS:**

```css
.todo-app {
    max-width: 600px;
    margin: 50px auto;
    padding: 20px;
}

.todo-app h1 {
    text-align: center;
    color: #333;
}

.todo-app form {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.todo-app input[type="text"] {
    flex: 1;
    padding: 10px;
    font-size: 16px;
    border: 1px solid #ddd;
    border-radius: 4px;
}

.todo-app button {
    padding: 10px 20px;
    font-size: 16px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    background: #2563eb;
    color: white;
}

.todo-app button:hover {
    background: #1d4ed8;
}

.filters {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.filters button {
    flex: 1;
    background: #f3f4f6;
    color: #333;
}

.filters button.active {
    background: #2563eb;
    color: white;
}

.todo-list {
    list-style: none;
    padding: 0;
}

.todo-list li {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px;
    border-bottom: 1px solid #e5e7eb;
}

.todo-list li.completed span {
    text-decoration: line-through;
    color: #9ca3af;
}

.todo-list li span {
    flex: 1;
}

.todo-list li button {
    padding: 6px 12px;
    font-size: 14px;
    background: #ef4444;
}

.todo-list li button:hover {
    background: #dc2626;
}

.empty-state {
    text-align: center;
    color: #9ca3af;
    padding: 40px 0;
}

.footer {
    margin-top: 20px;
    padding-top: 10px;
    border-top: 1px solid #e5e7eb;
    color: #6b7280;
}
```

---

## Summary

In this chapter, you learned:

✅ **useState** — Managing state in functional components  
✅ **Event handling** — Responding to user interactions  
✅ **Controlled inputs** — Managing form state  
✅ **Conditional rendering** — Showing/hiding UI dynamically  
✅ **Lists & keys** — Rendering arrays of data  
✅ **Re-rendering** — Understanding when components update  

You can now build fully interactive React applications!

---

## Practice Exercises

1. Build a **counter** with increment, decrement, and reset buttons.

2. Create a **login form** with email and password inputs. Show validation errors.

3. Build a **shopping cart** where users can add/remove items and see the total price.

4. Create a **search filter** for a list of products (filter by name or category).

5. Build a **modal component** that opens/closes based on state.

---