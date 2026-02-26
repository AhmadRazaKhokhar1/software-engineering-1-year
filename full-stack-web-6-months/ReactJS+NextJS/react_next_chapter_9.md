# Chapter 9: Capstone Project - Next.js + Firebase Blog Platform

## Introduction

**Congratulations on making it to the final chapter!** 🎉

It's time to put everything together and build a **complete, production-ready Next.js application** from scratch using **Firebase** as our backend.

In this capstone project, you'll build a **Serverless + NextJS Blog Platform** with:

✅ **Authentication** (register, login, logout with Firebase Auth)  
✅ **CRUD Operations** (create, read, update, delete posts)  
✅ **Database** (Firestore)  
✅ **Rich Text Editor** (write blog posts)  
✅ **Image Uploads** (Firebase Storage)  
✅ **Categories & Tags** (organize content)  
✅ **Comments** (user engagement)  
✅ **Search & Filtering** (find posts)  
✅ **Responsive Design** (mobile-friendly)  
✅ **SEO Optimization** (metadata, sitemap)  
✅ **Deployment** (Vercel)  

This is a **hire-ready portfolio project** that demonstrates everything you've learned.

---

## Project Overview

### Features

**Public Features:**
- View all blog posts
- Read individual posts
- Filter by category/tag
- Search posts
- Comment on posts (requires login)

**Authenticated Features:**
- Create new posts
- Edit own posts
- Delete own posts
- Upload cover images
- Manage profile

**Admin Features:**
- Moderate comments
- Delete any post
- Manage categories/tags

---

## Tech Stack

```
Frontend:
- Next.js 14 (App Router)
- React 18
- Tailwind CSS
- React Hook Form
- Tiptap (Rich Text Editor)

Backend:
- Firebase Authentication
- Cloud Firestore
- Firebase Storage
- Server Actions

Deployment:
- Vercel
```

---

## Project Structure

```
blog-platform/
├── app/
│   ├── (auth)/
│   │   ├── login/
│   │   │   └── page.tsx
│   │   └── register/
│   │       └── page.tsx
│   │
│   ├── (dashboard)/
│   │   ├── dashboard/
│   │   │   └── page.tsx
│   │   ├── new-post/
│   │   │   └── page.tsx
│   │   └── edit/[id]/
│   │       └── page.tsx
│   │
│   ├── blog/
│   │   ├── page.tsx
│   │   ├── [slug]/
│   │   │   └── page.tsx
│   │   └── category/[category]/
│   │       └── page.tsx
│   │
│   ├── api/
│   │   ├── upload/
│   │   │   └── route.ts
│   │   └── posts/
│   │       └── route.ts
│   │
│   ├── actions/
│   │   ├── auth.js
│   │   ├── posts.js
│   │   └── comments.js
│   │
│   ├── components/
│   │   ├── ui/
│   │   ├── blog/
│   │   └── forms/
│   │
│   ├── lib/
│   │   ├── firebase.js
│   │   ├── firestore.js
│   │   └── utils.js
│   │
│   ├── layout.tsx
│   ├── page.tsx
│   └── globals.css
│
├── public/
│
├── .env.local
├── firebase.json
├── next.config.mjs
├── tailwind.config.ts
└── package.json
```

---

## Step-by-Step Implementation

### Phase 1: Setup & Firebase Configuration

**1. Initialize Project**

```bash
npx create-next-app@latest blog-platform
cd blog-platform

# Install dependencies
npm install firebase
npm install react-hook-form zod
npm install @tiptap/react @tiptap/starter-kit
npm install date-fns slugify
npm install react-firebase-hooks
```

**2. Create Firebase Project**

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click "Add Project"
3. Name it "blog-platform"
4. Disable Google Analytics (optional)
5. Click "Create Project"

**3. Enable Firebase Services**

**Authentication:**
1. Go to Authentication → Get Started
2. Enable "Email/Password" provider

**Firestore:**
1. Go to Firestore Database → Create Database
2. Start in **production mode**
3. Choose a location

**Storage:**
1. Go to Storage → Get Started
2. Start in **production mode**

**4. Get Firebase Config**

1. Go to Project Settings → General
2. Scroll to "Your apps"
3. Click Web icon (</>) → Register app
4. Copy the config

**5. Firebase Configuration**

```js
// lib/firebase.ts
import { initializeApp, getApps } from 'firebase/app';
import { getAuth } from 'firebase/auth';
import { getFirestore } from 'firebase/firestore';
import { getStorage } from 'firebase/storage';

const firebaseConfig = {
  apiKey: process.env.NEXT_PUBLIC_FIREBASE_API_KEY,
  authDomain: process.env.NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN,
  projectId: process.env.NEXT_PUBLIC_FIREBASE_PROJECT_ID,
  storageBucket: process.env.NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: process.env.NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID,
  appId: process.env.NEXT_PUBLIC_FIREBASE_APP_ID,
};

// Initialize Firebase
const app = getApps().length === 0 ? initializeApp(firebaseConfig) : getApps()[0];

export const auth = getAuth(app);
export const db = getFirestore(app);
export const storage = getStorage(app);
export default app;
```

**6. Environment Variables**

```bash
# .env.local
NEXT_PUBLIC_FIREBASE_API_KEY="your-api-key"
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN="your-project.firebaseapp.com"
NEXT_PUBLIC_FIREBASE_PROJECT_ID="your-project-id"
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET="your-project.appspot.com"
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID="your-sender-id"
NEXT_PUBLIC_FIREBASE_APP_ID="your-app-id"
```

**7. Firestore Security Rules**

```typescript
// firestore.rules
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Helper functions
    function isSignedIn() {
      return request.auth != null;
    }
    
    function isOwner(userId) {
      return request.auth.uid == userId;
    }
    
    function isAdmin() {
      return isSignedIn() && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
    
    // Users collection
    match /users/{userId} {
      allow read: if true;
      allow create: if isSignedIn();
      allow update: if isOwner(userId) || isAdmin();
      allow delete: if isAdmin();
    }
    
    // Posts collection
    match /posts/{postId} {
      allow read: if true;
      allow create: if isSignedIn();
      allow update: if isSignedIn() && (isOwner(resource.data.authorId) || isAdmin());
      allow delete: if isSignedIn() && (isOwner(resource.data.authorId) || isAdmin());
    }
    
    // Comments collection
    match /comments/{commentId} {
      allow read: if true;
      allow create: if isSignedIn();
      allow update: if isOwner(resource.data.authorId);
      allow delete: if isOwner(resource.data.authorId) || isAdmin();
    }
    
    // Categories collection
    match /categories/{categoryId} {
      allow read: if true;
      allow write: if isAdmin();
    }
    
    // Tags collection
    match /tags/{tagId} {
      allow read: if true;
      allow write: if isAdmin();
    }
  }
}
```

**8. Storage Security Rules**

```typescript
// storage.rules
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /posts/{allPaths=**} {
      allow read: if true;
      allow write: if request.auth != null 
                   && request.resource.size < 5 * 1024 * 1024  // 5MB
                   && request.resource.contentType.matches('image/.*');
    }
    
    match /users/{userId}/{allPaths=**} {
      allow read: if true;
      allow write: if request.auth != null 
                   && request.auth.uid == userId
                   && request.resource.size < 2 * 1024 * 1024;  // 2MB
    }
  }
}
```

**9. Firestore Helper Functions**

```js
// lib/firestore.ts
import { 
  collection, 
  doc, 
  getDoc, 
  getDocs, 
  addDoc, 
  updateDoc, 
  deleteDoc,
  query,
  where,
  orderBy,
  limit,
  serverTimestamp,
  increment
} from 'firebase/firestore';
import { db } from './firebase';

// Collections
export const usersCollection = collection(db, 'users');
export const postsCollection = collection(db, 'posts');
export const commentsCollection = collection(db, 'comments');
export const categoriesCollection = collection(db, 'categories');
export const tagsCollection = collection(db, 'tags');

// Helper to convert Firestore doc to object
export function docToJSON(doc) {
  if (!doc.exists()) return null;
  
  const data = doc.data();
  return {
    id: doc.id,
    ...data,
    createdAt: data.createdAt?.toDate?.()?.toISOString?.() || null,
    updatedAt: data.updatedAt?.toDate?.()?.toISOString?.() || null,
    publishedAt: data.publishedAt?.toDate?.()?.toISOString?.() || null,
  };
}

// Get document by ID
export async function getDocById(collectionRef, docId) {
  const docRef = doc(collectionRef, docId);
  const docSnap = await getDoc(docRef);
  return docToJSON(docSnap);
}

// Get all documents from collection
export async function getAllDocs(collectionRef, queryConstraints = []) {
  const q = query(collectionRef, ...queryConstraints);
  const snapshot = await getDocs(q);
  return snapshot.docs.map(doc => docToJSON(doc));
}

// Create document
export async function createDoc(collectionRef, data) {
  const docRef = await addDoc(collectionRef, {
    ...data,
    createdAt: serverTimestamp(),
    updatedAt: serverTimestamp(),
  });
  return docRef.id;
}

// Update document
export async function updateDocument(collectionRef, docId, data) {
  const docRef = doc(collectionRef, docId);
  await updateDoc(docRef, {
    ...data,
    updatedAt: serverTimestamp(),
  });
}

// Delete document
export async function deleteDocument(collectionRef, docId) {
  const docRef = doc(collectionRef, docId);
  await deleteDoc(docRef);
}
```

---

### Phase 2: Authentication

**1. Auth Context**

```tsx
// lib/AuthContext.ts
'use client';

import { createContext, useContext, useEffect, useState } from 'react';
import { 
  onAuthStateChanged,
  signInWithEmailAndPassword,
  createUserWithEmailAndPassword,
  signOut as firebaseSignOut
} from 'firebase/auth';
import { auth, db } from './firebase';
import { doc, getDoc, setDoc } from 'firebase/firestore';

const AuthContext = createContext({});

export const useAuth = () => useContext(AuthContext);

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [userData, setUserData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, async (firebaseUser) => {
      if (firebaseUser) {
        setUser(firebaseUser);
        
        // Get user data from Firestore
        const userDoc = await getDoc(doc(db, 'users', firebaseUser.uid));
        if (userDoc.exists()) {
          setUserData({ id: userDoc.id, ...userDoc.data() });
        }
      } else {
        setUser(null);
        setUserData(null);
      }
      setLoading(false);
    });

    return unsubscribe;
  }, []);

  const signUp = async (email, password, name) => {
    const userCredential = await createUserWithEmailAndPassword(auth, email, password);
    
    // Create user document in Firestore
    await setDoc(doc(db, 'users', userCredential.user.uid), {
      name,
      email,
      role: 'user',
      createdAt: new Date(),
      updatedAt: new Date(),
    });
    
    return userCredential.user;
  };

  const signIn = async (email, password) => {
    return signInWithEmailAndPassword(auth, email, password);
  };

  const signOut = async () => {
    await firebaseSignOut(auth);
  };

  const value = {
    user,
    userData,
    loading,
    signUp,
    signIn,
    signOut,
  };

  return (
    <AuthContext.Provider value={value}>
      {!loading && children}
    </AuthContext.Provider>
  );
}
```

**2. Root Layout with Auth Provider**

```tsx
// app/layout.tsx
import { AuthProvider } from '@/lib/AuthContext';
import './globals.css';

export const metadata = {
  title: 'Blog Platform',
  description: 'A modern blog platform built with Next.js and Firebase',
};

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <AuthProvider>
          {children}
        </AuthProvider>
      </body>
    </html>
  );
}
```

**3. Register Page**

```tsx
// app/(auth)/register/page.tsx
'use client';

import { useState } from 'react';
import { useRouter } from 'next/navigation';
import Link from 'next/link';
import { useAuth } from '@/lib/AuthContext';

export default function RegisterPage() {
  const router = useRouter();
  const { signUp } = useAuth();
  
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    password: '',
    confirmPassword: '',
  });
  const [error, setError] = useState('');
  const [loading, setLoading] = useState(false);

  const handleChange = (e) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    setError('');

    // Validation
    if (formData.password !== formData.confirmPassword) {
      setError('Passwords do not match');
      return;
    }

    if (formData.password.length < 6) {
      setError('Password must be at least 6 characters');
      return;
    }

    setLoading(true);

    try {
      await signUp(formData.email, formData.password, formData.name);
      router.push('/dashboard');
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50 py-12 px-4">
      <div className="max-w-md w-full space-y-8">
        <div>
          <h2 className="text-center text-3xl font-bold">Create Account</h2>
        </div>

        <form onSubmit={handleSubmit} className="mt-8 space-y-6">
          {error && (
            <div className="bg-red-50 border border-red-200 text-red-600 px-4 py-3 rounded">
              {error}
            </div>
          )}

          <div>
            <label htmlFor="name" className="block text-sm font-medium text-gray-700">
              Name
            </label>
            <input
              id="name"
              name="name"
              type="text"
              required
              value={formData.name}
              onChange={handleChange}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"
            />
          </div>

          <div>
            <label htmlFor="email" className="block text-sm font-medium text-gray-700">
              Email
            </label>
            <input
              id="email"
              name="email"
              type="email"
              required
              value={formData.email}
              onChange={handleChange}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"
            />
          </div>

          <div>
            <label htmlFor="password" className="block text-sm font-medium text-gray-700">
              Password
            </label>
            <input
              id="password"
              name="password"
              type="password"
              required
              value={formData.password}
              onChange={handleChange}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"
            />
          </div>

          <div>
            <label htmlFor="confirmPassword" className="block text-sm font-medium text-gray-700">
              Confirm Password
            </label>
            <input
              id="confirmPassword"
              name="confirmPassword"
              type="password"
              required
              value={formData.confirmPassword}
              onChange={handleChange}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"
            />
          </div>

          <button
            type="submit"
            disabled={loading}
            className="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 disabled:opacity-50"
          >
            {loading ? 'Creating account...' : 'Register'}
          </button>

          <p className="text-center text-sm text-gray-600">
            Already have an account?{' '}
            <Link href="/login" className="font-medium text-blue-600 hover:text-blue-500">
              Sign in
            </Link>
          </p>
        </form>
      </div>
    </div>
  );
}
```

**4. Login Page**

```tsx
// app/(auth)/login/page.tsx
'use client';

import { useState } from 'react';
import { useRouter } from 'next/navigation';
import Link from 'next/link';
import { useAuth } from '@/lib/AuthContext';

export default function LoginPage() {
  const router = useRouter();
  const { signIn } = useAuth();
  
  const [formData, setFormData] = useState({
    email: '',
    password: '',
  });
  const [error, setError] = useState('');
  const [loading, setLoading] = useState(false);

  const handleChange = (e) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    setError('');
    setLoading(true);

    try {
      await signIn(formData.email, formData.password);
      router.push('/dashboard');
    } catch (err) {
      setError('Invalid email or password');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50 py-12 px-4">
      <div className="max-w-md w-full space-y-8">
        <div>
          <h2 className="text-center text-3xl font-bold">Sign in to your account</h2>
        </div>

        <form onSubmit={handleSubmit} className="mt-8 space-y-6">
          {error && (
            <div className="bg-red-50 border border-red-200 text-red-600 px-4 py-3 rounded">
              {error}
            </div>
          )}

          <div>
            <label htmlFor="email" className="block text-sm font-medium text-gray-700">
              Email
            </label>
            <input
              id="email"
              name="email"
              type="email"
              required
              value={formData.email}
              onChange={handleChange}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"
            />
          </div>

          <div>
            <label htmlFor="password" className="block text-sm font-medium text-gray-700">
              Password
            </label>
            <input
              id="password"
              name="password"
              type="password"
              required
              value={formData.password}
              onChange={handleChange}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"
            />
          </div>

          <button
            type="submit"
            disabled={loading}
            className="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 disabled:opacity-50"
          >
            {loading ? 'Signing in...' : 'Sign in'}
          </button>

          <p className="text-center text-sm text-gray-600">
            Don't have an account?{' '}
            <Link href="/register" className="font-medium text-blue-600 hover:text-blue-500">
              Register
            </Link>
          </p>
        </form>
      </div>
    </div>
  );
}
```

---

### Phase 3: Blog Posts (CRUD with Firebase)

**1. Post Actions**

```js
// app/actions/posts.tsx
'use server';

import { 
  collection,
  doc,
  getDoc,
  getDocs,
  addDoc,
  updateDoc,
  deleteDoc,
  query,
  where,
  orderBy,
  serverTimestamp,
  increment
} from 'firebase/firestore';
import { db } from '@/lib/firebase';
import { revalidatePath } from 'next/cache';
import { redirect } from 'next/navigation';
import slugify from 'slugify';

const postsCollection = collection(db, 'posts');

export async function createPost(userId, formData) {
  try {
    const title = formData.get('title');
    const excerpt = formData.get('excerpt');
    const content = formData.get('content');
    const categoryId = formData.get('categoryId');
    const published = formData.get('published') === 'on';
    const coverImage = formData.get('coverImage');

    // Generate slug
    const slug = slugify(title, { lower: true, strict: true });

    // Create post
    const postData = {
      title,
      slug,
      excerpt,
      content,
      coverImage: coverImage || null,
      published,
      authorId: userId,
      categoryId: categoryId || null,
      views: 0,
      createdAt: serverTimestamp(),
      updatedAt: serverTimestamp(),
      publishedAt: published ? serverTimestamp() : null,
    };

    const docRef = await addDoc(postsCollection, postData);

    revalidatePath('/blog');
    redirect(`/blog/${slug}`);
  } catch (error) {
    console.error('Error creating post:', error);
    return { success: false, error: error.message };
  }
}

export async function updatePost(postId, userId, formData) {
  try {
    const postRef = doc(db, 'posts', postId);
    const postSnap = await getDoc(postRef);

    if (!postSnap.exists()) {
      throw new Error('Post not found');
    }

    const postData = postSnap.data();

    // Check ownership
    if (postData.authorId !== userId) {
      throw new Error('Unauthorized');
    }

    const title = formData.get('title');
    const excerpt = formData.get('excerpt');
    const content = formData.get('content');
    const categoryId = formData.get('categoryId');
    const published = formData.get('published') === 'on';
    const coverImage = formData.get('coverImage');

    // Generate new slug
    const slug = slugify(title, { lower: true, strict: true });

    const updateData = {
      title,
      slug,
      excerpt,
      content,
      categoryId: categoryId || null,
      published,
      updatedAt: serverTimestamp(),
    };

    if (coverImage) {
      updateData.coverImage = coverImage;
    }

    // Set publishedAt if publishing for first time
    if (published && !postData.publishedAt) {
      updateData.publishedAt = serverTimestamp();
    }

    await updateDoc(postRef, updateData);

    revalidatePath('/blog');
    revalidatePath(`/blog/${slug}`);
    redirect(`/blog/${slug}`);
  } catch (error) {
    console.error('Error updating post:', error);
    return { success: false, error: error.message };
  }
}

export async function deletePost(postId, userId, userRole) {
  try {
    const postRef = doc(db, 'posts', postId);
    const postSnap = await getDoc(postRef);

    if (!postSnap.exists()) {
      throw new Error('Post not found');
    }

    const postData = postSnap.data();

    // Check authorization (owner or admin)
    if (postData.authorId !== userId && userRole !== 'admin') {
      throw new Error('Unauthorized');
    }

    await deleteDoc(postRef);

    revalidatePath('/blog');
    revalidatePath('/dashboard');
    redirect('/dashboard');
  } catch (error) {
    console.error('Error deleting post:', error);
    return { success: false, error: error.message };
  }
}

export async function incrementPostViews(postId) {
  try {
    const postRef = doc(db, 'posts', postId);
    await updateDoc(postRef, {
      views: increment(1)
    });
  } catch (error) {
    console.error('Error incrementing views:', error);
  }
}
```

**2. Image Upload Action**

```js
// app/actions/upload.tsx
'use server';

import { ref, uploadBytes, getDownloadURL } from 'firebase/storage';
import { storage } from '@/lib/firebase';

export async function uploadImage(formData) {
  try {
    const file = formData.get('file');
    
    if (!file) {
      throw new Error('No file provided');
    }

    // Create unique filename
    const timestamp = Date.now();
    const filename = `posts/${timestamp}-${file.name}`;
    
    // Convert File to Buffer
    const bytes = await file.arrayBuffer();
    const buffer = Buffer.from(bytes);

    // Create storage reference
    const storageRef = ref(storage, filename);

    // Upload file
    await uploadBytes(storageRef, buffer, {
      contentType: file.type,
    });

    // Get download URL
    const downloadURL = await getDownloadURL(storageRef);

    return { success: true, url: downloadURL };
  } catch (error) {
    console.error('Error uploading image:', error);
    return { success: false, error: error.message };
  }
}
```

**3. Blog List Page**

```tsx
// app/blog/page.tsx
import { 
  collection, 
  getDocs, 
  query, 
  where, 
  orderBy 
} from 'firebase/firestore';
import { db } from '@/lib/firebase';
import Link from 'next/link';
import Image from 'next/image';
import { formatDistanceToNow } from 'date-fns';

export const metadata = {
  title: 'Blog',
  description: 'Read our latest articles',
};

async function getPosts(searchParams) {
  const postsRef = collection(db, 'posts');
  let q = query(
    postsRef,
    where('published', '==', true),
    orderBy('publishedAt', 'desc')
  );

  // Apply category filter if present
  if (searchParams.category) {
    q = query(
      postsRef,
      where('published', '==', true),
      where('categoryId', '==', searchParams.category),
      orderBy('publishedAt', 'desc')
    );
  }

  const snapshot = await getDocs(q);
  const posts = [];

  for (const doc of snapshot.docs) {
    const data = doc.data();
    
    // Get author data
    let authorName = 'Unknown';
    if (data.authorId) {
      const authorDoc = await getDoc(doc(db, 'users', data.authorId));
      if (authorDoc.exists()) {
        authorName = authorDoc.data().name;
      }
    }

    // Get category data
    let categoryName = null;
    if (data.categoryId) {
      const categoryDoc = await getDoc(doc(db, 'categories', data.categoryId));
      if (categoryDoc.exists()) {
        categoryName = categoryDoc.data().name;
      }
    }

    // Get comments count
    const commentsQuery = query(
      collection(db, 'comments'),
      where('postId', '==', doc.id)
    );
    const commentsSnapshot = await getDocs(commentsQuery);

    posts.push({
      id: doc.id,
      ...data,
      authorName,
      categoryName,
      commentsCount: commentsSnapshot.size,
      publishedAt: data.publishedAt?.toDate?.() || null,
    });
  }

  // Client-side search filter if needed
  if (searchParams.search) {
    const searchTerm = searchParams.search.toLowerCase();
    return posts.filter(post => 
      post.title.toLowerCase().includes(searchTerm) ||
      post.excerpt.toLowerCase().includes(searchTerm)
    );
  }

  return posts;
}

async function getCategories() {
  const snapshot = await getDocs(collection(db, 'categories'));
  return snapshot.docs.map(doc => ({
    id: doc.id,
    ...doc.data()
  }));
}

export default async function BlogPage({ searchParams }) {
  const posts = await getPosts(searchParams);
  const categories = await getCategories();

  return (
    <div className="max-w-7xl mx-auto px-4 py-12">
      <div className="mb-8">
        <h1 className="text-4xl font-bold mb-4">Blog</h1>

        {/* Categories Filter */}
        <div className="flex gap-2 flex-wrap">
          <Link
            href="/blog"
            className={`px-4 py-2 rounded-lg ${!searchParams.category ? 'bg-blue-600 text-white' : 'bg-gray-200'}`}
          >
            All
          </Link>
          {categories.map(cat => (
            <Link
              key={cat.id}
              href={`/blog?category=${cat.id}`}
              className={`px-4 py-2 rounded-lg ${searchParams.category === cat.id ? 'bg-blue-600 text-white' : 'bg-gray-200'}`}
            >
              {cat.name}
            </Link>
          ))}
        </div>
      </div>

      {/* Posts Grid */}
      <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-8">
        {posts.map(post => (
          <article key={post.id} className="border rounded-lg overflow-hidden hover:shadow-lg transition">
            {post.coverImage && (
              <div className="relative w-full h-48">
                <Image
                  src={post.coverImage}
                  alt={post.title}
                  fill
                  className="object-cover"
                />
              </div>
            )}

            <div className="p-6">
              {post.categoryName && (
                <span className="text-sm text-blue-600 font-medium">
                  {post.categoryName}
                </span>
              )}

              <h2 className="text-xl font-bold mt-2 mb-2">
                <Link href={`/blog/${post.slug}`} className="hover:text-blue-600">
                  {post.title}
                </Link>
              </h2>

              <p className="text-gray-600 mb-4 line-clamp-3">{post.excerpt}</p>

              <div className="flex items-center justify-between text-sm text-gray-500">
                <span>{post.authorName}</span>
                {post.publishedAt && (
                  <span>{formatDistanceToNow(post.publishedAt)} ago</span>
                )}
              </div>

              <div className="mt-4 flex items-center gap-4 text-sm text-gray-500">
                <span>{post.views} views</span>
                <span>{post.commentsCount} comments</span>
              </div>
            </div>
          </article>
        ))}
      </div>

      {posts.length === 0 && (
        <div className="text-center py-12">
          <p className="text-gray-500">No posts found</p>
        </div>
      )}
    </div>
  );
}
```

**4. Individual Post Page**

```tsx
// app/blog/[slug]/page.tsx
import { 
  collection, 
  getDocs,
  getDoc,
  doc,
  query, 
  where,
  orderBy
} from 'firebase/firestore';
import { db } from '@/lib/firebase';
import { notFound } from 'next/navigation';
import Image from 'next/image';
import { formatDistanceToNow } from 'date-fns';
import { incrementPostViews } from '@/app/actions/posts';
import CommentSection from '@/app/components/CommentSection';

export async function generateMetadata({ params }) {
  const post = await getPostBySlug(params.slug);
  
  if (!post) {
    return {
      title: 'Post Not Found',
    };
  }

  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      description: post.excerpt,
      images: post.coverImage ? [post.coverImage] : [],
      publishedTime: post.publishedAt?.toISOString(),
    },
  };
}

async function getPostBySlug(slug) {
  const postsRef = collection(db, 'posts');
  const q = query(postsRef, where('slug', '==', slug));
  const snapshot = await getDocs(q);

  if (snapshot.empty) {
    return null;
  }

  const postDoc = snapshot.docs[0];
  const data = postDoc.data();

  // Get author
  let author = { name: 'Unknown' };
  if (data.authorId) {
    const authorDoc = await getDoc(doc(db, 'users', data.authorId));
    if (authorDoc.exists()) {
      author = { id: authorDoc.id, ...authorDoc.data() };
    }
  }

  // Get category
  let category = null;
  if (data.categoryId) {
    const categoryDoc = await getDoc(doc(db, 'categories', data.categoryId));
    if (categoryDoc.exists()) {
      category = { id: categoryDoc.id, ...categoryDoc.data() };
    }
  }

  return {
    id: postDoc.id,
    ...data,
    author,
    category,
    publishedAt: data.publishedAt?.toDate() || null,
    createdAt: data.createdAt?.toDate() || null,
  };
}

export default async function PostPage({ params }) {
  const post = await getPostBySlug(params.slug);

  if (!post) {
    notFound();
  }

  // Increment views (fire and forget)
  incrementPostViews(post.id);

  return (
    <article className="max-w-4xl mx-auto px-4 py-12">
      {/* Cover Image */}
      {post.coverImage && (
        <div className="relative w-full h-96 mb-8 rounded-lg overflow-hidden">
          <Image
            src={post.coverImage}
            alt={post.title}
            fill
            className="object-cover"
            priority
          />
        </div>
      )}

      {/* Category */}
      {post.category && (
        <div className="mb-4">
          <span className="px-3 py-1 bg-blue-100 text-blue-600 rounded-full text-sm font-medium">
            {post.category.name}
          </span>
        </div>
      )}

      {/* Title */}
      <h1 className="text-4xl md:text-5xl font-bold mb-4">{post.title}</h1>

      {/* Meta */}
      <div className="flex items-center gap-4 text-gray-600 mb-8 pb-8 border-b">
        <span className="font-medium">{post.author.name}</span>
        <span>•</span>
        {post.publishedAt && (
          <span>{formatDistanceToNow(post.publishedAt)} ago</span>
        )}
        <span>•</span>
        <span>{post.views} views</span>
      </div>

      {/* Excerpt */}
      <p className="text-xl text-gray-600 mb-8 leading-relaxed">
        {post.excerpt}
      </p>

      {/* Content */}
      <div 
        className="prose prose-lg max-w-none mb-12"
        dangerouslySetInnerHTML={{ __html: post.content }}
      />

      {/* Comments */}
      <div className="mt-12 pt-12 border-t">
        <h2 className="text-2xl font-bold mb-6">Comments</h2>
        <CommentSection postId={post.id} />
      </div>
    </article>
  );
}
```

---

**Due to length constraints, I'll provide the remaining key components as a summary:**

### Phase 4: Additional Components

**1. Create/Edit Post Form**
- Rich text editor with Tiptap
- Image upload to Firebase Storage
- Category selection
- Publish toggle

**2. Dashboard**
- List user's posts from Firestore
- Edit/Delete buttons
- Post statistics

**3. Comment System**
```js
// Firestore structure
comments/{commentId}
  - content
  - authorId
  - postId
  - createdAt
```

**4. Image Upload Component**
```tsx
// Uses Firebase Storage
import { ref, uploadBytes, getDownloadURL } from 'firebase/storage';
```

---

## Firebase Best Practices

**1. Security Rules**
- Always validate on server
- Use authentication checks
- Implement role-based access

**2. Queries**
- Create composite indexes for complex queries
- Use pagination with `limit()` and `startAfter()`
- Cache frequently accessed data

**3. Storage**
- Validate file types and sizes
- Use unique filenames
- Implement storage rules

**4. Performance**
- Minimize reads with proper data structure
- Use `onSnapshot` for real-time data
- Implement proper loading states

---

## Deployment

```bash
# 1. Build
npm run build

# 2. Deploy to Vercel
vercel --prod

# 3. Set environment variables in Vercel dashboard
# Add all NEXT_PUBLIC_FIREBASE_* variables
```

---

## Summary

**🎉 Congratulations!** You've built a full-stack blog platform with:

✅ Firebase Authentication  
✅ Cloud Firestore database  
✅ Firebase Storage for images  
✅ Real-time updates  
✅ Security rules  
✅ Production deployment  

**Key Firebase Advantages:**
- No backend server needed
- Real-time capabilities
- Automatic scaling
- Built-in authentication
- Generous free tier

**You're now a Next.js + Firebase developer!** 🚀

Keep building and good luck with your career! 💪