# Chapter 7: Navigation, Forms & Backend Features

## Introduction

You've mastered rendering strategies and data fetching. Now let's make Next.js **full-stack** by adding navigation, forms, API routes, and backend features!

This chapter covers:

1. **Link & Navigation** — Client-side routing
2. **Dynamic Routes** — Advanced patterns
3. **Route Handlers (API Routes)** — Backend endpoints
4. **Forms & Server Actions** — Modern form handling
5. **Authentication Concepts** — User login/logout flow
6. **Middleware Basics** — Request interception

By the end, you'll understand **Next.js as a full-stack framework** and be able to build complete applications.

---

## 1. Link & Navigation

Next.js provides the `<Link>` component for **client-side navigation** (no full page reload).

### Basic Link Component

```tsx
// app/page.tsx
import Link from 'next/link';

export default function HomePage() {
  return (
    <div>
      <h1>Home</h1>
      
      {/* ✅ Use Link for internal navigation */}
      <Link href="/about">About Us</Link>
      <Link href="/blog">Blog</Link>
      <Link href="/contact">Contact</Link>
      
      {/* ❌ Don't use <a> for internal links */}
      <a href="/about">About (causes full reload)</a>
    </div>
  );
}
```

**Why use Link?**
- ✅ Client-side navigation (no full page reload)
- ✅ Prefetches linked pages in viewport (instant navigation)
- ✅ Preserves scroll position
- ✅ Faster transitions

### Link with Dynamic Routes

```tsx
import Link from 'next/link';

export default function BlogList({ posts }) {
  return (
    <div>
      <h1>Blog Posts</h1>
      {posts.map(post => (
        <article key={post.id}>
          <h2>
            {/* Dynamic route */}
            <Link href={`/blog/${post.slug}`}>
              {post.title}
            </Link>
          </h2>
          <p>{post.excerpt}</p>
        </article>
      ))}
    </div>
  );
}
```

### Link with Query Parameters

```tsx
import Link from 'next/link';

export default function ProductFilters() {
  return (
    <div>
      <h3>Filter by Category</h3>
      
      {/* Query parameters */}
      <Link href="/products?category=electronics">
        Electronics
      </Link>
      <Link href="/products?category=clothing">
        Clothing
      </Link>
      
      {/* Multiple query params */}
      <Link href="/products?category=electronics&sort=price">
        Electronics (by price)
      </Link>
      
      {/* Object syntax */}
      <Link href={{
        pathname: '/products',
        query: { category: 'electronics', sort: 'price' }
      }}>
        Electronics (by price)
      </Link>
    </div>
  );
}
```

### Active Link Styling

```tsx
'use client';

import Link from 'next/link';
import { usePathname } from 'next/navigation';

export default function Navigation() {
  const pathname = usePathname();

  const links = [
    { href: '/', label: 'Home' },
    { href: '/about', label: 'About' },
    { href: '/blog', label: 'Blog' },
    { href: '/contact', label: 'Contact' },
  ];

  return (
    <nav>
      <ul>
        {links.map(link => (
          <li key={link.href}>
            <Link 
              href={link.href}
              className={pathname === link.href ? 'active' : ''}
            >
              {link.label}
            </Link>
          </li>
        ))}
      </ul>
    </nav>
  );
}
```

**CSS:**

```css
nav a {
  color: #666;
  text-decoration: none;
  padding: 8px 16px;
}

nav a.active {
  color: #2563eb;
  font-weight: bold;
  border-bottom: 2px solid #2563eb;
}
```

### Programmatic Navigation

```tsx
'use client';

import { useRouter } from 'next/navigation';

export default function LoginForm() {
  const router = useRouter();

  const handleSubmit = async (e) => {
    e.preventDefault();
    
    // Login logic...
    const success = await login();
    
    if (success) {
      // Navigate programmatically
      router.push('/dashboard');
      
      // Or replace (doesn't add to history)
      // router.replace('/dashboard');
      
      // Or go back
      // router.back();
      
      // Or refresh current route
      // router.refresh();
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="email" name="email" />
      <input type="password" name="password" />
      <button type="submit">Login</button>
    </form>
  );
}
```

### Prefetching

```tsx
import Link from 'next/link';

export default function HomePage() {
  return (
    <div>
      {/* ✅ Default: Prefetch in viewport */}
      <Link href="/about">About (prefetched)</Link>
      
      {/* ❌ Disable prefetch */}
      <Link href="/contact" prefetch={false}>
        Contact (not prefetched)
      </Link>
    </div>
  );
}
```

### External Links

```tsx
import Link from 'next/link';

export default function Footer() {
  return (
    <footer>
      {/* External links - use target and rel */}
      <Link 
        href="https://github.com" 
        target="_blank" 
        rel="noopener noreferrer"
      >
        GitHub
      </Link>
      
      {/* Or use regular <a> for external */}
      <a 
        href="https://twitter.com" 
        target="_blank" 
        rel="noopener noreferrer"
      >
        Twitter
      </a>
    </footer>
  );
}
```

---

## 2. Dynamic Routes (Advanced)

### Reading URL Parameters

```tsx
// app/blog/[slug]/page.tsx

export default function BlogPost({ params }) {
  // params.slug is available
  return (
    <article>
      <h1>Post: {params.slug}</h1>
    </article>
  );
}
```

### Reading Search Params

```tsx
// app/products/page.tsx

export default function ProductsPage({ searchParams }) {
  // searchParams contains query parameters
  const category = searchParams.category;
  const sort = searchParams.sort;
  const page = searchParams.page || 1;

  return (
    <div>
      <h1>Products</h1>
      <p>Category: {category}</p>
      <p>Sort: {sort}</p>
      <p>Page: {page}</p>
    </div>
  );
}

// URL: /products?category=electronics&sort=price&page=2
// searchParams: { category: 'electronics', sort: 'price', page: '2' }
```

### Client-Side Search Params

```tsx
'use client';

import { useSearchParams } from 'next/navigation';

export default function SearchBox() {
  const searchParams = useSearchParams();
  
  const query = searchParams.get('q');
  const category = searchParams.get('category');

  return (
    <div>
      <p>Search: {query}</p>
      <p>Category: {category}</p>
    </div>
  );
}
```

### Updating Search Params (Client)

```tsx
'use client';

import { useRouter, useSearchParams } from 'next/navigation';

export default function ProductFilters() {
  const router = useRouter();
  const searchParams = useSearchParams();

  const updateFilter = (key, value) => {
    const params = new URLSearchParams(searchParams);
    params.set(key, value);
    router.push(`/products?${params.toString()}`);
  };

  return (
    <div>
      <button onClick={() => updateFilter('category', 'electronics')}>
        Electronics
      </button>
      <button onClick={() => updateFilter('category', 'clothing')}>
        Clothing
      </button>
      <button onClick={() => updateFilter('sort', 'price')}>
        Sort by Price
      </button>
    </div>
  );
}
```

### Route Groups (Organization)

```
app/
├── (marketing)/
│   ├── layout.tsx        # Layout for marketing pages
│   ├── page.tsx          # / (home)
│   ├── about/
│   │   └── page.tsx      # /about
│   └── pricing/
│       └── page.tsx      # /pricing
│
└── (shop)/
    ├── layout.tsx        # Layout for shop pages
    ├── products/
    │   └── page.tsx      # /products
    └── cart/
        └── page.tsx      # /cart
```

**Route groups** (folders in parentheses) **don't affect URLs** — they're just for organization.

### Parallel Routes (Advanced)

```
app/
└── dashboard/
    ├── layout.tsx
    ├── page.tsx
    ├── @team/
    │   └── page.tsx
    └── @analytics/
        └── page.tsx
```

```tsx
// app/dashboard/layout.tsx
export default function DashboardLayout({ children, team, analytics }) {
  return (
    <div>
      <div>{children}</div>
      <div className="sidebar">
        <div>{team}</div>
        <div>{analytics}</div>
      </div>
    </div>
  );
}
```

### Intercepting Routes (Modals)

```
app/
├── photos/
│   ├── page.tsx           # /photos
│   ├── [id]/
│   │   └── page.tsx       # /photos/123
│   └── (..)photos/
│       └── [id]/
│           └── page.tsx   # Intercept /photos/123 (modal)
```

---

## 3. Route Handlers (API Routes)

**Route Handlers** are Next.js's built-in API routes — backend endpoints in your app.

### Basic GET Handler

```tsx
// app/api/hello/route.tsx

export async function GET() {
  return Response.json({ message: 'Hello from API!' });
}
```

**Access:** `GET /api/hello`

**Response:**
```json
{ "message": "Hello from API!" }
```

### POST Handler

```tsx
// app/api/users/route.tsx

export async function POST(request) {
  const body = await request.json();
  
  // Validate
  if (!body.name || !body.email) {
    return Response.json(
      { error: 'Name and email are required' },
      { status: 400 }
    );
  }

  // Save to database (example)
  const user = await db.user.create({
    data: {
      name: body.name,
      email: body.email,
    }
  });

  return Response.json(user, { status: 201 });
}
```

**Usage:**
```typescript
const response = await fetch('/api/users', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    name: 'Alice',
    email: 'alice@example.com'
  })
});

const user = await response.json();
```

### All HTTP Methods

```tsx
// app/api/posts/route.tsx

export async function GET() {
  const posts = await db.post.findMany();
  return Response.json(posts);
}

export async function POST(request) {
  const body = await request.json();
  const post = await db.post.create({ data: body });
  return Response.json(post, { status: 201 });
}

export async function PUT(request) {
  const body = await request.json();
  const post = await db.post.update({
    where: { id: body.id },
    data: body
  });
  return Response.json(post);
}

export async function DELETE(request) {
  const { searchParams } = new URL(request.url);
  const id = searchParams.get('id');
  
  await db.post.delete({ where: { id } });
  return Response.json({ success: true });
}
```

### Dynamic Route Handlers

```tsx
// app/api/posts/[id]/route.tsx

export async function GET(request, { params }) {
  const post = await db.post.findUnique({
    where: { id: params.id }
  });

  if (!post) {
    return Response.json(
      { error: 'Post not found' },
      { status: 404 }
    );
  }

  return Response.json(post);
}

export async function PATCH(request, { params }) {
  const body = await request.json();
  
  const post = await db.post.update({
    where: { id: params.id },
    data: body
  });

  return Response.json(post);
}

export async function DELETE(request, { params }) {
  await db.post.delete({
    where: { id: params.id }
  });

  return Response.json({ success: true });
}
```

**Usage:**
```typescript
// GET /api/posts/123
const post = await fetch('/api/posts/123').then(r => r.json());

// PATCH /api/posts/123
await fetch('/api/posts/123', {
  method: 'PATCH',
  body: JSON.stringify({ title: 'Updated Title' })
});

// DELETE /api/posts/123
await fetch('/api/posts/123', { method: 'DELETE' });
```

### Headers and Cookies

```tsx
// app/api/profile/route.tsx
import { cookies, headers } from 'next/headers';

export async function GET() {
  // Read headers
  const headersList = headers();
  const authorization = headersList.get('authorization');

  // Read cookies
  const cookieStore = cookies();
  const token = cookieStore.get('token');

  // Set cookies
  cookieStore.set('visited', 'true', {
    httpOnly: true,
    secure: process.env.NODE_ENV === 'production',
    maxAge: 60 * 60 * 24 * 7 // 1 week
  });

  return Response.json({ 
    authorized: !!authorization,
    hasToken: !!token
  });
}
```

### Error Handling

```tsx
// app/api/users/[id]/route.tsx

export async function GET(request, { params }) {
  try {
    const user = await db.user.findUnique({
      where: { id: params.id }
    });

    if (!user) {
      return Response.json(
        { error: 'User not found' },
        { status: 404 }
      );
    }

    return Response.json(user);
  } catch (error) {
    console.error('Error fetching user:', error);
    
    return Response.json(
      { error: 'Internal server error' },
      { status: 500 }
    );
  }
}
```

### CORS Headers

```tsx
// app/api/public/route.tsx

export async function GET() {
  return Response.json(
    { message: 'Public API' },
    {
      headers: {
        'Access-Control-Allow-Origin': '*',
        'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE',
        'Access-Control-Allow-Headers': 'Content-Type, Authorization',
      }
    }
  );
}

export async function OPTIONS() {
  return new Response(null, {
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE',
      'Access-Control-Allow-Headers': 'Content-Type, Authorization',
    }
  });
}
```

---

## 4. Forms & Server Actions

**Server Actions** are Next.js's modern way to handle forms — no API routes needed!

### Basic Server Action

```tsx
// app/actions.tsx
'use server';

export async function createPost(formData) {
  const title = formData.get('title');
  const content = formData.get('content');

  // Save to database
  const post = await db.post.create({
    data: { title, content }
  });

  return { success: true, post };
}
```

```tsx
// app/new-post/page.tsx
import { createPost } from '../actions';

export default function NewPost() {
  return (
    <form action={createPost}>
      <input type="text" name="title" placeholder="Title" required />
      <textarea name="content" placeholder="Content" required />
      <button type="submit">Create Post</button>
    </form>
  );
}
```

**What happens:**
1. User submits form
2. Form data sent to server
3. `createPost` runs on server
4. Database updated
5. Page revalidates automatically

### Server Action with Validation

```tsx
// app/actions.tsx
'use server';

import { revalidatePath } from 'next/cache';
import { z } from 'zod';

const PostSchema = z.object({
  title: z.string().min(3).max(100),
  content: z.string().min(10),
});

export async function createPost(formData) {
  // Validate
  const data = {
    title: formData.get('title'),
    content: formData.get('content'),
  };

  const result = PostSchema.safeParse(data);

  if (!result.success) {
    return {
      success: false,
      errors: result.error.flatten().fieldErrors
    };
  }

  // Save to database
  const post = await db.post.create({
    data: result.data
  });

  // Revalidate blog page
  revalidatePath('/blog');

  return { success: true, post };
}
```

### Client Component with Server Action

```tsx
// app/components/CreatePostForm.tsx
'use client';

import { useState } from 'react';
import { createPost } from '../actions';

export default function CreatePostForm() {
  const [errors, setErrors] = useState({});
  const [loading, setLoading] = useState(false);

  const handleSubmit = async (formData) => {
    setLoading(true);
    setErrors({});

    const result = await createPost(formData);

    if (!result.success) {
      setErrors(result.errors);
    } else {
      // Reset form or redirect
      alert('Post created!');
    }

    setLoading(false);
  };

  return (
    <form action={handleSubmit}>
      <div>
        <input type="text" name="title" placeholder="Title" />
        {errors.title && <p className="error">{errors.title[0]}</p>}
      </div>

      <div>
        <textarea name="content" placeholder="Content" />
        {errors.content && <p className="error">{errors.content[0]}</p>}
      </div>

      <button type="submit" disabled={loading}>
        {loading ? 'Creating...' : 'Create Post'}
      </button>
    </form>
  );
}
```

### Server Action with Redirect

```tsx
// app/actions.tsx
'use server';

import { redirect } from 'next/navigation';

export async function createPost(formData) {
  const title = formData.get('title');
  const content = formData.get('content');

  const post = await db.post.create({
    data: { title, content }
  });

  // Redirect to new post
  redirect(`/blog/${post.slug}`);
}
```

### Server Action with useFormStatus

```tsx
// app/components/SubmitButton.tsx
'use client';

import { useFormStatus } from 'react-dom';

export function SubmitButton() {
  const { pending } = useFormStatus();

  return (
    <button type="submit" disabled={pending}>
      {pending ? 'Submitting...' : 'Submit'}
    </button>
  );
}
```

```tsx
// app/new-post/page.tsx
import { createPost } from '../actions';
import { SubmitButton } from '../components/SubmitButton';

export default function NewPost() {
  return (
    <form action={createPost}>
      <input type="text" name="title" />
      <textarea name="content" />
      <SubmitButton />
    </form>
  );
}
```

### Progressive Enhancement

```tsx
// app/new-post/page.tsx
import { createPost } from '../actions';

export default function NewPost() {
  return (
    <form action={createPost}>
      <input type="text" name="title" required />
      <textarea name="content" required />
      
      {/* Works even if JavaScript is disabled! */}
      <button type="submit">Create Post</button>
    </form>
  );
}
```

---

## 5. Authentication Concepts

### Session-Based Auth Flow

```tsx
// app/actions/auth.tsx
'use server';

import { cookies } from 'next/headers';
import { redirect } from 'next/navigation';
import bcrypt from 'bcrypt';

export async function login(formData) {
  const email = formData.get('email');
  const password = formData.get('password');

  // Find user
  const user = await db.user.findUnique({
    where: { email }
  });

  if (!user) {
    return { error: 'Invalid credentials' };
  }

  // Verify password
  const valid = await bcrypt.compare(password, user.passwordHash);

  if (!valid) {
    return { error: 'Invalid credentials' };
  }

  // Create session
  const session = await createSession(user.id);

  // Set cookie
  cookies().set('session', session.token, {
    httpOnly: true,
    secure: process.env.NODE_ENV === 'production',
    sameSite: 'lax',
    maxAge: 60 * 60 * 24 * 7 // 1 week
  });

  redirect('/dashboard');
}

export async function logout() {
  cookies().delete('session');
  redirect('/login');
}
```

### Protected Routes (Middleware)

```tsx
// middleware.ts
import { NextResponse } from 'next/server';

export function middleware(request) {
  const session = request.cookies.get('session');

  // Redirect to login if not authenticated
  if (!session && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  // Redirect to dashboard if already logged in
  if (session && request.nextUrl.pathname === '/login') {
    return NextResponse.redirect(new URL('/dashboard', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*', '/login']
};
```

### Get Current User (Server Component)

```tsx
// app/lib/auth.tsx
import { cookies } from 'next/headers';

export async function getCurrentUser() {
  const sessionToken = cookies().get('session')?.value;

  if (!sessionToken) {
    return null;
  }

  const session = await db.session.findUnique({
    where: { token: sessionToken },
    include: { user: true }
  });

  if (!session || session.expiresAt < new Date()) {
    return null;
  }

  return session.user;
}
```

```tsx
// app/dashboard/page.tsx
import { getCurrentUser } from '../lib/auth';
import { redirect } from 'next/navigation';

export default async function Dashboard() {
  const user = await getCurrentUser();

  if (!user) {
    redirect('/login');
  }

  return (
    <div>
      <h1>Welcome, {user.name}!</h1>
      <p>Email: {user.email}</p>
    </div>
  );
}
```

### Login Form

```tsx
// app/login/page.tsx
import { login } from '../actions/auth';

export default function LoginPage() {
  return (
    <div>
      <h1>Login</h1>
      <form action={login}>
        <input 
          type="email" 
          name="email" 
          placeholder="Email" 
          required 
        />
        <input 
          type="password" 
          name="password" 
          placeholder="Password" 
          required 
        />
        <button type="submit">Login</button>
      </form>
    </div>
  );
}
```

### Using NextAuth (Recommended)

```bash
npm install next-auth
```

```tsx
// app/api/auth/[...nextauth]/route.tsx
import NextAuth from 'next-auth';
import CredentialsProvider from 'next-auth/providers/credentials';
import GoogleProvider from 'next-auth/providers/google';

const handler = NextAuth({
  providers: [
    CredentialsProvider({
      name: 'Credentials',
      credentials: {
        email: { label: "Email", type: "email" },
        password: { label: "Password", type: "password" }
      },
      async authorize(credentials) {
        const user = await verifyUser(credentials);
        return user || null;
      }
    }),
    GoogleProvider({
      clientId: process.env.GOOGLE_CLIENT_ID,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET,
    }),
  ],
  session: {
    strategy: 'jwt',
  },
  pages: {
    signIn: '/login',
  },
});

export { handler as GET, handler as POST };
```

---

## 6. Middleware Basics

**Middleware** runs **before a request is completed** — perfect for auth, redirects, headers, etc.

### Basic Middleware

```tsx
// middleware.ts (root level)
import { NextResponse } from 'next/server';

export function middleware(request) {
  console.log('Request URL:', request.nextUrl.pathname);
  
  // Continue to the page
  return NextResponse.next();
}
```

### Redirects

```tsx
// middleware.ts
import { NextResponse } from 'next/server';

export function middleware(request) {
  // Redirect old URLs
  if (request.nextUrl.pathname === '/old-blog') {
    return NextResponse.redirect(new URL('/blog', request.url));
  }

  return NextResponse.next();
}
```

### Adding Headers

```tsx
// middleware.ts
import { NextResponse } from 'next/server';

export function middleware(request) {
  const response = NextResponse.next();

  // Add custom header
  response.headers.set('x-custom-header', 'my-value');

  return response;
}
```

### Authentication Check

```tsx
// middleware.ts
import { NextResponse } from 'next/server';

export function middleware(request) {
  const session = request.cookies.get('session');
  const isAuthPage = request.nextUrl.pathname.startsWith('/dashboard');

  if (isAuthPage && !session) {
    // Redirect to login
    return NextResponse.redirect(new URL('/login', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: '/dashboard/:path*'
};
```

### Matcher Configuration

```tsx
// middleware.ts
export function middleware(request) {
  // Your middleware logic
}

// Match specific paths
export const config = {
  matcher: [
    '/dashboard/:path*',    // All dashboard routes
    '/admin/:path*',        // All admin routes
    '/api/:path*',          // All API routes
  ]
};

// Or use regex
export const config = {
  matcher: [
    '/((?!api|_next/static|_next/image|favicon.ico).*)',
  ]
};
```

### Geolocation

```tsx
// middleware.ts
import { NextResponse } from 'next/server';

export function middleware(request) {
  const country = request.geo?.country || 'US';
  const city = request.geo?.city || 'Unknown';

  const response = NextResponse.next();
  response.headers.set('x-geo-country', country);
  response.headers.set('x-geo-city', city);

  return response;
}
```

---

## Complete Example: Blog with Auth

```
app/
├── (auth)/
│   ├── login/
│   │   └── page.tsx
│   └── register/
│       └── page.tsx
│
├── (protected)/
│   ├── dashboard/
│   │   └── page.tsx
│   └── new-post/
│       └── page.tsx
│
├── blog/
│   ├── page.tsx
│   └── [slug]/
│       └── page.tsx
│
├── api/
│   ├── auth/
│   │   └── [...nextauth]/
│   │       └── route.ts
│   └── posts/
│       └── route.ts
│
├── actions/
│   ├── auth.js
│   └── posts.js
│
├── lib/
│   ├── auth.js
│   └── db.js
│
└── middleware.ts
```

**Auth actions:**

```tsx
// app/actions/auth.tsx
'use server';

import { cookies } from 'next/headers';
import { redirect } from 'next/navigation';
import bcrypt from 'bcrypt';
import { db } from '../lib/db';

export async function login(formData) {
  const email = formData.get('email');
  const password = formData.get('password');

  const user = await db.user.findUnique({ where: { email } });

  if (!user || !(await bcrypt.compare(password, user.password))) {
    return { error: 'Invalid credentials' };
  }

  const session = await db.session.create({
    data: {
      userId: user.id,
      expiresAt: new Date(Date.now() + 7 * 24 * 60 * 60 * 1000)
    }
  });

  cookies().set('session', session.id, {
    httpOnly: true,
    secure: true,
    sameSite: 'lax',
    maxAge: 7 * 24 * 60 * 60
  });

  redirect('/dashboard');
}

export async function logout() {
  const sessionId = cookies().get('session')?.value;
  
  if (sessionId) {
    await db.session.delete({ where: { id: sessionId } });
  }

  cookies().delete('session');
  redirect('/');
}
```

**Post actions:**

```tsx
// app/actions/posts.tsx
'use server';

import { revalidatePath } from 'next/cache';
import { redirect } from 'next/navigation';
import { getCurrentUser } from '../lib/auth';
import { db } from '../lib/db';

export async function createPost(formData) {
  const user = await getCurrentUser();

  if (!user) {
    throw new Error('Unauthorized');
  }

  const title = formData.get('title');
  const content = formData.get('content');

  const post = await db.post.create({
    data: {
      title,
      content,
      authorId: user.id,
      slug: title.toLowerCase().replace(/\s+/g, '-')
    }
  });

  revalidatePath('/blog');
  redirect(`/blog/${post.slug}`);
}
```

**Middleware:**

```tsx
// middleware.ts
import { NextResponse } from 'next/server';

export function middleware(request) {
  const session = request.cookies.get('session');
  const isProtected = request.nextUrl.pathname.startsWith('/dashboard') ||
                      request.nextUrl.pathname.startsWith('/new-post');

  if (isProtected && !session) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  if (session && request.nextUrl.pathname === '/login') {
    return NextResponse.redirect(new URL('/dashboard', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*', '/new-post', '/login']
};
```

---

## Summary

In this chapter, you learned:

✅ **Link & Navigation** — Client-side routing with prefetching  
✅ **Dynamic Routes** — URL parameters and search params  
✅ **Route Handlers** — Backend API endpoints  
✅ **Server Actions** — Modern form handling without API routes  
✅ **Authentication** — Login/logout flow with sessions  
✅ **Middleware** — Request interception for auth, redirects, headers  

You now understand **Next.js as a full-stack framework**!

---

## Practice Exercises

1. Create a navigation menu with active link highlighting

2. Build a product filter system using search params

3. Create API routes for CRUD operations on a resource

4. Build a contact form using Server Actions with validation

5. Implement authentication with login/logout and protected routes

---

**🎉 Fantastic work!** You now understand Next.js navigation, forms, API routes, and authentication!