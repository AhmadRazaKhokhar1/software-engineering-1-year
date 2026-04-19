# Chapter 6: Rendering Strategies & Data Fetching in Next.js

## Introduction

Next.js gives you **multiple rendering strategies** to optimize performance and user experience. Understanding when to use each is crucial for building fast, scalable applications.

This chapter covers:

1. **Server Components** — Render on the server (default)
2. **Client Components** — Render in the browser (interactive)
3. **SSR (Server-Side Rendering)** — Render on each request
4. **SSG (Static Site Generation)** — Pre-render at build time
5. **ISR (Incremental Static Regeneration)** — Update static pages
6. **Fetch caching & revalidation** — Control data freshness
7. **Environment variables** — Secure configuration

By the end, you'll know **how to fetch data efficiently** and choose the right rendering strategy for each page.

---

## 1. Server Components

**Server Components** are the default in Next.js App Router. They run **only on the server**.

### Basic Server Component

```tsx
// app/users/page.tsx (Server Component by default)

async function getUsers() {
  const res = await fetch('https://jsonplaceholder.typicode.com/users');
  return res.json();
}

export default async function UsersPage() {
  const users = await getUsers();  // ✅ Fetch data on server

  return (
    <div>
      <h1>Users</h1>
      <ul>
        {users.map(user => (
          <li key={user.id}>{user.name} - {user.email}</li>
        ))}
      </ul>
    </div>
  );
}
```

**What happens:**

1. User requests `/users`
2. Server fetches data from API
3. Server renders component to HTML
4. Browser receives complete HTML
5. User sees content immediately (no loading spinner!)

### Benefits of Server Components

✅ **Zero client JavaScript** — Reduces bundle size  
✅ **Direct database access** — No API layer needed  
✅ **Server-only code** — API keys stay secure  
✅ **Automatic code splitting** — Only needed code sent  
✅ **Better performance** — Server is faster than client  

### Direct Database Access

```tsx
// app/products/page.tsx
import { db } from '@/lib/db';  // Database client

export default async function ProductsPage() {
  // ✅ Query database directly (server-side only)
  const products = await db.product.findMany({
    orderBy: { createdAt: 'desc' },
    take: 10,
  });

  return (
    <div>
      <h1>Products</h1>
      {products.map(product => (
        <div key={product.id}>
          <h2>{product.name}</h2>
          <p>${product.price}</p>
        </div>
      ))}
    </div>
  );
}
```

**Important:** Database credentials never reach the client!

### Multiple Parallel Fetches

```tsx
// app/dashboard/page.tsx

async function getUsers() {
  const res = await fetch('https://api.example.com/users');
  return res.json();
}

async function getPosts() {
  const res = await fetch('https://api.example.com/posts');
  return res.json();
}

async function getStats() {
  const res = await fetch('https://api.example.com/stats');
  return res.json();
}

export default async function Dashboard() {
  // ✅ Fetch all in parallel with Promise.all
  const [users, posts, stats] = await Promise.all([
    getUsers(),
    getPosts(),
    getStats(),
  ]);

  return (
    <div>
      <h1>Dashboard</h1>
      <div>Users: {users.length}</div>
      <div>Posts: {posts.length}</div>
      <div>Total Views: {stats.totalViews}</div>
    </div>
  );
}
```

---

## 2. Client Components

Use **Client Components** when you need interactivity, browser APIs, or React hooks.

### Basic Client Component

```tsx
// app/components/Counter.tsx
'use client';  // ← Makes this a Client Component

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

### When to Use Client Components

✅ **Interactivity** — Buttons, forms, modals  
✅ **React hooks** — useState, useEffect, useContext  
✅ **Browser APIs** — localStorage, geolocation, WebSockets  
✅ **Event listeners** — onClick, onChange, onScroll  
✅ **Third-party libraries** — Many need browser environment  

### Client Component with Data Fetching

```tsx
// app/components/Posts.tsx
'use client';

import { useState, useEffect } from 'react';

export default function Posts() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/posts')
      .then(res => res.json())
      .then(data => {
        setPosts(data);
        setLoading(false);
      });
  }, []);

  if (loading) return <div>Loading...</div>;

  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

**Note:** Server Components are better for data fetching. Use Client Components only when you need client-side features.

### Combining Server and Client Components

```tsx
// app/page.tsx (Server Component)
import Counter from './components/Counter';  // Client Component

async function getData() {
  const res = await fetch('https://api.example.com/data');
  return res.json();
}

export default async function HomePage() {
  const data = await getData();  // Server-side fetch

  return (
    <div>
      <h1>{data.title}</h1>
      <p>Server-rendered: {data.description}</p>
      
      <Counter />  {/* Client-side interactivity */}
    </div>
  );
}
```

**Best Practice Pattern:**

```
Server Component (data fetching)
  ↓
Client Component (interactivity)
  ↓
Server Component (static content) ← Pass as children prop
```

---

## 3. SSR (Server-Side Rendering)

**SSR** renders pages on the server **for each request**. This is the default behavior for Server Components.

### Dynamic Rendering (SSR)

```tsx
// app/posts/page.tsx

async function getPosts() {
  const res = await fetch('https://api.example.com/posts', {
    cache: 'no-store'  // ← Force dynamic rendering (no cache)
  });
  return res.json();
}

export default async function PostsPage() {
  const posts = await getPosts();  // Fetched on every request

  return (
    <div>
      <h1>Latest Posts</h1>
      <p>Generated at: {new Date().toLocaleTimeString()}</p>
      <ul>
        {posts.map(post => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}
```

**When page is requested:**

1. User visits `/posts`
2. Server fetches fresh data from API
3. Server renders HTML with latest data
4. Browser receives HTML
5. User sees up-to-date content

### Force Dynamic Rendering

```tsx
// Option 1: Use dynamic functions
export default async function Page() {
  const headers = headers();  // Makes page dynamic
  const searchParams = searchParams();  // Makes page dynamic
  const cookies = cookies();  // Makes page dynamic
  
  // ...
}

// Option 2: Disable caching
export const dynamic = 'force-dynamic';

export default async function Page() {
  // This page is always rendered dynamically
}

// Option 3: No-cache fetch
async function getData() {
  const res = await fetch('https://api.example.com/data', {
    cache: 'no-store'
  });
  return res.json();
}
```

### When to Use SSR

✅ **Personalized content** — User-specific data  
✅ **Real-time data** — Stock prices, live scores  
✅ **Frequently changing** — News feeds, social media  
✅ **Authentication-dependent** — User dashboards  

---

## 4. SSG (Static Site Generation)

**SSG** pre-renders pages at **build time**. The HTML is generated once and reused for all requests.

### Static Rendering (Default)

```tsx
// app/about/page.tsx

export default function AboutPage() {
  return (
    <div>
      <h1>About Us</h1>
      <p>This page is pre-rendered at build time.</p>
    </div>
  );
}
```

**When you build:**

```bash
npm run build
```

Next.js generates `about.html` once. Every request serves the same HTML file (ultra-fast!).

### Fetch with Caching (Static)

```tsx
// app/posts/page.tsx

async function getPosts() {
  const res = await fetch('https://api.example.com/posts');
  // ✅ By default, Next.js caches this forever
  return res.json();
}

export default async function PostsPage() {
  const posts = await getPosts();  // Fetched once at build time

  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

### Static with Dynamic Routes

```tsx
// app/blog/[slug]/page.tsx

// ✅ Tell Next.js which paths to pre-render
export async function generateStaticParams() {
  const posts = await fetch('https://api.example.com/posts')
    .then(res => res.json());

  return posts.map(post => ({
    slug: post.slug,
  }));
}

async function getPost(slug) {
  const res = await fetch(`https://api.example.com/posts/${slug}`);
  return res.json();
}

export default async function BlogPost({ params }) {
  const post = await getPost(params.slug);

  return (
    <article>
      <h1>{post.title}</h1>
      <div>{post.content}</div>
    </article>
  );
}
```

**At build time:**

```
Generating static pages:
  /blog/post-1
  /blog/post-2
  /blog/post-3
  ...
```

All pages are pre-rendered as HTML files.

### When to Use SSG

✅ **Static content** — About, FAQ, landing pages  
✅ **Blog posts** — Content rarely changes  
✅ **Product pages** — Updates are scheduled  
✅ **Documentation** — Static content  

---

## 5. ISR (Incremental Static Regeneration)

**ISR** lets you update static pages **after build** without rebuilding the entire site.

### Time-Based Revalidation

```tsx
// app/posts/page.tsx

async function getPosts() {
  const res = await fetch('https://api.example.com/posts', {
    next: { revalidate: 3600 }  // ← Revalidate every 1 hour
  });
  return res.json();
}

export default async function PostsPage() {
  const posts = await getPosts();

  return (
    <div>
      <h1>Blog Posts</h1>
      <p>Updated every hour</p>
      <ul>
        {posts.map(post => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}
```

**How it works:**

1. **First request (0:00)** → Generates static HTML
2. **Next requests (0:00 - 0:59)** → Serves cached HTML (instant!)
3. **Request after 1 hour (1:00)** → Serves stale HTML, regenerates in background
4. **Subsequent requests** → Serves fresh HTML

### On-Demand Revalidation

Trigger regeneration manually:

```tsx
// app/api/revalidate/route.tsx
import { revalidatePath, revalidateTag } from 'next/cache';

export async function POST(request) {
  const { path, tag } = await request.json();

  if (path) {
    revalidatePath(path);  // Revalidate specific path
  }

  if (tag) {
    revalidateTag(tag);  // Revalidate all pages with this tag
  }

  return Response.json({ revalidated: true });
}
```

**Trigger from webhook:**

```bash
# When you publish a new blog post
curl -X POST https://yoursite.com/api/revalidate \
  -H "Content-Type: application/json" \
  -d '{"path": "/blog"}'
```

### Tag-Based Revalidation

```tsx
// app/posts/page.tsx

async function getPosts() {
  const res = await fetch('https://api.example.com/posts', {
    next: { 
      revalidate: 3600,
      tags: ['posts']  // ← Tag for revalidation
    }
  });
  return res.json();
}

export default async function PostsPage() {
  const posts = await getPosts();
  // ...
}
```

```tsx
// app/api/revalidate/route.tsx
import { revalidateTag } from 'next/cache';

export async function POST() {
  revalidateTag('posts');  // Revalidate all fetches tagged 'posts'
  return Response.json({ revalidated: true });
}
```

### When to Use ISR

✅ **E-commerce** — Product inventory updates hourly  
✅ **News sites** — Articles published periodically  
✅ **Blogs** — New posts added occasionally  
✅ **Marketing pages** — Updated weekly/monthly  

---

## 6. Fetch Caching & Revalidation

Next.js extends `fetch` with powerful caching options.

### Default Behavior

```tsx
// ✅ Cached forever (static)
const res = await fetch('https://api.example.com/data');
```

### No Caching (Dynamic)

```tsx
// ❌ Never cached (always fresh)
const res = await fetch('https://api.example.com/data', {
  cache: 'no-store'
});
```

### Time-Based Revalidation

```tsx
// ⏱️ Revalidate every 60 seconds
const res = await fetch('https://api.example.com/data', {
  next: { revalidate: 60 }
});
```

### Tagging for Revalidation

```tsx
// 🏷️ Tag for on-demand revalidation
const res = await fetch('https://api.example.com/data', {
  next: { 
    revalidate: 3600,
    tags: ['posts', 'homepage']
  }
});
```

### Complete Example

```tsx
// app/products/page.tsx

// Page-level revalidation
export const revalidate = 3600;  // Revalidate page every hour

async function getProducts() {
  // Fetch with custom revalidation
  const res = await fetch('https://api.example.com/products', {
    next: { 
      revalidate: 1800,  // 30 minutes
      tags: ['products']
    }
  });
  return res.json();
}

async function getFeatured() {
  // Never cache (always fresh)
  const res = await fetch('https://api.example.com/featured', {
    cache: 'no-store'
  });
  return res.json();
}

export default async function ProductsPage() {
  const [products, featured] = await Promise.all([
    getProducts(),    // Cached for 30 min
    getFeatured(),    // Always fresh
  ]);

  return (
    <div>
      <section>
        <h2>Featured (Live)</h2>
        {featured.map(item => (
          <div key={item.id}>{item.name}</div>
        ))}
      </section>

      <section>
        <h2>All Products (Cached 30min)</h2>
        {products.map(product => (
          <div key={product.id}>{product.name}</div>
        ))}
      </section>
    </div>
  );
}
```

### Cache Options Summary

| Option | Behavior | Use Case |
|--------|----------|----------|
| Default (none) | Cache forever | Static content |
| `cache: 'no-store'` | Never cache | Real-time data |
| `next: { revalidate: 60 }` | Revalidate every 60s | Periodically updated |
| `next: { tags: ['x'] }` | Tag for on-demand revalidation | Event-driven updates |

---

## 7. Environment Variables

Store sensitive data and configuration in environment variables.

### Creating Environment Variables

Create `.env.local`:

```bash
# .env.local (not committed to Git)

# Public variables (exposed to browser)
NEXT_PUBLIC_API_URL=https://api.example.com
NEXT_PUBLIC_ANALYTICS_ID=UA-12345678

# Private variables (server-only)
DATABASE_URL=postgresql://user:pass@localhost:5432/db
API_SECRET_KEY=super-secret-key
STRIPE_SECRET_KEY=sk_test_...
```

**Important files:**

```bash
.env.local          # Local development (not committed)
.env.production     # Production environment
.env.development    # Development defaults
.env                # Shared defaults (can be committed)
```

### Using Environment Variables

**Server Components (all variables accessible):**

```tsx
// app/products/page.tsx (Server Component)

async function getProducts() {
  // ✅ Private key only accessible on server
  const res = await fetch(`${process.env.API_URL}/products`, {
    headers: {
      'Authorization': `Bearer ${process.env.API_SECRET_KEY}`
    }
  });
  return res.json();
}

export default async function ProductsPage() {
  const products = await getProducts();
  // ...
}
```

**Client Components (only NEXT_PUBLIC_* accessible):**

```tsx
// app/components/Analytics.tsx
'use client';

export default function Analytics() {
  // ✅ Public variable (starts with NEXT_PUBLIC_)
  const analyticsId = process.env.NEXT_PUBLIC_ANALYTICS_ID;

  // ❌ This is undefined in Client Components
  const secretKey = process.env.API_SECRET_KEY;  // undefined

  return <div>Analytics ID: {analyticsId}</div>;
}
```

### API Routes

```tsx
// app/api/products/route.tsx

export async function GET() {
  // ✅ Full access to all environment variables
  const res = await fetch(`${process.env.API_URL}/products`, {
    headers: {
      'Authorization': `Bearer ${process.env.API_SECRET_KEY}`
    }
  });

  const data = await res.json();
  return Response.json(data);
}
```

### Best Practices

```tsx
// ✅ Good: Validate environment variables on startup
// lib/env.ts

if (!process.env.DATABASE_URL) {
  throw new Error('DATABASE_URL is not defined');
}

if (!process.env.API_SECRET_KEY) {
  throw new Error('API_SECRET_KEY is not defined');
}

export const env = {
  databaseUrl: process.env.DATABASE_URL,
  apiSecretKey: process.env.API_SECRET_KEY,
  apiUrl: process.env.NEXT_PUBLIC_API_URL,
};
```

```tsx
// Use in your app
import { env } from '@/lib/env';

const res = await fetch(`${env.apiUrl}/data`, {
  headers: {
    'Authorization': `Bearer ${env.apiSecretKey}`
  }
});
```

### Security Rules

| Variable Type | Prefix | Accessible Where | Example |
|--------------|--------|------------------|---------|
| **Public** | `NEXT_PUBLIC_` | Server + Client | `NEXT_PUBLIC_API_URL` |
| **Private** | None | Server only | `DATABASE_URL` |

**⚠️ Never expose secrets to the client!**

```tsx
// ❌ DANGEROUS: Secret exposed to browser
const MyComponent = () => {
  return <div>{process.env.API_SECRET_KEY}</div>;
};

// ✅ SAFE: Use API route instead
const MyComponent = async () => {
  const data = await fetch('/api/secure-data');
  return <div>{data}</div>;
};
```

---

## Complete Example: Blog with Different Strategies

```
app/
├── (marketing)/
│   ├── page.tsx              # Homepage (SSG)
│   └── about/
│       └── page.tsx          # About (SSG)
│
├── blog/
│   ├── page.tsx              # Blog list (ISR)
│   └── [slug]/
│       └── page.tsx          # Blog post (ISR)
│
├── dashboard/
│   └── page.tsx              # Dashboard (SSR)
│
└── api/
    └── revalidate/
        └── route.ts         # On-demand revalidation
```

**Homepage (SSG):**

```tsx
// app/(marketing)/page.tsx
export default function HomePage() {
  return (
    <div>
      <h1>Welcome</h1>
      <p>This page is pre-rendered at build time</p>
    </div>
  );
}
```

**Blog List (ISR - revalidate hourly):**

```tsx
// app/blog/page.tsx

async function getPosts() {
  const res = await fetch('https://api.example.com/posts', {
    next: { revalidate: 3600, tags: ['posts'] }
  });
  return res.json();
}

export default async function BlogPage() {
  const posts = await getPosts();

  return (
    <div>
      <h1>Blog</h1>
      {posts.map(post => (
        <article key={post.id}>
          <h2>{post.title}</h2>
          <a href={`/blog/${post.slug}`}>Read more</a>
        </article>
      ))}
    </div>
  );
}
```

**Blog Post (ISR with generateStaticParams):**

```tsx
// app/blog/[slug]/page.tsx

export async function generateStaticParams() {
  const posts = await fetch('https://api.example.com/posts')
    .then(res => res.json());

  return posts.map(post => ({
    slug: post.slug,
  }));
}

export async function generateMetadata({ params }) {
  const post = await getPost(params.slug);
  
  return {
    title: post.title,
    description: post.excerpt,
  };
}

async function getPost(slug) {
  const res = await fetch(`https://api.example.com/posts/${slug}`, {
    next: { revalidate: 3600, tags: ['posts', `post-${slug}`] }
  });
  return res.json();
}

export default async function BlogPost({ params }) {
  const post = await getPost(params.slug);

  return (
    <article>
      <h1>{post.title}</h1>
      <div dangerouslySetInnerHTML={{ __html: post.content }} />
    </article>
  );
}
```

**Dashboard (SSR - always fresh):**

```tsx
// app/dashboard/page.tsx

export const dynamic = 'force-dynamic';  // Force SSR

async function getUserData() {
  const res = await fetch('https://api.example.com/user', {
    cache: 'no-store',
    headers: {
      'Authorization': `Bearer ${process.env.API_SECRET_KEY}`
    }
  });
  return res.json();
}

export default async function Dashboard() {
  const user = await getUserData();

  return (
    <div>
      <h1>Dashboard</h1>
      <p>Welcome, {user.name}</p>
      <p>Last login: {new Date().toLocaleString()}</p>
    </div>
  );
}
```

**Revalidation API:**

```tsx
// app/api/revalidate/route.tsx
import { revalidatePath, revalidateTag } from 'next/cache';

export async function POST(request) {
  const secret = request.headers.get('x-revalidate-secret');
  
  // Verify secret
  if (secret !== process.env.REVALIDATE_SECRET) {
    return Response.json({ error: 'Invalid secret' }, { status: 401 });
  }

  const { path, tag } = await request.json();

  try {
    if (path) {
      revalidatePath(path);
    }
    
    if (tag) {
      revalidateTag(tag);
    }

    return Response.json({ revalidated: true, now: Date.now() });
  } catch (err) {
    return Response.json({ error: err.message }, { status: 500 });
  }
}
```

---

## Summary

In this chapter, you learned:

✅ **Server Components** — Default, render on server, fetch data directly  
✅ **Client Components** — Add 'use client' for interactivity  
✅ **SSR** — Render on each request for personalized/real-time data  
✅ **SSG** — Pre-render at build time for static content  
✅ **ISR** — Update static pages without full rebuild  
✅ **Fetch caching** — Control data freshness with revalidation  
✅ **Environment variables** — Secure configuration management  

**Rendering Strategy Decision Tree:**

```
Is content personalized or real-time?
├─ Yes → SSR (dynamic rendering)
└─ No → Continue

Does content change frequently?
├─ Yes → ISR (with revalidation)
└─ No → SSG (static generation)

Does it need interactivity?
├─ Yes → Add Client Components
└─ No → Keep as Server Components
```

---

## Practice Exercises

1. Build a page that fetches user data on every request (SSR)

2. Create a blog with ISR (revalidate every 10 minutes)

3. Build a product catalog with SSG and generateStaticParams

4. Implement on-demand revalidation for blog posts

5. Create a dashboard with both Server and Client Components

---

**🎉 Amazing progress!** You now understand Next.js rendering strategies and data fetching patterns.