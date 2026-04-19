

# 📘 Chapter 7: Semantic HTML, Forms & Structured Data

> **🟣 Modern, professional HTML — Learning semantic layout tags, building functional forms, creating data tables, and understanding accessibility & SEO fundamentals.**

---

## 7.1 What is Semantic HTML?

In Chapter 6, we used `<div>` to group content. But `<div>` has **no meaning** — it's just a generic box. **Semantic HTML** replaces meaningless `<div>` elements with **tags that describe their purpose**.

### The Problem with `<div>` Everywhere:

```html
<!-- ❌ Non-semantic: Everything is a <div> -->
<div>Logo and site title here</div>
<div>Navigation links here</div>
<div>Main article content here</div>
<div>Sidebar content here</div>
<div>Copyright info here</div>

<!-- A screen reader sees: "div, div, div, div, div"
     A developer sees: "Which div is what?!" -->
```

### The Solution — Semantic Tags:

```html
<!-- ✅ Semantic: Tags DESCRIBE their content -->
<header>Logo and site title here</header>
<nav>Navigation links here</nav>
<main>Main article content here</main>
<aside>Sidebar content here</aside>
<footer>Copyright info here</footer>

<!-- A screen reader sees: "header, navigation, main content, sidebar, footer"
     A developer sees: Clear, self-documenting code! -->
```

### Why Semantic HTML Matters:

| Benefit | Explanation |
|---------|-------------|
| **Accessibility** | Screen readers announce sections: "navigation," "main content," "footer" |
| **SEO** | Search engines understand page structure and content importance |
| **Code Readability** | Developers instantly understand what each section is |
| **Maintainability** | Easier to find and update specific sections |
| **Future-proofing** | Browsers and tools understand your page better |

---

## 7.2 `<header>` — Page or Section Header

The `<header>` tag represents **introductory content** — typically containing the logo, site title, tagline, or navigation.

### Syntax:

```html
<header>
    <h1>Website Name</h1>
    <p>Tagline or description</p>
</header>
```

### Key Facts:

| Feature | Detail |
|---------|--------|
| **Type** | Block-level, semantic |
| **Purpose** | Introductory/banner content for page or section |
| **Can contain** | Headings, logos, navigation, search bars |
| **Multiple allowed?** | ✅ Yes — a page can have multiple headers |

### Examples:

```html
<!-- Page-level header (most common) -->
<header>
    <img src="images/logo.png" alt="TechBlog Logo" width="150" height="45">
    <h1>TechBlog</h1>
    <p>Your daily source for web development tips and tutorials</p>
</header>

<!-- Section-level header -->
<section>
    <header>
        <h2>Latest Articles</h2>
        <p>Updated daily with fresh content</p>
    </header>
    <p>Article content here...</p>
</section>

<!-- Article-level header -->
<article>
    <header>
        <h2>Getting Started with HTML</h2>
        <p>By Ali Khan | January 15, 2025</p>
    </header>
    <p>Article body content here...</p>
</article>
```

### ❌ What `<header>` is NOT:

```html
<!-- ❌ WRONG: <header> is NOT just for <h1> tags -->
<!-- If you only have a heading, you don't need <header> -->
<header>
    <h1>Title</h1>
</header>

<!-- ✅ Just use the heading directly if that's all you have -->
<h1>Title</h1>

<!-- ✅ Use <header> when you have MULTIPLE elements forming a header -->
<header>
    <img src="logo.png" alt="Logo" width="100" height="30">
    <h1>My Website</h1>
    <nav>
        <ul>
            <li><a href="/">Home</a></li>
            <li><a href="/about">About</a></li>
        </ul>
    </nav>
</header>
```

---

## 7.3 `<nav>` — Navigation Section

The `<nav>` tag identifies a section of **major navigation links**.

### Syntax:

```html
<nav>
    <ul>
        <li><a href="index.html">Home</a></li>
        <li><a href="about.html">About</a></li>
        <li><a href="contact.html">Contact</a></li>
    </ul>
</nav>
```

### Key Facts:

| Feature | Detail |
|---------|--------|
| **Type** | Block-level, semantic |
| **Purpose** | Contains major navigation links |
| **Screen readers** | Announce "navigation" so users can skip to or from it |
| **Multiple allowed?** | ✅ Yes — page nav, footer nav, sidebar nav |

### Multiple `<nav>` Elements:

```html
<!-- Primary navigation (top of page) -->
<nav>
    <ul>
        <li><a href="index.html">Home</a></li>
        <li><a href="about.html">About</a></li>
        <li><a href="services.html">Services</a></li>
        <li><a href="contact.html">Contact</a></li>
    </ul>
</nav>

<!-- Breadcrumb navigation -->
<nav>
    <p>
        <a href="index.html">Home</a> &gt;
        <a href="blog.html">Blog</a> &gt;
        <span>Current Article</span>
    </p>
</nav>

<!-- Footer navigation -->
<footer>
    <nav>
        <ul>
            <li><a href="privacy.html">Privacy Policy</a></li>
            <li><a href="terms.html">Terms of Service</a></li>
            <li><a href="sitemap.html">Sitemap</a></li>
        </ul>
    </nav>
</footer>
```

### ❌ What `<nav>` is NOT For:

```html
<!-- ❌ Not every group of links needs <nav> -->
<!-- Social media links in a paragraph are NOT major navigation -->
<p>
    Follow us on
    <a href="https://twitter.com">Twitter</a> and
    <a href="https://github.com">GitHub</a>.
</p>

<!-- ✅ Use <nav> only for MAJOR navigation blocks -->
```

---

## 7.4 `<main>` — The Primary Content

The `<main>` tag wraps the **primary, unique content** of the page — the content that is **different on every page**.

### Syntax:

```html
<main>
    <h1>Page Title</h1>
    <p>Main content goes here...</p>
</main>
```

### Key Facts:

| Feature | Detail |
|---------|--------|
| **Type** | Block-level, semantic |
| **Purpose** | Contains the page's unique, primary content |
| **Only ONE per page** | ❌ Cannot have multiple `<main>` elements |
| **Should NOT contain** | Headers, footers, navigation (those are outside `<main>`) |

### What Goes Inside vs Outside `<main>`:

```
OUTSIDE <main>:          INSIDE <main>:
├── <header>             ├── Page-specific content
├── <nav>                ├── Articles
├── <footer>             ├── Blog posts
                         ├── Product listings
                         ├── Forms
                         └── Unique page content
```

### Example:

```html
<body>
    <header>
        <h1>TechBlog</h1>
        <nav>
            <ul>
                <li><a href="index.html">Home</a></li>
                <li><a href="about.html">About</a></li>
            </ul>
        </nav>
    </header>

    <main>
        <!-- This content is unique to THIS page -->
        <h2>Welcome to TechBlog</h2>
        <p>Discover the latest in web development.</p>

        <article>
            <h3>Learning HTML</h3>
            <p>HTML is the foundation of every website...</p>
        </article>
    </main>

    <footer>
        <p>&copy; 2025 TechBlog</p>
    </footer>
</body>
```

> 📌 **Rule:** The `<header>`, `<nav>`, and `<footer>` are typically the SAME across all pages. The `<main>` content is what CHANGES from page to page.

---

## 7.5 `<section>` — Thematic Grouping

The `<section>` tag groups **thematically related content** — like a chapter in a book.

### Syntax:

```html
<section>
    <h2>Section Title</h2>
    <p>Section content here...</p>
</section>
```

### Key Facts:

| Feature | Detail |
|---------|--------|
| **Type** | Block-level, semantic |
| **Purpose** | Groups related content under a common theme |
| **Should have** | A heading (`<h2>`-`<h6>`) as its first child |
| **Multiple allowed?** | ✅ Yes — pages often have many sections |

### Examples:

```html
<main>
    <section>
        <h2>About Us</h2>
        <p>We are a team of passionate web developers...</p>
    </section>

    <section>
        <h2>Our Services</h2>
        <p>We offer web design, development, and SEO...</p>
    </section>

    <section>
        <h2>Testimonials</h2>
        <p>What our clients say about us...</p>
    </section>

    <section>
        <h2>Contact Us</h2>
        <p>Get in touch with our team...</p>
    </section>
</main>
```

### `<section>` vs `<div>`:

```html
<!-- ✅ Use <section> when content has a THEME and a HEADING -->
<section>
    <h2>Our Team</h2>
    <p>Meet the people behind our success.</p>
</section>

<!-- ✅ Use <div> when grouping has NO thematic meaning (just for CSS) -->
<div>
    <img src="icon.png" alt="Icon" width="32" height="32">
    <p>Some text next to an icon</p>
</div>
```

> 📌 **Rule:** If you can give it a heading, it's probably a `<section>`. If it's just a wrapper for CSS, use `<div>`.

---

## 7.6 `<article>` — Self-Contained Content

The `<article>` tag represents **self-contained, independent content** that could stand alone — like a blog post, news article, product card, or forum comment.

### Syntax:

```html
<article>
    <h2>Article Title</h2>
    <p>Article content...</p>
</article>
```

### Key Facts:

| Feature | Detail |
|---------|--------|
| **Type** | Block-level, semantic |
| **Purpose** | Independent, reusable content |
| **Test** | Could this content be shared on its own (e.g., in an RSS feed)? If yes → use `<article>` |
| **Multiple allowed?** | ✅ Yes — blog listing pages have many articles |

### Examples:

```html
<!-- Blog post -->
<article>
    <header>
        <h2>Getting Started with HTML</h2>
        <p>By Ali Khan | <time>January 15, 2025</time></p>
    </header>
    <p>HTML is the foundation of every website. In this article, 
       we explore the basics of HTML and how to create your 
       first web page.</p>
    <p>HTML stands for HyperText Markup Language. It uses tags 
       to structure content that browsers can display.</p>
    <footer>
        <p>Tags: HTML, Web Development, Beginners</p>
    </footer>
</article>

<!-- Product card -->
<article>
    <img src="images/headphones.jpg" 
         alt="ProSound wireless headphones" 
         width="300" 
         height="200">
    <h3>ProSound Wireless Headphones</h3>
    <p>Premium noise-cancelling headphones with 40-hour battery.</p>
    <p><strong>$49.99</strong> <del>$79.99</del></p>
    <a href="products/headphones.html">View Details</a>
</article>

<!-- User comment -->
<article>
    <header>
        <strong>Sara Ahmed</strong>
        <time>January 16, 2025 at 3:45 PM</time>
    </header>
    <p>Great article! Very helpful for beginners. I learned a 
       lot from this tutorial.</p>
</article>
```

### `<article>` vs `<section>`:

| Feature | `<article>` | `<section>` |
|---------|------------|-------------|
| **Self-contained?** | ✅ Yes — makes sense on its own | ❌ No — part of a larger whole |
| **Reusable?** | ✅ Can be shared independently | ❌ Depends on surrounding content |
| **Examples** | Blog post, news article, comment, product card | About section, Services section, FAQ section |
| **Test** | "Could I share this alone?" | "Does this group thematically?" |

```html
<!-- A page with sections containing articles -->
<main>
    <section>
        <h2>Latest Blog Posts</h2>

        <article>
            <h3>Post 1: HTML Basics</h3>
            <p>Content of post 1...</p>
        </article>

        <article>
            <h3>Post 2: CSS Introduction</h3>
            <p>Content of post 2...</p>
        </article>

        <article>
            <h3>Post 3: JavaScript Fundamentals</h3>
            <p>Content of post 3...</p>
        </article>
    </section>
</main>
```

---

## 7.7 `<aside>` — Side Content

The `<aside>` tag represents content that is **tangentially related** to the main content — like a sidebar, pull quote, or additional info box.

### Syntax:

```html
<aside>
    <h3>Related Links</h3>
    <ul>
        <li><a href="#">Link 1</a></li>
        <li><a href="#">Link 2</a></li>
    </ul>
</aside>
```

### Key Facts:

| Feature | Detail |
|---------|--------|
| **Type** | Block-level, semantic |
| **Purpose** | Content related but not essential to main content |
| **Common uses** | Sidebars, pull quotes, ads, related articles, author bio |

### Examples:

```html
<!-- Sidebar with related articles -->
<aside>
    <h3>Related Articles</h3>
    <ul>
        <li><a href="css-basics.html">CSS Basics for Beginners</a></li>
        <li><a href="js-intro.html">Introduction to JavaScript</a></li>
        <li><a href="responsive.html">Responsive Web Design</a></li>
    </ul>
</aside>

<!-- Author info box -->
<aside>
    <h3>About the Author</h3>
    <img src="images/author.jpg" alt="Ali Khan" width="100" height="100">
    <p><strong>Ali Khan</strong> is a front-end developer 
       with 5 years of experience.</p>
</aside>

<!-- Pull quote inside an article -->
<article>
    <h2>The Importance of HTML</h2>
    <p>HTML is the backbone of the web. Every website starts 
       with HTML before any styling or scripting is applied.</p>

    <aside>
        <blockquote>
            <p>"HTML is the skeleton. CSS is the skin. 
                JavaScript is the muscles."</p>
        </blockquote>
    </aside>

    <p>Understanding HTML deeply will make you a better 
       developer in every other web technology.</p>
</article>
```

---

## 7.8 `<footer>` — Page or Section Footer

The `<footer>` tag represents **closing content** — typically copyright info, contact details, social links, or site navigation.

### Syntax:

```html
<footer>
    <p>&copy; 2025 My Website. All rights reserved.</p>
</footer>
```

### Key Facts:

| Feature | Detail |
|---------|--------|
| **Type** | Block-level, semantic |
| **Purpose** | Closing/footer content for page or section |
| **Multiple allowed?** | ✅ Yes — page footer, article footer, section footer |

### Examples:

```html
<!-- Page footer -->
<footer>
    <nav>
        <ul>
            <li><a href="privacy.html">Privacy Policy</a></li>
            <li><a href="terms.html">Terms of Service</a></li>
            <li><a href="sitemap.html">Sitemap</a></li>
        </ul>
    </nav>
    <p>Follow us:
        <a href="https://twitter.com" target="_blank" rel="noopener noreferrer">Twitter</a> |
        <a href="https://github.com" target="_blank" rel="noopener noreferrer">GitHub</a>
    </p>
    <p><small>&copy; 2025 WebDev Academy. All rights reserved.</small></p>
</footer>

<!-- Article footer -->
<article>
    <h2>HTML Tutorial</h2>
    <p>Article content here...</p>
    <footer>
        <p>Written by Ali Khan | Published: January 2025</p>
        <p>Tags: HTML, Tutorial, Beginners</p>
    </footer>
</article>
```

---

## 7.9 Complete Semantic Layout — Putting It All Together

Here's a **professional, real-world page layout** using all semantic tags:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="TechBlog - Web development articles and tutorials">
    <title>TechBlog - Home</title>
</head>
<body>

    <!-- ===== PAGE HEADER ===== -->
    <header>
        <img src="images/logo.png" alt="TechBlog Logo" width="150" height="45">
        <h1>TechBlog</h1>
        <p>Your daily source for web development knowledge</p>

        <nav>
            <ul>
                <li><strong>Home</strong></li>
                <li><a href="articles.html">Articles</a></li>
                <li><a href="about.html">About</a></li>
                <li><a href="contact.html">Contact</a></li>
            </ul>
        </nav>
    </header>

    <!-- ===== MAIN CONTENT ===== -->
    <main>

        <!-- Hero / Introduction Section -->
        <section>
            <h2>Welcome to TechBlog</h2>
            <p>Discover tutorials, tips, and best practices for 
               modern web development. Start your journey today!</p>
        </section>

        <!-- Latest Articles Section -->
        <section>
            <h2>Latest Articles</h2>

            <!-- Article 1 -->
            <article>
                <header>
                    <h3>Getting Started with HTML</h3>
                    <p>By Ali Khan | <time datetime="2025-01-15">January 15, 2025</time></p>
                </header>
                <figure>
                    <img src="images/html-article.jpg" 
                         alt="HTML code displayed on a monitor" 
                         width="400" 
                         height="250">
                    <figcaption>HTML — The foundation of every website</figcaption>
                </figure>
                <p>HTML is the foundation of every website. In this 
                   beginner-friendly guide, we cover everything you 
                   need to know to write your first web page.</p>
                <footer>
                    <a href="blog/html-basics.html">Read Full Article →</a>
                </footer>
            </article>

            <!-- Article 2 -->
            <article>
                <header>
                    <h3>CSS Flexbox Complete Guide</h3>
                    <p>By Sara Ahmed | <time datetime="2025-01-12">January 12, 2025</time></p>
                </header>
                <figure>
                    <img src="images/css-article.jpg" 
                         alt="CSS Flexbox layout examples" 
                         width="400" 
                         height="250">
                    <figcaption>Master CSS Flexbox for modern layouts</figcaption>
                </figure>
                <p>Flexbox is one of the most powerful CSS layout 
                   tools. Learn how to create flexible, responsive 
                   layouts with ease.</p>
                <footer>
                    <a href="blog/css-flexbox.html">Read Full Article →</a>
                </footer>
            </article>

        </section>

    </main>

    <!-- ===== SIDEBAR ===== -->
    <aside>
        <section>
            <h2>About the Author</h2>
            <img src="images/author.jpg" alt="Ali Khan" width="120" height="120">
            <p><strong>Ali Khan</strong></p>
            <p>Front-end developer and educator. I write about 
               HTML, CSS, and JavaScript.</p>
        </section>

        <section>
            <h2>Popular Tags</h2>
            <ul>
                <li><a href="tags/html.html">HTML</a></li>
                <li><a href="tags/css.html">CSS</a></li>
                <li><a href="tags/javascript.html">JavaScript</a></li>
                <li><a href="tags/accessibility.html">Accessibility</a></li>
            </ul>
        </section>
    </aside>

    <!-- ===== PAGE FOOTER ===== -->
    <footer>
        <nav>
            <ul>
                <li><a href="privacy.html">Privacy Policy</a></li>
                <li><a href="terms.html">Terms of Service</a></li>
                <li><a href="sitemap.html">Sitemap</a></li>
            </ul>
        </nav>
        <p><small>&copy; 2025 TechBlog. All rights reserved.</small></p>
    </footer>

</body>
</html>
```

### Visual Structure Map:

```
┌─────────────────────────────────────────────────┐
│  <header>                                       │
│    Logo | Site Title | Tagline                  │
│    <nav> Home | Articles | About | Contact      │
│  </header>                                      │
├─────────────────────────────────────────────────┤
│  <main>                          │  <aside>     │
│    <section> Welcome </section>  │   Author Bio │
│    <section>                     │   Popular    │
│      <article> Post 1 </article> │   Tags       │
│      <article> Post 2 </article> │              │
│    </section>                    │              │
│  </main>                         │  </aside>    │
├─────────────────────────────────────────────────┤
│  <footer>                                       │
│    Privacy | Terms | Sitemap                    │
│    © 2025 TechBlog                              │
│  </footer>                                      │
└─────────────────────────────────────────────────┘
```

---

## 7.10 The `<time>` Element

The `<time>` tag represents a **specific date, time, or duration** in a machine-readable format.

### Syntax:

```html
<time datetime="2025-01-15">January 15, 2025</time>
```

### Examples:

```html
<!-- Date -->
<p>Published on <time datetime="2025-01-15">January 15, 2025</time></p>

<!-- Date and Time -->
<p>Meeting at <time datetime="2025-01-20T14:30">2:30 PM on January 20</time></p>

<!-- Just Time -->
<p>Office opens at <time datetime="09:00">9:00 AM</time></p>

<!-- Duration -->
<p>Course duration: <time datetime="PT2H30M">2 hours 30 minutes</time></p>
```

| Format | Meaning | Example |
|--------|---------|---------|
| `YYYY-MM-DD` | Date | `2025-01-15` |
| `HH:MM` | Time | `14:30` |
| `YYYY-MM-DDTHH:MM` | Date + Time | `2025-01-15T14:30` |
| `PTXHXM` | Duration | `PT2H30M` (2 hours 30 minutes) |

> 📌 The `datetime` attribute is **machine-readable** so search engines and browsers can understand the exact date/time.

---

## 7.11 HTML Tables — Structured Data

Tables display **data in rows and columns**. They are perfect for structured, tabular data.

### Basic Table Structure:

```html
<table>
    <tr>
        <th>Header 1</th>
        <th>Header 2</th>
        <th>Header 3</th>
    </tr>
    <tr>
        <td>Row 1, Cell 1</td>
        <td>Row 1, Cell 2</td>
        <td>Row 1, Cell 3</td>
    </tr>
    <tr>
        <td>Row 2, Cell 1</td>
        <td>Row 2, Cell 2</td>
        <td>Row 2, Cell 3</td>
    </tr>
</table>
```

### Table Tags Explained:

| Tag | Full Name | Purpose |
|-----|-----------|---------|
| `<table>` | Table | Container for the entire table |
| `<tr>` | Table Row | Defines a row |
| `<th>` | Table Header | Header cell (bold + centered by default) |
| `<td>` | Table Data | Regular data cell |
| `<thead>` | Table Head | Groups header rows |
| `<tbody>` | Table Body | Groups body rows |
| `<tfoot>` | Table Foot | Groups footer rows |
| `<caption>` | Caption | Title/description for the table |

### Complete Table Example:

```html
<table>
    <caption>Student Exam Results — Spring 2025</caption>

    <thead>
        <tr>
            <th>Name</th>
            <th>Subject</th>
            <th>Score</th>
            <th>Grade</th>
        </tr>
    </thead>

    <tbody>
        <tr>
            <td>Ali Khan</td>
            <td>HTML</td>
            <td>95</td>
            <td>A+</td>
        </tr>
        <tr>
            <td>Sara Ahmed</td>
            <td>HTML</td>
            <td>88</td>
            <td>A</td>
        </tr>
        <tr>
            <td>Omar Farooq</td>
            <td>HTML</td>
            <td>72</td>
            <td>B</td>
        </tr>
        <tr>
            <td>Fatima Noor</td>
            <td>HTML</td>
            <td>91</td>
            <td>A</td>
        </tr>
    </tbody>

    <tfoot>
        <tr>
            <td>Average</td>
            <td>—</td>
            <td>86.5</td>
            <td>A</td>
        </tr>
    </tfoot>
</table>
```

### Display:

```
Student Exam Results — Spring 2025

| Name         | Subject | Score | Grade |
|------------- |---------|-------|-------|
| Ali Khan     | HTML    | 95    | A+    |
| Sara Ahmed   | HTML    | 88    | A     |
| Omar Farooq  | HTML    | 72    | B     |
| Fatima Noor  | HTML    | 91    | A     |
|------------- |---------|-------|-------|
| Average      | —       | 86.5  | A     |
```

### Spanning Columns and Rows:

#### `colspan` — Merge Cells Horizontally:

```html
<table>
    <tr>
        <th colspan="3">Student Information</th>
    </tr>
    <tr>
        <th>Name</th>
        <th>Age</th>
        <th>City</th>
    </tr>
    <tr>
        <td>Ali Khan</td>
        <td>22</td>
        <td>Lahore</td>
    </tr>
</table>
```

```
|     Student Information      |   ← spans 3 columns
| Name      | Age  | City     |
| Ali Khan  | 22   | Lahore   |
```

#### `rowspan` — Merge Cells Vertically:

```html
<table>
    <tr>
        <th>Name</th>
        <th>Subject</th>
        <th>Score</th>
    </tr>
    <tr>
        <td rowspan="2">Ali Khan</td>
        <td>HTML</td>
        <td>95</td>
    </tr>
    <tr>
        <td>CSS</td>
        <td>88</td>
    </tr>
</table>
```

```
| Name      | Subject | Score |
| Ali Khan  | HTML    | 95    |   ← "Ali Khan" spans 2 rows
|           | CSS     | 88    |
```

### ⚠️ Tables are NOT for Layout:

```html
<!-- ❌ WRONG: Using tables to create page layout -->
<table>
    <tr>
        <td>Navigation here</td>
        <td>Main content here</td>
        <td>Sidebar here</td>
    </tr>
</table>

<!-- ✅ RIGHT: Use semantic tags + CSS for layout -->
<nav>Navigation here</nav>
<main>Main content here</main>
<aside>Sidebar here</aside>
```

> 📌 **Rule:** Tables are for **DATA only**. Never use tables for page layout. That's what CSS Flexbox and Grid are for.

---

## 7.12 HTML Forms — User Input

Forms allow users to **input data** and send it to a server. They are essential for logins, sign-ups, search bars, contact forms, and more.

### Basic Form Structure:

```html
<form action="/submit" method="POST">
    <label for="name">Name:</label>
    <input type="text" id="name" name="name">

    <button type="submit">Submit</button>
</form>
```

### Form Tags & Attributes:

| Tag/Attribute | Purpose |
|---------------|---------|
| `<form>` | Container for all form elements |
| `action` | URL where form data is sent |
| `method` | HTTP method: `GET` or `POST` |
| `<input>` | Input field (text, email, password, etc.) |
| `<label>` | Text label for an input field |
| `<button>` | Clickable button |
| `<textarea>` | Multi-line text input |
| `<select>` | Dropdown menu |
| `<option>` | Individual option in a dropdown |

### The `<label>` and `for` Attribute:

```html
<!-- ✅ CORRECT: Label linked to input via "for" and "id" -->
<label for="email">Email Address:</label>
<input type="text" id="email" name="email">

<!-- When user clicks the LABEL, the input gets focused! -->
```

| Label Feature | Benefit |
|---------------|---------|
| **Clicking label** | Focuses/activates the associated input |
| **Accessibility** | Screen readers read the label with the input |
| **Required** | Every input MUST have a label |

> 📌 **Rule:** The `for` attribute in `<label>` must match the `id` attribute in `<input>`. This links them together.

---

## 7.13 Input Types — The Complete Guide

HTML5 provides many input types for different kinds of data:

### Text Inputs:

```html
<!-- Simple text -->
<label for="username">Username:</label>
<input type="text" id="username" name="username" placeholder="Enter your username">

<!-- Email (validates email format) -->
<label for="email">Email:</label>
<input type="email" id="email" name="email" placeholder="you@example.com">

<!-- Password (hides characters) -->
<label for="password">Password:</label>
<input type="password" id="password" name="password" placeholder="Enter password">

<!-- Phone number -->
<label for="phone">Phone:</label>
<input type="tel" id="phone" name="phone" placeholder="+92-300-1234567">

<!-- URL -->
<label for="website">Website:</label>
<input type="url" id="website" name="website" placeholder="https://example.com">

<!-- Search -->
<label for="search">Search:</label>
<input type="search" id="search" name="search" placeholder="Search...">
```

### Number & Date Inputs:

```html
<!-- Number -->
<label for="age">Age:</label>
<input type="number" id="age" name="age" min="1" max="120">

<!-- Range (slider) -->
<label for="volume">Volume:</label>
<input type="range" id="volume" name="volume" min="0" max="100">

<!-- Date -->
<label for="birthday">Birthday:</label>
<input type="date" id="birthday" name="birthday">

<!-- Time -->
<label for="appointment">Appointment Time:</label>
<input type="time" id="appointment" name="appointment">
```

### Selection Inputs:

```html
<!-- Checkbox (multiple selections) -->
<p>Select your skills:</p>
<input type="checkbox" id="html" name="skills" value="html">
<label for="html">HTML</label><br>

<input type="checkbox" id="css" name="skills" value="css">
<label for="css">CSS</label><br>

<input type="checkbox" id="js" name="skills" value="js">
<label for="js">JavaScript</label><br>


<!-- Radio buttons (single selection) -->
<p>Select your experience level:</p>
<input type="radio" id="beginner" name="level" value="beginner">
<label for="beginner">Beginner</label><br>

<input type="radio" id="intermediate" name="level" value="intermediate">
<label for="intermediate">Intermediate</label><br>

<input type="radio" id="advanced" name="level" value="advanced">
<label for="advanced">Advanced</label><br>
```

### Other Inputs:

```html
<!-- Color picker -->
<label for="color">Favorite Color:</label>
<input type="color" id="color" name="color" value="#ff0000">

<!-- File upload -->
<label for="resume">Upload Resume:</label>
<input type="file" id="resume" name="resume">

<!-- Hidden field (not visible to user) -->
<input type="hidden" name="form_id" value="contact_form_v2">
```

### Complete Input Types Table:

| Type | Purpose | Example |
|------|---------|---------|
| `text` | General text | Username, name |
| `email` | Email with validation | user@email.com |
| `password` | Hidden characters | Login password |
| `tel` | Phone number | +92-300-1234567 |
| `url` | Website URL | https://example.com |
| `number` | Numeric value | Age, quantity |
| `range` | Slider | Volume, rating |
| `date` | Date picker | Birthday |
| `time` | Time picker | Appointment |
| `checkbox` | Multiple selection | Skills, preferences |
| `radio` | Single selection | Gender, experience level |
| `color` | Color picker | Theme color |
| `file` | File upload | Resume, photo |
| `search` | Search field | Site search |
| `hidden` | Invisible field | Form tracking |

---

## 7.14 Textarea, Select & Button

### `<textarea>` — Multi-Line Text Input:

```html
<label for="message">Your Message:</label><br>
<textarea id="message" name="message" rows="5" cols="40" 
          placeholder="Type your message here..."></textarea>
```

| Attribute | Purpose |
|-----------|---------|
| `rows` | Number of visible text lines |
| `cols` | Visible width in characters |
| `placeholder` | Hint text shown before user types |

### `<select>` — Dropdown Menu:

```html
<label for="country">Country:</label>
<select id="country" name="country">
    <option value="">-- Select Country --</option>
    <option value="pk">Pakistan</option>
    <option value="in">India</option>
    <option value="us">United States</option>
    <option value="uk">United Kingdom</option>
    <option value="ae">UAE</option>
</select>
```

### Grouped Options with `<optgroup>`:

```html
<label for="course">Select Course:</label>
<select id="course" name="course">
    <optgroup label="Frontend">
        <option value="html">HTML</option>
        <option value="css">CSS</option>
        <option value="js">JavaScript</option>
    </optgroup>
    <optgroup label="Backend">
        <option value="python">Python</option>
        <option value="node">Node.js</option>
        <option value="php">PHP</option>
    </optgroup>
</select>
```

### `<button>` — Submit & Action Buttons:

```html
<!-- Submit button (sends form data) -->
<button type="submit">Submit Form</button>

<!-- Reset button (clears all fields) -->
<button type="reset">Clear Form</button>

<!-- Regular button (no default action) -->
<button type="button">Click Me</button>
```

---

## 7.15 Form Validation — Built-in HTML5 Validation

HTML5 includes **built-in validation** — no JavaScript needed for basic checks!

### Validation Attributes:

```html
<!-- Required field (cannot be empty) -->
<input type="text" name="name" required>

<!-- Minimum and maximum length -->
<input type="text" name="username" minlength="3" maxlength="20" required>

<!-- Minimum and maximum value (for numbers) -->
<input type="number" name="age" min="18" max="100" required>

<!-- Pattern (regex) for custom validation -->
<input type="text" name="zipcode" pattern="[0-9]{5}" 
       title="Please enter a 5-digit zip code" required>

<!-- Email validation (built-in) -->
<input type="email" name="email" required>

<!-- URL validation (built-in) -->
<input type="url" name="website" required>
```

### Validation Attributes Table:

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `required` | Field must be filled | `<input required>` |
| `minlength` | Minimum characters | `minlength="3"` |
| `maxlength` | Maximum characters | `maxlength="100"` |
| `min` | Minimum numeric value | `min="18"` |
| `max` | Maximum numeric value | `max="100"` |
| `pattern` | Regex pattern match | `pattern="[A-Za-z]+"` |
| `placeholder` | Hint text in field | `placeholder="Enter name"` |
| `readonly` | User cannot edit | `<input readonly>` |
| `disabled` | Field is inactive | `<input disabled>` |

---

## 7.16 Complete Contact Form Example

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Contact Us - My Website</title>
</head>
<body>

    <header>
        <h1>Contact Us</h1>
        <p>Fill out the form below and we will get back to you shortly.</p>
    </header>

    <main>
        <form action="/submit-contact" method="POST">

            <!-- Name Field -->
            <div>
                <label for="fullname">Full Name:</label><br>
                <input type="text" id="fullname" name="fullname" 
                       placeholder="Enter your full name" 
                       minlength="2" maxlength="50" required>
            </div>

            <br>

            <!-- Email Field -->
            <div>
                <label for="email">Email Address:</label><br>
                <input type="email" id="email" name="email" 
                       placeholder="you@example.com" required>
            </div>

            <br>

            <!-- Phone Field -->
            <div>
                <label for="phone">Phone Number:</label><br>
                <input type="tel" id="phone" name="phone" 
                       placeholder="+92-300-1234567">
            </div>

            <br>

            <!-- Subject Dropdown -->
            <div>
                <label for="subject">Subject:</label><br>
                <select id="subject" name="subject" required>
                    <option value="">-- Select Subject --</option>
                    <option value="general">General Inquiry</option>
                    <option value="support">Technical Support</option>
                    <option value="feedback">Feedback</option>
                    <option value="business">Business Proposal</option>
                </select>
            </div>

            <br>

            <!-- Priority Radio Buttons -->
            <div>
                <p>Priority Level:</p>
                <input type="radio" id="low" name="priority" value="low">
                <label for="low">Low</label><br>

                <input type="radio" id="medium" name="priority" value="medium" checked>
                <label for="medium">Medium</label><br>

                <input type="radio" id="high" name="priority" value="high">
                <label for="high">High</label>
            </div>

            <br>

            <!-- Message Textarea -->
            <div>
                <label for="message">Your Message:</label><br>
                <textarea id="message" name="message" rows="6" cols="50" 
                          placeholder="Type your message here..." 
                          minlength="10" required></textarea>
            </div>

            <br>

            <!-- Newsletter Checkbox -->
            <div>
                <input type="checkbox" id="newsletter" name="newsletter" value="yes">
                <label for="newsletter">Subscribe to our newsletter</label>
            </div>

            <br>

            <!-- Terms Checkbox (Required) -->
            <div>
                <input type="checkbox" id="terms" name="terms" required>
                <label for="terms">I agree to the 
                    <a href="terms.html" target="_blank" rel="noopener noreferrer">
                        Terms and Conditions
                    </a>
                </label>
            </div>

            <br>

            <!-- Buttons -->
            <div>
                <button type="submit">Send Message</button>
                <button type="reset">Clear Form</button>
            </div>

        </form>
    </main>

    <footer>
        <p><small>&copy; 2025 My Website. All rights reserved.</small></p>
    </footer>

</body>
</html>
```

---

## 7.17 `<fieldset>` and `<legend>` — Grouping Form Fields

The `<fieldset>` groups related form fields, and `<legend>` provides a title for that group.

```html
<form action="/register" method="POST">

    <fieldset>
        <legend>Personal Information</legend>

        <label for="fname">First Name:</label><br>
        <input type="text" id="fname" name="fname" required><br><br>

        <label for="lname">Last Name:</label><br>
        <input type="text" id="lname" name="lname" required><br><br>

        <label for="dob">Date of Birth:</label><br>
        <input type="date" id="dob" name="dob" required>
    </fieldset>

    <br>

    <fieldset>
        <legend>Account Details</legend>

        <label for="user">Username:</label><br>
        <input type="text" id="user" name="user" minlength="3" required><br><br>

        <label for="pass">Password:</label><br>
        <input type="password" id="pass" name="pass" minlength="8" required><br><br>

        <label for="confirm">Confirm Password:</label><br>
        <input type="password" id="confirm" name="confirm" minlength="8" required>
    </fieldset>

    <br>

    <button type="submit">Register</button>

</form>
```

### Display:

```
┌─ Personal Information ──────────────────┐
│  First Name: [_______________]          │
│  Last Name:  [_______________]          │
│  Date of Birth: [_______________]       │
└─────────────────────────────────────────┘

┌─ Account Details ───────────────────────┐
│  Username:         [_______________]    │
│  Password:         [_______________]    │
│  Confirm Password: [_______________]    │
└─────────────────────────────────────────┘

[ Register ]
```

---

## 7.18 Accessibility & SEO Basics

### Accessibility (a11y):

Accessibility means making your website usable by **everyone**, including people with disabilities.

| Practice | How To |
|----------|--------|
| **Always use `alt` on images** | Describes images for screen readers |
| **Use semantic tags** | `<nav>`, `<main>`, `<header>` help navigation |
| **Label all form inputs** | `<label for="id">` links labels to inputs |
| **Use proper heading hierarchy** | `h1 → h2 → h3` (never skip levels) |
| **Use `lang` attribute** | `<html lang="en">` for correct pronunciation |
| **Don't rely on color alone** | Use text + color for messages |
| **Make links descriptive** | "Read our about page" not "Click here" |

### SEO (Search Engine Optimization):

SEO means writing HTML that **search engines understand and rank well**.

| Practice | How To |
|----------|--------|
| **Use one `<h1>` per page** | Main topic of the page |
| **Write descriptive `<title>`** | Shows in search results |
| **Add `<meta description>`** | Summary shown in Google |
| **Use semantic tags** | Google understands page structure |
| **Write meaningful alt text** | Google indexes image content |
| **Use `<time>` for dates** | Google understands publication dates |
| **Internal linking** | Connect related pages together |
| **Clean URL structure** | `about.html` not `page2.html` |

---

## 7.19 Semantic Tags Complete Reference

| Tag | Purpose | Can Have Multiple? |
|-----|---------|-------------------|
| `<header>` | Introductory content / banner | ✅ Yes |
| `<nav>` | Major navigation links | ✅ Yes |
| `<main>` | Primary unique page content | ❌ Only ONE |
| `<section>` | Thematic content grouping | ✅ Yes |
| `<article>` | Self-contained independent content | ✅ Yes |
| `<aside>` | Side/supplementary content | ✅ Yes |
| `<footer>` | Closing content / credits | ✅ Yes |
| `<figure>` | Image/diagram with caption | ✅ Yes |
| `<figcaption>` | Caption for `<figure>` | One per `<figure>` |
| `<time>` | Machine-readable date/time | ✅ Yes |
| `<mark>` | Highlighted text | ✅ Yes |
| `<details>` | Expandable content | ✅ Yes |
| `<summary>` | Visible heading for `<details>` | One per `<details>` |

---

## 7.20 Summary & Key Takeaways

| Concept | Key Point |
|---------|-----------|
| Semantic HTML | Tags that describe their content's purpose |
| `<header>` | Introductory content for page or section |
| `<nav>` | Major navigation links |
| `<main>` | Primary unique content (only ONE per page) |
| `<section>` | Thematic grouping with a heading |
| `<article>` | Self-contained, shareable content |
| `<aside>` | Supplementary/sidebar content |
| `<footer>` | Closing content, credits, links |
| Tables | For structured data ONLY — never for layout |
| `<thead>`, `<tbody>`, `<tfoot>` | Semantic table sections |
| Forms | User input with `<form>`, `<input>`, `<label>` |
| `<label>` + `for` | Links labels to inputs for accessibility |
| HTML5 Validation | `required`, `minlength`, `pattern`, etc. |
| `<fieldset>` + `<legend>` | Group and title related form fields |
| Accessibility | Semantic tags, alt text, labels, heading hierarchy |
| SEO | Title, description, headings, semantic structure |

---

> 🎉 **Congratulations!** You've completed Chapter 7. You now know how to build modern, semantic HTML pages with proper layout tags, functional forms with validation, data tables, and accessibility best practices!

---