# Chapter 4: Side Effects, Data Fetching & React Best Practices

## Introduction

You've built interactive UIs with state and events. Now it's time to connect your React apps to the real world!

This chapter covers:

1. **useEffect** — Managing side effects (API calls, timers, subscriptions)
2. **Data fetching** — Getting data from APIs with proper loading and error states
3. **Cleanup functions** — Preventing memory leaks
4. **Lifting state up** — Sharing state between components
5. **Folder structure** — Organizing real-world React projects
6. **Mini project** — Building a complete React application

By the end of this chapter, you'll be **React-ready for Next.js** with professional-level skills.

---

## 1. useEffect Hook

**useEffect** lets you perform **side effects** in functional components.

### What are Side Effects?

Side effects are operations that interact with the outside world:

- ✅ Fetching data from APIs
- ✅ Setting up subscriptions (WebSockets, event listeners)
- ✅ Timers (setTimeout, setInterval)
- ✅ Manually changing the DOM
- ✅ Logging to console
- ✅ Reading/writing to localStorage

### Basic useEffect

```tsx
import { useState, useEffect } from 'react';

function Example() {
    const [count, setCount] = useState(0);

    // Runs after every render
    useEffect(() => {
        console.log('Component rendered!');
        document.title = `Count: ${count}`;
    });

    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
        </div>
    );
}
```

### useEffect with Dependency Array

Control when the effect runs using the **dependency array**:

```tsx
function Example() {
    const [count, setCount] = useState(0);
    const [name, setName] = useState('Alice');

    // ❌ Runs after EVERY render (no dependency array)
    useEffect(() => {
        console.log('Runs on every render');
    });

    // ✅ Runs only ONCE after initial render (empty array)
    useEffect(() => {
        console.log('Component mounted!');
    }, []);

    // ✅ Runs when 'count' changes
    useEffect(() => {
        console.log('Count changed:', count);
        document.title = `Count: ${count}`;
    }, [count]);

    // ✅ Runs when 'count' OR 'name' changes
    useEffect(() => {
        console.log('Count or name changed');
    }, [count, name]);

    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
            <input value={name} onChange={(e) => setName(e.target.value)} />
        </div>
    );
}
```

**Dependency Array Rules:**

| Dependency Array | When Effect Runs |
|-----------------|------------------|
| No array | After every render |
| `[]` (empty) | Only once (component mount) |
| `[count]` | When `count` changes |
| `[count, name]` | When `count` OR `name` changes |

### Common useEffect Patterns

**1. Run once on mount:**

```tsx
useEffect(() => {
    console.log('Component mounted - runs once');
    // Fetch initial data, set up subscriptions, etc.
}, []); // Empty array = run once
```

**2. Run when specific value changes:**

```tsx
useEffect(() => {
    console.log('Search term changed:', searchTerm);
    // Fetch search results
}, [searchTerm]);
```

**3. Run on every render (rare, usually a mistake):**

```tsx
useEffect(() => {
    console.log('Runs after every render');
}); // No array = runs always
```

### useEffect Execution Order

```tsx
function Component() {
    console.log('1. Render starts');

    useEffect(() => {
        console.log('3. useEffect runs');
    });

    console.log('2. Render finishes (JSX returned)');

    return <div>Hello</div>;
}

// Output:
// 1. Render starts
// 2. Render finishes (JSX returned)
// 3. useEffect runs
```

**Flow:**
1. Component renders (JSX is calculated)
2. React updates the DOM
3. Browser paints the screen
4. useEffect runs

---

## 2. Fetching Data

The most common use of `useEffect` is fetching data from APIs.

### Basic Data Fetching

```tsx
import { useState, useEffect } from 'react';

function UserList() {
    const [users, setUsers] = useState([]);

    useEffect(() => {
        // Fetch data when component mounts
        fetch('https://jsonplaceholder.typicode.com/users')
            .then(response => response.json())
            .then(data => setUsers(data))
            .catch(error => console.error('Error:', error));
    }, []); // Empty array = fetch once

    return (
        <div>
            <h1>Users</h1>
            <ul>
                {users.map(user => (
                    <li key={user.id}>{user.name}</li>
                ))}
            </ul>
        </div>
    );
}
```

### Loading & Error States

Always handle loading and error states for better UX:

```tsx
function UserList() {
    const [users, setUsers] = useState([]);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
        setLoading(true);
        setError(null);

        fetch('https://jsonplaceholder.typicode.com/users')
            .then(response => {
                if (!response.ok) {
                    throw new Error('Network response was not ok');
                }
                return response.json();
            })
            .then(data => {
                setUsers(data);
                setLoading(false);
            })
            .catch(error => {
                setError(error.message);
                setLoading(false);
            });
    }, []);

    // Loading state
    if (loading) {
        return <div className="loading">Loading users...</div>;
    }

    // Error state
    if (error) {
        return <div className="error">Error: {error}</div>;
    }

    // Success state
    return (
        <div>
            <h1>Users ({users.length})</h1>
            <ul>
                {users.map(user => (
                    <li key={user.id}>
                        <strong>{user.name}</strong> - {user.email}
                    </li>
                ))}
            </ul>
        </div>
    );
}
```

### Using async/await

```tsx
function UserList() {
    const [users, setUsers] = useState([]);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
        // Can't make useEffect callback async directly
        // So create an async function inside
        const fetchUsers = async () => {
            try {
                setLoading(true);
                setError(null);

                const response = await fetch('https://jsonplaceholder.typicode.com/users');
                
                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                
                const data = await response.json();
                setUsers(data);
            } catch (err) {
                setError(err.message);
            } finally {
                setLoading(false);
            }
        };

        fetchUsers();
    }, []);

    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;

    return (
        <div>
            <h1>Users</h1>
            <ul>
                {users.map(user => (
                    <li key={user.id}>{user.name}</li>
                ))}
            </ul>
        </div>
    );
}
```

### Custom Hook for Data Fetching

Create a reusable hook for fetching data:

```tsx
// hooks/useFetch.ts
import { useState, useEffect } from 'react';

function useFetch(url) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
        const fetchData = async () => {
            try {
                setLoading(true);
                setError(null);

                const response = await fetch(url);
                
                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                
                const result = await response.json();
                setData(result);
            } catch (err) {
                setError(err.message);
            } finally {
                setLoading(false);
            }
        };

        fetchData();
    }, [url]); // Re-fetch if URL changes

    return { data, loading, error };
}

export default useFetch;
```

**Usage:**

```tsx
import useFetch from './hooks/useFetch';

function UserList() {
    const { data: users, loading, error } = useFetch(
        'https://jsonplaceholder.typicode.com/users'
    );

    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;

    return (
        <div>
            <h1>Users</h1>
            <ul>
                {users.map(user => (
                    <li key={user.id}>{user.name}</li>
                ))}
            </ul>
        </div>
    );
}
```

### Fetching Data Based on User Input

```tsx
function PostSearch() {
    const [searchTerm, setSearchTerm] = useState('');
    const [posts, setPosts] = useState([]);
    const [loading, setLoading] = useState(false);

    useEffect(() => {
        // Don't fetch if search term is empty
        if (!searchTerm.trim()) {
            setPosts([]);
            return;
        }

        const fetchPosts = async () => {
            setLoading(true);
            try {
                const response = await fetch(
                    `https://jsonplaceholder.typicode.com/posts?title_like=${searchTerm}`
                );
                const data = await response.json();
                setPosts(data);
            } catch (error) {
                console.error('Error:', error);
            } finally {
                setLoading(false);
            }
        };

        // Debounce: wait 500ms after user stops typing
        const timeoutId = setTimeout(fetchPosts, 500);

        // Cleanup: cancel previous timeout if searchTerm changes
        return () => clearTimeout(timeoutId);
    }, [searchTerm]);

    return (
        <div>
            <input
                type="text"
                placeholder="Search posts..."
                value={searchTerm}
                onChange={(e) => setSearchTerm(e.target.value)}
            />

            {loading && <p>Searching...</p>}

            <ul>
                {posts.map(post => (
                    <li key={post.id}>
                        <h3>{post.title}</h3>
                        <p>{post.body}</p>
                    </li>
                ))}
            </ul>
        </div>
    );
}
```

---

## 3. Cleanup Functions

Some effects need **cleanup** to prevent memory leaks.

### Why Cleanup is Needed

```tsx
function Timer() {
    const [seconds, setSeconds] = useState(0);

    useEffect(() => {
        // ❌ Problem: interval keeps running even after unmount
        setInterval(() => {
            setSeconds(prev => prev + 1);
        }, 1000);
    }, []);

    return <div>Seconds: {seconds}</div>;
}
```

**Problem:** If the component unmounts, the interval keeps running → **memory leak**.

### Basic Cleanup

```tsx
function Timer() {
    const [seconds, setSeconds] = useState(0);

    useEffect(() => {
        const intervalId = setInterval(() => {
            setSeconds(prev => prev + 1);
        }, 1000);

        // ✅ Cleanup function
        return () => {
            clearInterval(intervalId);
            console.log('Cleanup: interval cleared');
        };
    }, []);

    return <div>Seconds: {seconds}</div>;
}
```

**How it works:**

1. Component mounts → effect runs, starts interval
2. Component unmounts → cleanup function runs, clears interval

### Cleanup with Event Listeners

```tsx
function MouseTracker() {
    const [position, setPosition] = useState({ x: 0, y: 0 });

    useEffect(() => {
        const handleMouseMove = (e) => {
            setPosition({ x: e.clientX, y: e.clientY });
        };

        // Add event listener
        window.addEventListener('mousemove', handleMouseMove);

        // ✅ Cleanup: remove event listener
        return () => {
            window.removeEventListener('mousemove', handleMouseMove);
        };
    }, []);

    return (
        <div>
            Mouse position: {position.x}, {position.y}
        </div>
    );
}
```

### Cleanup with Fetch (AbortController)

```tsx
function UserProfile({ userId }) {
    const [user, setUser] = useState(null);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        const abortController = new AbortController();

        const fetchUser = async () => {
            try {
                setLoading(true);
                const response = await fetch(
                    `https://jsonplaceholder.typicode.com/users/${userId}`,
                    { signal: abortController.signal }
                );
                const data = await response.json();
                setUser(data);
            } catch (error) {
                if (error.name !== 'AbortError') {
                    console.error('Error:', error);
                }
            } finally {
                setLoading(false);
            }
        };

        fetchUser();

        // ✅ Cleanup: abort fetch if userId changes or component unmounts
        return () => {
            abortController.abort();
        };
    }, [userId]);

    if (loading) return <div>Loading...</div>;
    if (!user) return <div>No user found</div>;

    return (
        <div>
            <h2>{user.name}</h2>
            <p>{user.email}</p>
        </div>
    );
}
```

### Common Cleanup Scenarios

| Effect | Cleanup Needed |
|--------|----------------|
| `setInterval` | `clearInterval` |
| `setTimeout` | `clearTimeout` |
| Event listeners | `removeEventListener` |
| Fetch requests | `abortController.abort()` |
| WebSocket connections | `socket.close()` |
| Subscriptions | `unsubscribe()` |

---

## 4. Lifting State Up

When multiple components need to share state, **lift it up** to their common parent.

### Problem: Sibling Components Can't Share State

```tsx
// ❌ ProductList and Cart can't communicate
function App() {
    return (
        <div>
            <ProductList />  {/* Has products state */}
            <Cart />         {/* Needs products state */}
        </div>
    );
}
```

### Solution: Lift State to Parent

```tsx
function App() {
    // ✅ State lives in parent
    const [cart, setCart] = useState([]);

    const addToCart = (product) => {
        setCart([...cart, product]);
    };

    const removeFromCart = (productId) => {
        setCart(cart.filter(item => item.id !== productId));
    };

    return (
        <div>
            {/* Pass state and handlers down as props */}
            <ProductList onAddToCart={addToCart} />
            <Cart items={cart} onRemoveFromCart={removeFromCart} />
        </div>
    );
}

function ProductList({ onAddToCart }) {
    const products = [
        { id: 1, name: 'Laptop', price: 999 },
        { id: 2, name: 'Phone', price: 699 }
    ];

    return (
        <div>
            <h2>Products</h2>
            {products.map(product => (
                <div key={product.id}>
                    <h3>{product.name}</h3>
                    <p>${product.price}</p>
                    <button onClick={() => onAddToCart(product)}>
                        Add to Cart
                    </button>
                </div>
            ))}
        </div>
    );
}

function Cart({ items, onRemoveFromCart }) {
    const total = items.reduce((sum, item) => sum + item.price, 0);

    return (
        <div>
            <h2>Cart ({items.length})</h2>
            {items.map((item, index) => (
                <div key={index}>
                    <span>{item.name} - ${item.price}</span>
                    <button onClick={() => onRemoveFromCart(item.id)}>
                        Remove
                    </button>
                </div>
            ))}
            <p><strong>Total: ${total}</strong></p>
        </div>
    );
}
```

### Multi-Level Lifting

```tsx
function App() {
    const [user, setUser] = useState(null);

    return (
        <div>
            <Header user={user} />
            <Dashboard user={user} setUser={setUser} />
        </div>
    );
}

function Dashboard({ user, setUser }) {
    return (
        <div>
            <Sidebar user={user} />
            <MainContent user={user} setUser={setUser} />
        </div>
    );
}

function MainContent({ user, setUser }) {
    return (
        <div>
            <Profile user={user} onUpdate={setUser} />
        </div>
    );
}
```

**Problem with deep nesting:** Props need to be passed through many levels ("prop drilling").

**Solutions:**
- Context API (for global state like theme, user)
- State management libraries (Redux, Zustand)
- We'll cover Context in later chapters

---

## 5. Folder Structure

Organizing your project properly makes it maintainable and scalable.

### Basic Structure (Small Projects)

```
src/
├── components/
│   ├── Header.tsx
│   ├── Footer.tsx
│   ├── Button.tsx
│   └── Card.tsx
├── App.tsx
├── App.css
├── main.tsx
└── index.css
```

### Intermediate Structure (Medium Projects)

```
src/
├── components/          # Reusable UI components
│   ├── Button/
│   │   ├── Button.tsx
│   │   └── Button.css
│   ├── Card/
│   │   ├── Card.tsx
│   │   └── Card.css
│   └── Modal/
│       ├── Modal.tsx
│       └── Modal.css
│
├── pages/              # Page-level components
│   ├── Home.tsx
│   ├── About.tsx
│   └── Contact.tsx
│
├── hooks/              # Custom hooks
│   ├── useFetch.ts
│   ├── useLocalStorage.ts
│   └── useDebounce.ts
│
├── utils/              # Helper functions
│   ├── api.ts
│   ├── formatters.ts
│   └── validators.ts
│
├── assets/             # Static assets
│   ├── images/
│   └── icons/
│
├── App.tsx
├── main.tsx
└── index.css
```

### Advanced Structure (Large Projects)

```
src/
├── features/           # Feature-based organization
│   ├── auth/
│   │   ├── components/
│   │   │   ├── LoginForm.tsx
│   │   │   └── SignupForm.tsx
│   │   ├── hooks/
│   │   │   └── useAuth.ts
│   │   └── authService.ts
│   │
│   ├── products/
│   │   ├── components/
│   │   │   ├── ProductCard.tsx
│   │   │   └── ProductList.tsx
│   │   ├── hooks/
│   │   │   └── useProducts.ts
│   │   └── productService.ts
│   │
│   └── cart/
│       ├── components/
│       │   ├── Cart.tsx
│       │   └── CartItem.tsx
│       └── hooks/
│           └── useCart.ts
│
├── components/         # Shared components
│   ├── ui/             # Generic UI components
│   │   ├── Button/
│   │   ├── Input/
│   │   └── Modal/
│   └── layout/         # Layout components
│       ├── Header.tsx
│       ├── Footer.tsx
│       └── Sidebar.tsx
│
├── hooks/              # Shared hooks
│   ├── useFetch.ts
│   └── useLocalStorage.ts
│
├── utils/              # Utilities
│   ├── api.ts
│   ├── constants.ts
│   └── helpers.ts
│
├── context/            # Context providers
│   ├── AuthContext.tsx
│   └── ThemeContext.tsx
│
├── App.tsx
├── main.tsx
└── index.css
```

### Naming Conventions

```
✅ Components: PascalCase
   - Button.tsx
   - UserProfile.tsx
   - ProductCard.tsx

✅ Hooks: camelCase with 'use' prefix
   - useFetch.ts
   - useAuth.ts
   - useLocalStorage.ts

✅ Utils/Helpers: camelCase
   - formatDate.ts
   - validateEmail.ts
   - api.ts

✅ Constants: UPPER_SNAKE_CASE
   - API_URL
   - MAX_ITEMS
   - DEFAULT_THEME
```

---

## 6. Mini React Project: Weather App

Let's build a complete weather app using everything we've learned.

### Project Structure

```
weather-app/
├── src/
│   ├── components/
│   │   ├── SearchBar.tsx
│   │   ├── WeatherCard.tsx
│   │   └── LoadingSpinner.tsx
│   ├── hooks/
│   │   └── useWeather.ts
│   ├── utils/
│   │   └── api.ts
│   ├── App.tsx
│   ├── App.css
│   └── main.tsx
├── package.json
└── vite.config.ts
```

### API Utility

```tsx
// src/utils/api.ts

const API_KEY = 'your_api_key_here'; // Get from https://openweathermap.org/api
const BASE_URL = 'https://api.openweathermap.org/data/2.5';

export const fetchWeatherByCity = async (city) => {
    const response = await fetch(
        `${BASE_URL}/weather?q=${city}&appid=${API_KEY}&units=metric`
    );

    if (!response.ok) {
        if (response.status === 404) {
            throw new Error('City not found');
        }
        throw new Error('Failed to fetch weather data');
    }

    return response.json();
};

export const fetchWeatherByCoords = async (lat, lon) => {
    const response = await fetch(
        `${BASE_URL}/weather?lat=${lat}&lon=${lon}&appid=${API_KEY}&units=metric`
    );

    if (!response.ok) {
        throw new Error('Failed to fetch weather data');
    }

    return response.json();
};
```

### Custom Hook

```tsx
// src/hooks/useWeather.ts
import { useState, useEffect } from 'react';
import { fetchWeatherByCity, fetchWeatherByCoords } from '../utils/api';

function useWeather(initialCity = '') {
    const [weather, setWeather] = useState(null);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);
    const [city, setCity] = useState(initialCity);

    useEffect(() => {
        if (!city) return;

        const getWeather = async () => {
            try {
                setLoading(true);
                setError(null);
                const data = await fetchWeatherByCity(city);
                setWeather(data);
            } catch (err) {
                setError(err.message);
                setWeather(null);
            } finally {
                setLoading(false);
            }
        };

        getWeather();
    }, [city]);

    const searchCity = (newCity) => {
        setCity(newCity);
    };

    const getCurrentLocation = () => {
        setLoading(true);
        setError(null);

        navigator.geolocation.getCurrentPosition(
            async (position) => {
                try {
                    const { latitude, longitude } = position.coords;
                    const data = await fetchWeatherByCoords(latitude, longitude);
                    setWeather(data);
                    setCity(data.name);
                } catch (err) {
                    setError(err.message);
                } finally {
                    setLoading(false);
                }
            },
            (err) => {
                setError('Unable to get your location');
                setLoading(false);
            }
        );
    };

    return {
        weather,
        loading,
        error,
        searchCity,
        getCurrentLocation
    };
}

export default useWeather;
```

### Components

```tsx
// src/components/SearchBar.tsx
import { useState } from 'react';

function SearchBar({ onSearch, onGetLocation }) {
    const [input, setInput] = useState('');

    const handleSubmit = (e) => {
        e.preventDefault();
        if (input.trim()) {
            onSearch(input.trim());
            setInput('');
        }
    };

    return (
        <div className="search-bar">
            <form onSubmit={handleSubmit}>
                <input
                    type="text"
                    placeholder="Enter city name..."
                    value={input}
                    onChange={(e) => setInput(e.target.value)}
                />
                <button type="submit">Search</button>
            </form>
            <button onClick={onGetLocation} className="location-btn">
                📍 Use My Location
            </button>
        </div>
    );
}

export default SearchBar;
```

```tsx
// src/components/WeatherCard.tsx

function WeatherCard({ weather }) {
    const { name, main, weather: weatherInfo, wind } = weather;
    const weatherIcon = weatherInfo[0].icon;

    return (
        <div className="weather-card">
            <div className="location">
                <h2>{name}</h2>
                <p>{weatherInfo[0].description}</p>
            </div>

            <div className="weather-icon">
                <img 
                    src={`https://openweathermap.org/img/wn/${weatherIcon}@4x.png`}
                    alt={weatherInfo[0].description}
                />
            </div>

            <div className="temperature">
                <h1>{Math.round(main.temp)}°C</h1>
            </div>

            <div className="details">
                <div className="detail-item">
                    <span className="label">Feels like</span>
                    <span className="value">{Math.round(main.feels_like)}°C</span>
                </div>
                <div className="detail-item">
                    <span className="label">Humidity</span>
                    <span className="value">{main.humidity}%</span>
                </div>
                <div className="detail-item">
                    <span className="label">Wind Speed</span>
                    <span className="value">{wind.speed} m/s</span>
                </div>
                <div className="detail-item">
                    <span className="label">Min / Max</span>
                    <span className="value">
                        {Math.round(main.temp_min)}° / {Math.round(main.temp_max)}°
                    </span>
                </div>
            </div>
        </div>
    );
}

export default WeatherCard;
```

```tsx
// src/components/LoadingSpinner.tsx

function LoadingSpinner() {
    return (
        <div className="loading-spinner">
            <div className="spinner"></div>
            <p>Loading weather data...</p>
        </div>
    );
}

export default LoadingSpinner;
```

### Main App

```tsx
// src/App.tsx
import { useState } from 'react';
import useWeather from './hooks/useWeather';
import SearchBar from './components/SearchBar';
import WeatherCard from './components/WeatherCard';
import LoadingSpinner from './components/LoadingSpinner';
import './App.css';

function App() {
    const { weather, loading, error, searchCity, getCurrentLocation } = useWeather();

    return (
        <div className="app">
            <div className="container">
                <h1 className="title">🌤️ Weather App</h1>

                <SearchBar 
                    onSearch={searchCity}
                    onGetLocation={getCurrentLocation}
                />

                <div className="content">
                    {loading && <LoadingSpinner />}

                    {error && (
                        <div className="error-message">
                            <p>❌ {error}</p>
                        </div>
                    )}

                    {weather && !loading && <WeatherCard weather={weather} />}

                    {!weather && !loading && !error && (
                        <div className="empty-state">
                            <p>🔍 Search for a city to see the weather</p>
                        </div>
                    )}
                </div>
            </div>
        </div>
    );
}

export default App;
```

### Styles

```css
/* src/App.css */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
}

.app {
    width: 100%;
    padding: 20px;
}

.container {
    max-width: 600px;
    margin: 0 auto;
}

.title {
    text-align: center;
    color: white;
    font-size: 48px;
    margin-bottom: 30px;
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
}

/* Search Bar */
.search-bar {
    background: white;
    padding: 20px;
    border-radius: 12px;
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
    margin-bottom: 30px;
}

.search-bar form {
    display: flex;
    gap: 10px;
    margin-bottom: 10px;
}

.search-bar input {
    flex: 1;
    padding: 12px 16px;
    border: 2px solid #e5e7eb;
    border-radius: 8px;
    font-size: 16px;
}

.search-bar input:focus {
    outline: none;
    border-color: #667eea;
}

.search-bar button {
    padding: 12px 24px;
    background: #667eea;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: background 0.2s;
}

.search-bar button:hover {
    background: #5568d3;
}

.location-btn {
    width: 100%;
    background: #10b981 !important;
}

.location-btn:hover {
    background: #059669 !important;
}

/* Weather Card */
.weather-card {
    background: white;
    padding: 30px;
    border-radius: 12px;
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
}

.location h2 {
    font-size: 32px;
    color: #1f2937;
    margin-bottom: 8px;
}

.location p {
    color: #6b7280;
    font-size: 18px;
    text-transform: capitalize;
}

.weather-icon {
    text-align: center;
    margin: 20px 0;
}

.weather-icon img {
    width: 150px;
    height: 150px;
}

.temperature {
    text-align: center;
    margin-bottom: 30px;
}

.temperature h1 {
    font-size: 64px;
    color: #667eea;
}

.details {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
}

.detail-item {
    display: flex;
    flex-direction: column;
    gap: 4px;
}

.detail-item .label {
    color: #6b7280;
    font-size: 14px;
}

.detail-item .value {
    color: #1f2937;
    font-size: 18px;
    font-weight: 600;
}

/* Loading Spinner */
.loading-spinner {
    text-align: center;
    padding: 60px 20px;
    background: white;
    border-radius: 12px;
}

.spinner {
    width: 50px;
    height: 50px;
    border: 4px solid #e5e7eb;
    border-top-color: #667eea;
    border-radius: 50%;
    margin: 0 auto 20px;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    to { transform: rotate(360deg); }
}

.loading-spinner p {
    color: #6b7280;
}

/* Error Message */
.error-message {
    background: #fee2e2;
    color: #dc2626;
    padding: 20px;
    border-radius: 12px;
    text-align: center;
}

/* Empty State */
.empty-state {
    text-align: center;
    padding: 60px 20px;
    background: white;
    border-radius: 12px;
    color: #6b7280;
    font-size: 18px;
}

/* Responsive */
@media (max-width: 640px) {
    .title {
        font-size: 36px;
    }

    .temperature h1 {
        font-size: 48px;
    }

    .details {
        grid-template-columns: 1fr;
    }
}
```

---

## Summary

In this chapter, you learned:

✅ **useEffect** — Running side effects (API calls, timers, subscriptions)  
✅ **Data fetching** — Getting data with loading and error states  
✅ **Cleanup functions** — Preventing memory leaks  
✅ **Lifting state up** — Sharing state between components  
✅ **Folder structure** — Organizing real-world React projects  
✅ **Complete project** — Built a weather app with all React fundamentals  

**You are now React-ready for Next.js!** 🎉

---

## Practice Exercises

1. Build a **photo gallery** that fetches images from an API (Unsplash or Pexels).

2. Create a **real-time clock** that updates every second using `setInterval`.

3. Build a **GitHub user search** that fetches and displays user data.

4. Create a **notes app** with localStorage persistence (save/load notes).

5. Build a **product filter** with search, category filter, and price range.

---

**🎉 Congratulations!** You've completed the React fundamentals section!

You now have a solid foundation in:
- ✅ Components and Props
- ✅ State and Events
- ✅ Side Effects and Data Fetching
- ✅ React Best Practices