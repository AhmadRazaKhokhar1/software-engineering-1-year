
# Chapter 8: Styling, Optimization & Production Practices

## Introduction

You've built full-stack Next.js applications with authentication and data fetching. Now let's make them **production-ready** with proper styling, optimization, and best practices!

This chapter covers:

1. **Styling Options** — CSS Modules, Tailwind CSS, and more
2. **Fonts & Images** — Automatic optimization
3. **Metadata & SEO** — Search engine optimization
4. **Performance Optimization** — Making apps blazing fast
5. **Error Boundaries** — Graceful error handling
6. **Deployment Workflow** — Shipping to production

By the end, you'll know how to **build, optimize, and deploy production-ready Next.js applications**.

---

## 1. Styling Options

Next.js supports multiple styling approaches. Choose what fits your project.

### CSS Modules (Built-in)

CSS Modules scope styles to components automatically.

```css
/* app/components/Button.module.css */
.button {
  padding: 12px 24px;
  border: none;
  border-radius: 6px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
}

.primary {
  background: #2563eb;
  color: white;
}

.primary:hover {
  background: #1d4ed8;
}

.secondary {
  background: #6b7280;
  color: white;
}

.secondary:hover {
  background: #4b5563;
}

.large {
  padding: 16px 32px;
  font-size: 18px;
}
```

```tsx
// app/components/Button.tsx
import styles from './Button.module.css';

export default function Button({ children, variant = 'primary', size = 'medium'
}) {
  return (
    <button className={`${styles.button
        } ${styles[variant
            ]
        } ${styles[size
            ]
        }`
    }>
      {children
    }
    </button>
  );
}
```

**Benefits:**
- ✅ Scoped styles (no conflicts)
- ✅ No extra dependencies
- ✅ Standard CSS syntax
- ✅ Works with Sass/SCSS

### Tailwind CSS (Recommended)

**Install Tailwind:**

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

**Configure:**

```js
// tailwind.config.ts
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    './pages /**/*.{js,ts,jsx,tsx,mdx
        }',
    './components /**/*.{js,ts,jsx,tsx,mdx
        }',
    './app /**/*.{js,ts,jsx,tsx,mdx
        }',
    ],
  theme: {
    extend: {
      colors: {
        primary: {
                    50: '#eff6ff',
                    500: '#3b82f6',
                    900: '#1e3a8a',
                },
            },
        },
    },
  plugins: [],
}
```

```css
/* app/globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .btn {
    @apply px-6 py-3 rounded-lg font-semibold transition-all;
    }
  
  .btn-primary {
    @apply bg-blue-600 text-white hover:bg-blue-700;
    }
  
  .btn-secondary {
    @apply bg-gray-600 text-white hover:bg-gray-700;
    }
}
```

**Usage:**

```tsx
// app/components/Button.tsx
export default function Button({ children, variant = 'primary', className = ''
}) {
  const variants = {
    primary: 'bg-blue-600 hover:bg-blue-700 text-white',
    secondary: 'bg-gray-600 hover:bg-gray-700 text-white',
    outline: 'border-2 border-blue-600 text-blue-600 hover:bg-blue-50',
    };

  return (
    <button 
      className={`px-6 py-3 rounded-lg font-semibold transition-all ${variants[variant
            ]
        } ${className
        }`
    }
    >
      {children
    }
    </button>
  );
}
```

```tsx
// Usage
<Button variant="primary">Save</Button>
<Button variant="secondary" className="w-full">Cancel</Button>
```

### Global Styles

```css
/* app/globals.css */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: system-ui, -apple-system, sans-serif;
  line-height: 1.6;
  color: #1f2937;
}

a {
  color: #2563eb;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}
```

```tsx
// app/layout.tsx
import './globals.css';

export default function RootLayout({ children
}) {
  return (
    <html lang="en">
      <body>{children
    }</body>
    </html>
  );
}
```

### CSS-in-JS (Styled Components, Emotion)

**Install:**

```bash
npm install styled-components
```

**Configure for Next.js 13+:**

```tsx
// app/lib/registry.tsx
'use client';

import { useState
} from 'react';
import { useServerInsertedHTML
} from 'next/navigation';
import { ServerStyleSheet, StyleSheetManager
} from 'styled-components';

export default function StyledComponentsRegistry({ children
}) {
  const [styledComponentsStyleSheet
    ] = useState(() => new ServerStyleSheet());

  useServerInsertedHTML(() => {
    const styles = styledComponentsStyleSheet.getStyleElement();
    styledComponentsStyleSheet.instance.clearTag();
    return <>{styles
        }</>;
    });

  if (typeof window !== 'undefined') return <>{children
    }</>;

  return (
    <StyleSheetManager sheet={styledComponentsStyleSheet.instance
    }>
      {children
    }
    </StyleSheetManager>
  );
}
```

```tsx
// app/layout.tsx
import StyledComponentsRegistry from './lib/registry';

export default function RootLayout({ children
}) {
  return (
    <html>
      <body>
        <StyledComponentsRegistry>{children
    }</StyledComponentsRegistry>
      </body>
    </html>
  );
}
```

**Usage:**

```tsx
// app/components/Button.tsx
'use client';

import styled from 'styled-components';

const StyledButton = styled.button`
  padding: 12px 24px;
  background: ${props => props.variant === 'primary' ? '#2563eb' : '#6b7280'
};
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  
  &:hover {
    background: ${props => props.variant === 'primary' ? '#1d4ed8' : '#4b5563'
    };
}
`;

export default function Button({ children, variant = 'primary'
}) {
  return <StyledButton variant={variant
    }>{children
    }</StyledButton>;
}
```

---

## 2. Fonts & Images

Next.js automatically optimizes fonts and images.

### Next.js Font Optimization

```tsx
// app/layout.tsx
import { Inter, Roboto_Mono
} from 'next/font/google';

const inter = Inter({
  subsets: ['latin'
    ],
  display: 'swap',
  variable: '--font-inter',
});

const robotoMono = Roboto_Mono({
  subsets: ['latin'
    ],
  display: 'swap',
  variable: '--font-roboto-mono',
});

export default function RootLayout({ children
}) {
  return (
    <html lang="en" className={`${inter.variable
        } ${robotoMono.variable
        }`
    }>
      <body className={inter.className
    }>{children
    }</body>
    </html>
  );
}
```

```css
/* app/globals.css */
body {
  font-family: var(--font-inter), sans-serif;
}

code {
  font-family: var(--font-roboto-mono), monospace;
}
```

**Local Fonts:**

```tsx
// app/layout.tsx
import localFont from 'next/font/local';

const myFont = localFont({
  src: './fonts/MyFont.woff2',
  display: 'swap',
  variable: '--font-my-font',
});

export default function RootLayout({ children
}) {
  return (
    <html lang="en" className={myFont.variable
    }>
      <body className={myFont.className
    }>{children
    }</body>
    </html>
  );
}
```

### Next.js Image Optimization

```tsx
import Image from 'next/image';

export default function ProductCard({ product
}) {
  return (
    <div className="card">
      { /* Optimized image */}
      <Image
        src={product.image
    }
        alt={product.name
    }
        width={
        400
    }
        height={
        300
    }
        priority={
        false
    } // Set true for above-fold images
        placeholder="blur" // Shows blur while loading
        blurDataURL="data:image/jpeg;base64,..."
      />
      <h2>{product.name
    }</h2>
      <p>${product.price
    }</p>
    </div>
  );
}
```

**Features:**
- ✅ Automatic format conversion (WebP, AVIF)
- ✅ Responsive images (srcset)
- ✅ Lazy loading by default
- ✅ Blur placeholder
- ✅ Prevents layout shift

**Fill container:**

```tsx
<div className="relative w-full h-64">
  <Image
    src="/hero.jpg"
    alt="Hero"
    fill
    style={
    { objectFit: 'cover'
    }
}
    priority
  />
</div>
```

**External images (configure):**

```js
// next.config.mjs
module.exports = {
  images: {
    remotePatterns: [
            {
        protocol: 'https',
        hostname: 'images.unsplash.com',
            },
            {
        protocol: 'https',
        hostname: 'cdn.example.com',
            },
        ],
    },
};
```

---

## 3. Metadata & SEO

### Static Metadata

```tsx
// app/page.tsx
export const metadata = {
  title: 'Home | My Next.js App',
  description: 'Welcome to my awesome Next.js application',
  keywords: ['Next.js', 'React', 'Web Development'
    ],
  authors: [
        { name: 'Your Name'
        }
    ],
  openGraph: {
    title: 'My Next.js App',
    description: 'Welcome to my app',
    url: 'https: //myapp.com',
    siteName: 'My Next.js App',
    images: [
            {
        url: 'https: //myapp.com/og-image.jpg',
        width: 1200,
        height: 630,
        alt: 'My App',
            },
        ],
    locale: 'en_US',
    type: 'website',
    },
  twitter: {
    card: 'summary_large_image',
    title: 'My Next.js App',
    description: 'Welcome to my app',
    images: ['https: //myapp.com/twitter-image.jpg'],
    creator: '@yourusername',
        },
  robots: {
    index: true,
    follow: true,
    googleBot: {
      index: true,
      follow: true,
      'max-video-preview': -1,
      'max-image-preview': 'large',
      'max-snippet': -1,
            },
        },
    };

export default function HomePage() {
  return <h1>Home</h1>;
    }
```

### Dynamic Metadata

```tsx
// app/blog/[slug]/page.tsx
export async function generateMetadata({ params
    }) {
  const post = await getPost(params.slug);

  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      description: post.excerpt,
      images: [post.image
                ],
      publishedTime: post.publishedAt,
      authors: [post.author.name
                ],
            },
        };
    }

export default async function BlogPost({ params
    }) {
  const post = await getPost(params.slug);
  return <article>...</article>;
    }
```

### Metadata Template (Layout)

```tsx
// app/layout.tsx
export const metadata = {
  metadataBase: new URL('https: //myapp.com'),
  title: {
    template: '%s | My App',
    default: 'My App',
        },
  description: 'Default description',
    };
```

Now pages automatically append to the template:

```tsx
// app/about/page.tsx
export const metadata = {
  title: 'About Us', // Becomes "About Us | My App"
    };
```

### JSON-LD Structured Data

```tsx
// app/blog/[slug]/page.tsx
export default async function BlogPost({ params
    }) {
  const post = await getPost(params.slug);

  const jsonLd = {
    '@context': 'https: //schema.org',
    '@type': 'BlogPosting',
    headline: post.title,
    description: post.excerpt,
    image: post.image,
    datePublished: post.publishedAt,
    dateModified: post.updatedAt,
    author: {
      '@type': 'Person',
      name: post.author.name,
            },
        };

  return (
    <>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={
            { __html: JSON.stringify(jsonLd)
            }
        }
      />
      <article>
        <h1>{post.title
        }</h1>
        <div dangerouslySetInnerHTML={
            { __html: post.content
            }
        } />
      </article>
    </>
  );
    }
```

### Sitemap

```tsx
// app/sitemap.tsx
export default async function sitemap() {
  const posts = await getAllPosts();

  const postUrls = posts.map(post => ({
    url: `https: //myapp.com/blog/${post.slug}`,
    lastModified: post.updatedAt,
    changeFrequency: 'weekly',
    priority: 0.8,
        }));

  return [
            {
      url: 'https: //myapp.com',
      lastModified: new Date(),
      changeFrequency: 'yearly',
      priority: 1,
            },
            {
      url: 'https: //myapp.com/about',
      lastModified: new Date(),
      changeFrequency: 'monthly',
      priority: 0.8,
            },
    ...postUrls,
        ];
    }
```

### Robots.txt

```tsx
// app/robots.tsx
export default function robots() {
  return {
    rules: {
      userAgent: '*',
      allow: '/',
      disallow: ['/admin/', '/api/'
                ],
            },
    sitemap: 'https: //myapp.com/sitemap.xml',
        };
    }
```

---

## 4. Performance Optimization

### Code Splitting (Automatic)

Next.js automatically code-splits by route:

```
/about loads → Only /about code is downloaded
/blog loads → Only /blog code is downloaded
```

### Dynamic Imports

```tsx
// app/page.tsx
import dynamic from 'next/dynamic';

// Lazy load heavy component
const HeavyChart = dynamic(() => import('./components/HeavyChart'),
    {
  loading: () => <p>Loading chart...</p>,
  ssr: false, // Don't render on server (browser-only)
    });

export default function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <HeavyChart data={data
        } />
    </div>
  );
    }
```

### Lazy Loading Images

```tsx
import Image from 'next/image';

export default function Gallery({ images
    }) {
  return (
    <div>
      {images.map((img, i) => (
        <Image
          key={i
            }
          src={img.url
            }
          alt={img.alt
            }
          width={
                400
            }
          height={
                300
            }
          loading={i < 3 ? 'eager' : 'lazy'
            } // First 3 eager, rest lazy
          priority={i === 0
            } // First image gets priority
        />
      ))
        }
    </div>
  );
    }
```

### Bundle Analysis

```bash
npm install @next/bundle-analyzer
```

```js
// next.config.mjs
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
    });

module.exports = withBundleAnalyzer({
        // your config
    });
```

```bash
ANALYZE=true npm run build
```

### Streaming with Suspense

```tsx
// app/dashboard/page.tsx
import { Suspense
    } from 'react';

async function Stats() {
  const stats = await fetchStats(); // Slow
  return <div>Stats: {stats.total
        }</div>;
    }

async function RecentActivity() {
  const activity = await fetchActivity(); // Slow
  return <div>Activity: {activity.length
        }</div>;
    }

export default function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      
      { /* Stream components independently */}
      <Suspense fallback={<div>Loading stats...</div>
        }>
        <Stats />
      </Suspense>
      
      <Suspense fallback={<div>Loading activity...</div>
        }>
        <RecentActivity />
      </Suspense>
    </div>
  );
    }
```

**Benefits:**
- User sees page immediately
- Data loads progressively
- Faster perceived performance

### Loading States

```tsx
// app/dashboard/loading.tsx
export default function Loading() {
  return (
    <div className="loading">
      <div className="spinner" />
      <p>Loading dashboard...</p>
    </div>
  );
    }
```

Automatically shown while `dashboard/page.tsx` loads.

---

## 5. Error Boundaries

### Error Component

```tsx
// app/error.tsx
'use client';

import { useEffect
    } from 'react';

export default function Error({ error, reset
    }) {
  useEffect(() => {
    console.error(error);
        },
        [error
        ]);

  return (
    <div className="error-container">
      <h2>Something went wrong!</h2>
      <p>{error.message
        }</p>
      <button onClick={() => reset()
        }>Try again</button>
    </div>
  );
    }
```

### Global Error (Root)

```tsx
// app/global-error.tsx
'use client';

export default function GlobalError({ error, reset
    }) {
  return (
    <html>
      <body>
        <h2>Application Error</h2>
        <p>{error.message
        }</p>
        <button onClick={() => reset()
        }>Try again</button>
      </body>
    </html>
  );
    }
```

### Not Found

```tsx
// app/not-found.tsx
import Link from 'next/link';

export default function NotFound() {
  return (
    <div className="not-found">
      <h1>404</h1>
      <h2>Page Not Found</h2>
      <p>The page you're looking for doesn't exist.</p>
      <Link href="/">Go Home</Link>
    </div>
  );
    }
```

```tsx
// app/blog/[slug]/page.tsx
import { notFound
    } from 'next/navigation';

export default async function BlogPost({ params
    }) {
  const post = await getPost(params.slug);

  if (!post) {
    notFound(); // Shows not-found.tsx
        }

  return <article>...</article>;
    }
```

---

## 6. Deployment Workflow

### Build for Production

```bash
npm run build
```

Output:
```
Route (app)                              Size     First Load JS
┌ ○ /                                    1.2 kB         85 kB
├ ○ /about                               890 B          84 kB
├ ƒ /blog/[slug
    ]                         1.5 kB         86 kB
└ ○ /contact                             1.1 kB         85 kB

○  (Static)  prerendered as static content
ƒ  (Dynamic) server-rendered on demand
```

### Environment Variables

```bash
# .env.local (development)
DATABASE_URL=postgresql: //localhost/dev_db
NEXT_PUBLIC_API_URL=http: //localhost:3000

# .env.production (production)
DATABASE_URL=postgresql: //prod-server/prod_db
NEXT_PUBLIC_API_URL=https: //myapp.com
```

### Vercel Deployment (Recommended)

```bash
# Install Vercel CLI
npm install -g vercel

# Deploy
vercel

# Production deployment
vercel --prod
```

**Or connect GitHub:**
1. Push code to GitHub
2. Import project in Vercel dashboard
3. Automatic deployments on push

### Docker Deployment

```dockerfile
# Dockerfile
FROM node: 18-alpine AS base

# Install dependencies only when needed
FROM base AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

# Rebuild the source code only when needed
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Production image
FROM base AS runner
WORKDIR /app

ENV NODE_ENV production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

CMD [
        "node",
        "server.ts"
    ]
```

```js
// next.config.mjs
module.exports = {
  output: 'standalone',
    };
```

```bash
docker build -t my-nextjs-app .
docker run -p 3000: 3000 my-nextjs-app
```

### Performance Checklist

Before deploying:

✅ **Images optimized** (use `next/image`)  
✅ **Fonts optimized** (use `next/font`)  
✅ **Code split** (dynamic imports for heavy components)  
✅ **Environment variables** set correctly  
✅ **Error boundaries** in place  
✅ **Loading states** for async content  
✅ **Metadata** for SEO  
✅ **Analytics** configured (Google Analytics, Vercel Analytics)  
✅ **Lighthouse score** > 90  

---

## Summary

In this chapter, you learned:

✅ **Styling** — CSS Modules, Tailwind CSS, CSS-in-JS  
✅ **Fonts** — Automatic optimization with next/font  
✅ **Images** — Automatic optimization with next/image  
✅ **SEO** — Metadata, structured data, sitemaps  
✅ **Performance** — Code splitting, lazy loading, Suspense  
✅ **Error Handling** — Error boundaries and not-found pages  
✅ **Deployment** — Building and shipping to production  

Your Next.js apps are now **production-ready**!

---

## Practice Exercises

1. Style a complete page using Tailwind CSS

2. Optimize all images in a gallery using next/image

3. Add comprehensive metadata to a blog

4. Implement error boundaries for all routes

5. Deploy a Next.js app to Vercel

---

**🎉 Congratulations!** You've completed 8 chapters! You're now ready to build production-grade Next.js applications!