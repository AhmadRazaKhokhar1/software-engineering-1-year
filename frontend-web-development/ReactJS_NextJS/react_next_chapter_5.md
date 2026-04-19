# Chapter 5: Introduction to Next.js & App Router

## Introduction

Welcome to **Next.js** — the React framework that powers modern web applications!

You've mastered React fundamentals. Now it's time to level up with Next.js, which adds:

- 🚀 **Server-side rendering (SSR)** — Better performance and SEO
- 🗂️ **File-based routing** — No more React Router setup
- ⚡ **Built-in optimization** — Images, fonts, scripts automatically optimized
- 🔧 **Full-stack capabilities** — API routes and server actions
- 📦 **Zero configuration** — Webpack, TypeScript, ESLint built-in

This chapter covers:

1. What Next.js is and why it exists
2. Next.js vs React SPA
3. App Router vs Pages Router
4. File-based routing
5. Layouts and nested routes
6. Metadata (SEO)
7. Server Components vs Client Components

By the end, you'll understand **Next.js architecture** and be ready to build production apps.

---

## 1. What is Next.js?

**Next.js** is a **React framework** for building production-ready web applications.

### React vs Next.js

| Feature | React (SPA) | Next.js |
|---------|-------------|---------|
| **Rendering** | Client-side only | Server + Client |
| **Routing** | Manual (React Router) | File-based (automatic) |
| **SEO** | Poor (JS must load first) | Excellent (server-rendered) |
| **Initial load** | Slower (large JS bundle) | Faster (server-rendered HTML) |
| **API routes** | Separate backend needed | Built-in |
| **Image optimization** | Manual | Automatic |
| **Code splitting** | Manual | Automatic |
| **TypeScript** | Manual setup | Built-in |
| **Configuration** | Manual (Webpack, Babel) | Zero config |

### Why Next.js Over React SPA?

**React SPA (Create React App / Vite):**

```
User visits page → Browser downloads JS → JS executes → React renders → Content shows
```

Problems:
- ❌ Slow initial load
- ❌ Bad SEO (search engines see empty HTML)
- ❌ No built-in routing
- ❌ Separate backend needed for APIs

**Next.js:**

```
User visits page → Server renders HTML → Browser shows content instantly → JS hydrates for interactivity
```

Benefits:
- ✅ Fast initial load (HTML ready)
- ✅ Great SEO (search engines see full content)
- ✅ File-based routing
- ✅ Built-in API routes

### Real-World Example

**React SPA (initial HTML):**

```html
<!DOCTYPE html>
<html>
  <head>
    <title>React App</title>
  </head>
  <body>
    <div id="root"></div>  <!-- Empty! -->
    <script src="/bundle.js"></script>
  </body>
</html>
```

Search engines see nothing. Users see blank screen until JS loads.

**Next.js (initial HTML):**

```html
<!DOCTYPE html>
<html>
  <head>
    <title>My Next.js App</title>
    <meta name="description" content="..." />
  </head>
  <body>
    <div>
      <h1>Welcome to My Site</h1>
      <p>This content is already in the HTML!</p>
      <!-- Full content visible immediately -->
    </div>
    <script src="/bundle.js"></script>
  </body>
</html>
```

Search engines and users see content immediately.

---

## 2. Why Next.js Over React SPA

### Performance

**React SPA flow:**

1. Browser requests page
2. Server sends minimal HTML + large JS bundle
3. Browser downloads JS (slow on 3G)
4. JS executes and React renders
5. User finally sees content

**Next.js flow:**

1. Browser requests page
2. Server renders React components to HTML
3. Browser receives full HTML (content visible instantly)
4. Browser downloads smaller JS chunks
5. JS "hydrates" to add interactivity

**Result:** Users see content 2-3x faster with Next.js.

### SEO (Search Engine Optimization)

**React SPA:**

```html
<!-- What Google sees -->
<div id="root"></div>
```

Google can execute JavaScript, but:
- It's slower and less reliable
- Other search engines might not
- Social media previews (Facebook, Twitter) don't work

**Next.js:**

```html
<!-- What Google sees -->
<article>
  <h1>10 Tips for Learning React</h1>
  <p>React is a JavaScript library...</p>
  <img src="/blog-image.jpg" alt="React tips" />
</article>
```

Google sees full content immediately → better rankings.

### Developer Experience

**React SPA:**

```bash
# Manual setup
npm install react-router-dom
# Configure routes manually
# Set up code splitting manually
# Configure image optimization manually
# Set up API proxy manually
```

**Next.js:**

```bash
# Everything built-in
npx create-next-app@latest
# Routing: automatic (file-based)
# Code splitting: automatic
# Image optimization: <Image /> component
# API routes: built-in
```

### Full-Stack Capabilities

**React SPA:**

```
Frontend (React) → Separate backend (Express, Django, etc.)
```

Need to:
- Set up separate server
- Handle CORS
- Deploy two applications

**Next.js:**

```
Frontend + Backend in one application
```

API routes in the same project:

```typescript
// app/api/users/route.tsx
export async function GET() {
  const users = await db.users.findMany();
  return Response.json(users);
}
```

---

## 3. App Router vs Pages Router

Next.js has **two routing systems**:

1. **Pages Router** (legacy, but still supported)
2. **App Router** (new, recommended — what we'll use)

### Pages Router (Old Way)

```
pages/
├── index.js           → /
├── about.js           → /about
├── blog/
│   ├── index.js       → /blog
│   └── [id].js        → /blog/123
└── api/
    └── hello.js       → /api/hello
```

Features:
- File-based routing ✅
- Client-side rendering by default
- Manual data fetching (getServerSideProps, getStaticProps)
- All components are Client Components

### App Router (New Way - We'll Use This)

```
app/
├── page.tsx            → /
├── layout.tsx          → Root layout
├── about/
│   └── page.tsx        → /about
├── blog/
│   ├── page.tsx        → /blog
│   └── [id]/
│       └── page.tsx    → /blog/123
└── api/
    └── users/
        └── route.ts   → /api/users
```

Features:
- File-based routing ✅
- **Server Components by default** (better performance)
- Nested layouts
- Streaming and Suspense
- Server Actions
- Better data fetching

### Why We Use App Router

| Feature | Pages Router | App Router |
|---------|--------------|------------|
| Server Components | ❌ No | ✅ Yes (default) |
| Nested layouts | ❌ Limited | ✅ Full support |
| Streaming | ❌ No | ✅ Yes |
| Server Actions | ❌ No | ✅ Yes |
| Loading UI | Manual | Built-in |
| Error handling | Manual | Built-in |
| Future of Next.js | Maintenance mode | Active development |

**We'll focus exclusively on App Router.**

---

## 4. File-Based Routing

In Next.js, **folders and files define routes** — no manual configuration needed.

### Basic Routes

```
app/
├── page.tsx                    → / (home)
├── about/
│   └── page.tsx                → /about
├── contact/
│   └── page.tsx                → /contact
└── blog/
    └── page.tsx                → /blog
```

**Example:**

```tsx
// app/page.tsx (Home page - /)
export default function HomePage() {
  return (
    <div>
      <h1>Welcome to Next.js!</h1>
      <p>This is the home page</p>
    </div>
  );
}
```

```tsx
// app/about/page.tsx (About page - /about)
export default function AboutPage() {
  return (
    <div>
      <h1>About Us</h1>
      <p>Learn more about our company</p>
    </div>
  );
}
```

### Special Files in App Router

| File | Purpose |
|------|---------|
| `page.js` | Makes route publicly accessible |
| `layout.js` | Shared UI wrapper for route segment |
| `loading.js` | Loading UI (automatic Suspense) |
| `error.js` | Error boundary |
| `not-found.js` | 404 page |
| `route.js` | API endpoint |

### Dynamic Routes

Use **[brackets]** for dynamic segments:

```
app/
└── blog/
    └── [id]/
        └── page.tsx            → /blog/123, /blog/456, etc.
```

```tsx
// app/blog/[id]/page.tsx
export default function BlogPost({ params }) {
  return (
    <div>
      <h1>Blog Post {params.id}</h1>
      <p>This is blog post with ID: {params.id}</p>
    </div>
  );
}
```

**Usage:**
- `/blog/1` → `params.id = "1"`
- `/blog/my-first-post` → `params.id = "my-first-post"`
- `/blog/123` → `params.id = "123"`

### Nested Dynamic Routes

```
app/
└── shop/
    └── [category]/
        └── [product]/
            └── page.tsx        → /shop/electronics/laptop
```

```tsx
// app/shop/[category]/[product]/page.tsx
export default function ProductPage({ params }) {
  return (
    <div>
      <h1>Product: {params.product}</h1>
      <p>Category: {params.category}</p>
    </div>
  );
}
```

**Usage:**
- `/shop/electronics/laptop` → `{ category: "electronics", product: "laptop" }`
- `/shop/clothing/shirt` → `{ category: "clothing", product: "shirt" }`

### Catch-All Routes

Use **[...slug]** to catch all segments:

```
app/
└── docs/
    └── [...slug]/
        └── page.tsx
```

```tsx
// app/docs/[...slug]/page.tsx
export default function DocsPage({ params }) {
  return (
    <div>
      <h1>Documentation</h1>
      <p>Path segments: {params.slug.join('/')}</p>
    </div>
  );
}
```

**Usage:**
- `/docs/getting-started` → `params.slug = ["getting-started"]`
- `/docs/api/authentication` → `params.slug = ["api", "authentication"]`
- `/docs/a/b/c/d` → `params.slug = ["a", "b", "c", "d"]`

### Optional Catch-All Routes

Use **[[...slug]]** to make it optional:

```
app/
└── shop/
    └── [[...slug]]/
        └── page.tsx
```

This matches:
- `/shop` → `params.slug = undefined`
- `/shop/electronics` → `params.slug = ["electronics"]`
- `/shop/electronics/laptop` → `params.slug = ["electronics", "laptop"]`

---

## 5. Layouts

**Layouts** are UI that wraps multiple pages — shared headers, footers, sidebars, etc.

### Root Layout (Required)

Every Next.js app must have a root layout:

```tsx
// app/layout.tsx (Root layout - wraps entire app)
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <header>
          <nav>
            <a href="/">Home</a>
            <a href="/about">About</a>
            <a href="/blog">Blog</a>
          </nav>
        </header>

        <main>
          {children}  {/* Page content goes here */}
        </main>

        <footer>
          <p>&copy; 2024 My Next.js App</p>
        </footer>
      </body>
    </html>
  );
}
```

**Key points:**
- Must return `<html>` and `<body>` tags
- `{children}` is where page content renders
- Shared across all pages

### Nested Layouts

You can have layouts at any level:

```
app/
├── layout.tsx              (Root layout)
├── page.tsx                (Home)
├── about/
│   └── page.tsx            (About)
└── blog/
    ├── layout.tsx          (Blog layout)
    ├── page.tsx            (Blog list)
    └── [id]/
        └── page.tsx        (Blog post)
```

```tsx
// app/layout.tsx (Root layout)
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <header>Global Header</header>
        {children}
        <footer>Global Footer</footer>
      </body>
    </html>
  );
}
```

```tsx
// app/blog/layout.tsx (Blog layout)
export default function BlogLayout({ children }) {
  return (
    <div className="blog-container">
      <aside>
        <h3>Blog Sidebar</h3>
        <ul>
          <li>Recent Posts</li>
          <li>Categories</li>
          <li>Tags</li>
        </ul>
      </aside>
      <div className="blog-content">
        {children}
      </div>
    </div>
  );
}
```

**Result:**

When you visit `/blog` or `/blog/123`:

```
<html>
  <body>
    <header>Global Header</header>     ← Root layout
    
    <div class="blog-container">       ← Blog layout
      <aside>Blog Sidebar</aside>
      <div class="blog-content">
        <!-- Page content here -->       ← page.tsx
      </div>
    </div>
    
    <footer>Global Footer</footer>     ← Root layout
  </body>
</html>
```

### Layout Persistence

Layouts **don't re-render** when navigating between pages in the same segment:

```
/ → /about          → Root layout re-renders
/blog → /blog/123   → Root layout stays, Blog layout stays
```

This preserves state and improves performance.

### Templates (Alternative to Layouts)

If you need layout to re-render on every navigation:

```tsx
// app/blog/template.tsx
export default function BlogTemplate({ children }) {
  return (
    <div>
      {children}
    </div>
  );
}
```

**Layout vs Template:**

| Feature | Layout | Template |
|---------|--------|----------|
| Re-renders on navigation | ❌ No (persists) | ✅ Yes (new instance) |
| Preserves state | ✅ Yes | ❌ No |
| Use case | Headers, footers | Animations, analytics |

---

## 6. Metadata (SEO)

Next.js provides two ways to add metadata for SEO:

### Static Metadata

```tsx
// app/page.tsx
export const metadata = {
  title: 'Home - My Next.js App',
  description: 'Welcome to my Next.js application',
  keywords: ['Next.js', 'React', 'Web Development'],
  openGraph: {
    title: 'My Next.js App',
    description: 'Welcome to my app',
    images: ['/og-image.jpg'],
  },
  twitter: {
    card: 'summary_large_image',
    title: 'My Next.js App',
    description: 'Welcome to my app',
    images: ['/twitter-image.jpg'],
  },
};

export default function HomePage() {
  return <h1>Home</h1>;
}
```

**Rendered HTML:**

```html
<head>
  <title>Home - My Next.js App</title>
  <meta name="description" content="Welcome to my Next.js application" />
  <meta name="keywords" content="Next.js, React, Web Development" />
  
  <!-- Open Graph (Facebook, LinkedIn) -->
  <meta property="og:title" content="My Next.js App" />
  <meta property="og:description" content="Welcome to my app" />
  <meta property="og:image" content="/og-image.jpg" />
  
  <!-- Twitter -->
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:title" content="My Next.js App" />
  <meta name="twitter:description" content="Welcome to my app" />
  <meta name="twitter:image" content="/twitter-image.jpg" />
</head>
```

### Dynamic Metadata

For dynamic routes, generate metadata based on params:

```tsx
// app/blog/[id]/page.tsx
export async function generateMetadata({ params }) {
  // Fetch post data
  const post = await fetch(`https://api.example.com/posts/${params.id}`)
    .then(res => res.json());

  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      description: post.excerpt,
      images: [post.image],
    },
  };
}

export default function BlogPost({ params }) {
  return <article>...</article>;
}
```

### Metadata Template (Layout)

Share metadata template across pages:

```tsx
// app/layout.tsx
export const metadata = {
  title: {
    template: '%s | My Next.js App',  // %s = page title
    default: 'My Next.js App',
  },
  description: 'A Next.js application',
};
```

```tsx
// app/about/page.tsx
export const metadata = {
  title: 'About Us',  // Becomes "About Us | My Next.js App"
};
```

---

## 7. use client vs Server Components

This is **the most important concept** in Next.js App Router.

### Server Components (Default)

By default, **all components in App Router are Server Components**.

```tsx
// app/page.tsx (Server Component by default)
export default function HomePage() {
  return <h1>This runs on the SERVER</h1>;
}
```

**Characteristics:**

✅ Run on the server  
✅ Can directly access databases  
✅ Can use async/await for data fetching  
✅ Don't increase client JS bundle  
✅ Better performance  
✅ Cannot use React hooks (useState, useEffect, etc.)  
✅ Cannot use browser APIs (window, localStorage, etc.)  
✅ Cannot add event handlers (onClick, onChange, etc.)  

**When to use:**
- Fetching data
- Accessing backend resources
- Keeping sensitive data on server (API keys)
- Reducing client JavaScript

### Client Components

Add **`"use client"`** at the top to make a component render on the client.

```tsx
// app/components/Counter.tsx
"use client";  // ← Makes this a Client Component

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

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

**Characteristics:**

✅ Run in the browser  
✅ Can use React hooks (useState, useEffect, etc.)  
✅ Can use browser APIs (window, localStorage, etc.)  
✅ Can add event handlers (onClick, onChange, etc.)  
✅ Increase client JS bundle  
❌ Cannot directly access databases  
❌ Cannot use async component syntax  

**When to use:**
- Interactivity (buttons, forms, modals)
- React hooks (useState, useEffect, etc.)
- Browser-only APIs (localStorage, geolocation, etc.)
- Event listeners

### Mixing Server and Client Components

**✅ Good Pattern: Client Component inside Server Component**

```tsx
// app/page.tsx (Server Component)
import Counter from './components/Counter';  // Client Component

export default function HomePage() {
  // This runs on the server
  const serverData = "Data from server";

  return (
    <div>
      <h1>{serverData}</h1>
      <Counter />  {/* Client Component for interactivity */}
    </div>
  );
}
```

**❌ Bad Pattern: Server Component as child of Client Component**

```tsx
// app/components/ClientWrapper.tsx
"use client";

import ServerComponent from './ServerComponent';  // ❌ Won't work as expected

export default function ClientWrapper() {
  return (
    <div>
      <ServerComponent />  {/* ❌ This becomes a Client Component! */}
    </div>
  );
}
```

**Solution: Pass Server Component as prop**

```tsx
// app/page.tsx (Server Component)
import ClientWrapper from './components/ClientWrapper';
import ServerComponent from './components/ServerComponent';

export default function HomePage() {
  return (
    <ClientWrapper>
      <ServerComponent />  {/* ✅ Passed as children */}
    </ClientWrapper>
  );
}
```

```tsx
// app/components/ClientWrapper.tsx
"use client";

export default function ClientWrapper({ children }) {
  return (
    <div className="wrapper">
      {children}  {/* Server Component stays on server */}
    </div>
  );
}
```

### Decision Tree: Server vs Client Component?

```
Does it need interactivity (clicks, forms, state)?
├─ Yes → Client Component ("use client")
└─ No → Server Component (default)

Does it use React hooks (useState, useEffect)?
├─ Yes → Client Component
└─ No → Server Component

Does it fetch data?
├─ From browser (useEffect) → Client Component
└─ From server (async) → Server Component

Does it use browser APIs (localStorage, window)?
├─ Yes → Client Component
└─ No → Server Component
```

### Examples

**Server Component (data fetching):**

```tsx
// app/users/page.tsx (Server Component)
async function getUsers() {
  const res = await fetch('https://jsonplaceholder.typicode.com/users');
  return res.json();
}

export default async function UsersPage() {
  const users = await getUsers();  // ✅ Async component

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

**Client Component (interactivity):**

```tsx
// app/components/SearchBox.tsx
"use client";

import { useState } from 'react';

export default function SearchBox({ onSearch }) {
  const [query, setQuery] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    onSearch(query);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search..."
      />
      <button type="submit">Search</button>
    </form>
  );
}
```

**Combining both:**

```tsx
// app/products/page.tsx (Server Component)
import SearchBox from '../components/SearchBox';  // Client Component

async function getProducts() {
  const res = await fetch('https://api.example.com/products');
  return res.json();
}

export default async function ProductsPage() {
  const products = await getProducts();  // Server-side data fetching

  return (
    <div>
      <h1>Products</h1>
      <SearchBox onSearch={(query) => console.log(query)} />  {/* Client interactivity */}
      
      <div>
        {products.map(product => (
          <div key={product.id}>
            <h3>{product.name}</h3>
            <p>${product.price}</p>
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

## Summary

In this chapter, you learned:

✅ **What Next.js is** — A React framework for production apps  
✅ **Why Next.js over React SPA** — Better performance, SEO, and DX  
✅ **App Router vs Pages Router** — Modern App Router is the way forward  
✅ **File-based routing** — Folders and files define routes automatically  
✅ **Layouts** — Shared UI that wraps pages  
✅ **Metadata** — SEO optimization with static and dynamic metadata  
✅ **Server vs Client Components** — The most important Next.js concept  

You now understand the **Next.js architecture**!

---

## Practice Exercises

1. Create a Next.js app with routes: `/`, `/about`, `/blog`, `/blog/[id]`

2. Build a root layout with a header (logo + nav) and footer

3. Create a blog layout with a sidebar (only for blog pages)

4. Add metadata to your pages (title, description, Open Graph)

5. Build a page with a Server Component (fetches data) and a Client Component (interactive button)

---

**Excellent progress!** You now understand Next.js architecture and the App Router.