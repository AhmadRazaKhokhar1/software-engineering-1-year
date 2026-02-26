

# 📘 Chapter 8: Multi-Page Websites, Best Practices & Capstone

> **🔴 HTML Mastery & Wrap-Up — Building a real multi-page website, learning professional standards, avoiding common mistakes, and preparing your HTML for CSS.**

---

## 8.1 Why This Chapter Matters

You've learned all the **individual HTML elements**. Now it's time to put everything together into a **real, professional, multi-page website**.

### What You'll Master in This Chapter:

```
✅ Professional folder and file structure
✅ Linking multiple pages together seamlessly
✅ HTML rules and coding standards
✅ Common mistakes and how to avoid them
✅ Writing clean, maintainable HTML
✅ Building a complete HTML-only capstone website
✅ Preparing your HTML for CSS styling
```

> 🧠 **Think of it this way:** Chapters 1-7 taught you individual instruments. Chapter 8 teaches you to play them together as a full orchestra.

---

## 8.2 Professional Folder & File Structure

A well-organized project structure is the **hallmark of a professional developer**. It makes your code maintainable, scalable, and easy to navigate.

### ❌ Bad Project Structure:

```
my-site/
├── index.html
├── about.html
├── contact.html
├── image1.jpg
├── image2.jpg
├── image3.png
├── logo.png
├── icon1.png
├── icon2.png
├── style.css
├── script.js
├── post1.html
├── post2.html
├── services.html
└── photo-of-team-at-office.jpg

(Everything dumped in one folder — chaos!)
```

### ✅ Professional Project Structure:

```
my-website/
│
├── index.html                    ← Homepage (root level)
├── about.html                    ← About page
├── contact.html                  ← Contact page
│
├── pages/                        ← Additional pages
│   ├── services.html
│   ├── portfolio.html
│   └── faq.html
│
├── blog/                         ← Blog posts
│   ├── index.html                ← Blog listing page
│   ├── html-basics.html
│   ├── css-introduction.html
│   └── javascript-fundamentals.html
│
├── images/                       ← All images
│   ├── logo.png
│   ├── hero-banner.jpg
│   ├── favicon.ico
│   ├── team/
│   │   ├── member-ali.jpg
│   │   ├── member-sara.jpg
│   │   └── member-omar.jpg
│   ├── blog/
│   │   ├── html-thumbnail.jpg
│   │   ├── css-thumbnail.jpg
│   │   └── js-thumbnail.jpg
│   └── icons/
│       ├── email.png
│       ├── phone.png
│       ├── twitter.png
│       └── github.png
│
├── css/                          ← Stylesheets (for future CSS)
│   └── style.css
│
└── js/                           ← JavaScript (for future JS)
    └── script.js
```

### Folder Structure Rules:

| Rule | Explanation |
|------|-------------|
| `index.html` at root | Homepage; servers look for this by default |
| Separate `images/` folder | All images organized in one place |
| Subfolder for categories | `images/team/`, `images/blog/`, `images/icons/` |
| Separate `pages/` folder | Keep non-essential pages organized |
| Separate `blog/` folder | Blog posts in their own directory |
| `css/` and `js/` folders | Ready for future CSS and JavaScript |
| Lowercase file names | `about.html` not `About.HTML` |
| Hyphens for spaces | `html-basics.html` not `html basics.html` |

---

## 8.3 File Naming Conventions

### ✅ Good File Names:

```
index.html
about.html
contact.html
services.html
html-basics.html
css-introduction.html
team-photo.jpg
hero-banner.jpg
logo-dark.png
email-icon.svg
```

### ❌ Bad File Names:

```
About Us.html           ← Spaces in file name
My Page (2).html        ← Spaces and special characters
INDEX.HTML              ← Uppercase letters
page1.html              ← Non-descriptive
IMG_20240115_001.jpg    ← Camera default name
final_FINAL_v2.html    ← Unprofessional naming
résumé.html             ← Special characters (accents)
```

### Complete Naming Rules:

| Rule | Good ✅ | Bad ❌ |
|------|---------|--------|
| All lowercase | `about.html` | `About.HTML` |
| Hyphens for spaces | `our-team.html` | `our team.html` |
| Descriptive names | `contact.html` | `page3.html` |
| No special characters | `services.html` | `services!.html` |
| Short but clear | `faq.html` | `frequently-asked-questions-page.html` |
| Consistent pattern | `post-1.html`, `post-2.html` | `post1.html`, `second_post.html` |

---

## 8.4 Linking Large Sites — Navigation Architecture

### Understanding Page Relationships:

```
                    ┌─────────────┐
                    │  index.html │ ← Homepage
                    │   (Home)    │
                    └──────┬──────┘
           ┌───────────────┼───────────────┐
           │               │               │
    ┌──────┴──────┐ ┌──────┴──────┐ ┌──────┴──────┐
    │ about.html  │ │services.html│ │contact.html │
    │  (About)    │ │ (Services)  │ │ (Contact)   │
    └─────────────┘ └──────┬──────┘ └─────────────┘
                           │
                    ┌──────┴──────┐
                    │  blog/      │
                    │ index.html  │ ← Blog listing
                    └──────┬──────┘
              ┌────────────┼────────────┐
              │            │            │
       ┌──────┴─────┐┌────┴─────┐┌─────┴──────┐
       │  post-1    ││ post-2   ││  post-3    │
       │  .html     ││ .html    ││  .html     │
       └────────────┘└──────────┘└────────────┘
```

### Consistent Navigation Across All Pages:

The key to a well-linked website is **consistent navigation** that appears on **every page**:

```html
<!-- This navigation block appears on EVERY page -->
<header>
    <a href="/index.html">
        <img src="/images/logo.png" alt="My Website - Home" width="150" height="45">
    </a>
    <nav>
        <ul>
            <li><a href="/index.html">Home</a></li>
            <li><a href="/about.html">About</a></li>
            <li><a href="/pages/services.html">Services</a></li>
            <li><a href="/blog/index.html">Blog</a></li>
            <li><a href="/contact.html">Contact</a></li>
        </ul>
    </nav>
</header>
```

### Path Reference Guide for Different Pages:

#### From `index.html` (root level):

```html
<a href="about.html">About</a>
<a href="contact.html">Contact</a>
<a href="pages/services.html">Services</a>
<a href="blog/index.html">Blog</a>
<img src="images/logo.png" alt="Logo">
```

#### From `pages/services.html` (one level deep):

```html
<a href="../index.html">Home</a>
<a href="../about.html">About</a>
<a href="services.html">Services</a>         <!-- Same folder -->
<a href="../blog/index.html">Blog</a>
<a href="../contact.html">Contact</a>
<img src="../images/logo.png" alt="Logo">
```

#### From `blog/html-basics.html` (one level deep):

```html
<a href="../index.html">Home</a>
<a href="../about.html">About</a>
<a href="../pages/services.html">Services</a>
<a href="index.html">Blog</a>                <!-- Same folder -->
<a href="../contact.html">Contact</a>
<img src="../images/logo.png" alt="Logo">
```

### Quick Reference — Path Symbols:

```
Same folder:        href="file.html"
Subfolder:          href="folder/file.html"
Up one level:       href="../file.html"
Up then into:       href="../folder/file.html"
Up two levels:      href="../../file.html"
```

---

## 8.5 HTML Rules & Coding Standards

These are the **professional rules** every HTML developer should follow:

### Rule 1: Always Include the Complete Document Structure

```html
<!-- ✅ Every HTML file MUST have this structure -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Descriptive Page Title</title>
</head>
<body>
    <!-- Content here -->
</body>
</html>
```

### Rule 2: One `<h1>` Per Page

```html
<!-- ✅ CORRECT -->
<h1>About Our Company</h1>
<h2>Our Mission</h2>
<h2>Our Team</h2>

<!-- ❌ WRONG -->
<h1>About Our Company</h1>
<h1>Our Mission</h1>
<h1>Our Team</h1>
```

### Rule 3: Sequential Heading Hierarchy

```html
<!-- ✅ CORRECT: h1 → h2 → h3 (no skipping) -->
<h1>Title</h1>
<h2>Section</h2>
<h3>Sub-section</h3>

<!-- ❌ WRONG: Skipping levels -->
<h1>Title</h1>
<h4>Sub-section</h4>
```

### Rule 4: Always Close Tags Properly

```html
<!-- ✅ CORRECT -->
<p>This paragraph is properly closed.</p>
<div>
    <p>Nested and closed correctly.</p>
</div>

<!-- ❌ WRONG -->
<p>This paragraph is never closed.
<div>
    <p>Bad nesting.
</div></p>
```

### Rule 5: Proper Nesting (Last Opened = First Closed)

```html
<!-- ✅ CORRECT -->
<p>This is <strong><em>bold and italic</em></strong> text.</p>

<!-- ❌ WRONG -->
<p>This is <strong><em>bold and italic</strong></em> text.</p>
```

### Rule 6: Always Use `alt` on Images

```html
<!-- ✅ CORRECT -->
<img src="photo.jpg" alt="Team meeting in the conference room" width="600" height="400">

<!-- ❌ WRONG -->
<img src="photo.jpg">
```

### Rule 7: Always Link Labels to Inputs

```html
<!-- ✅ CORRECT -->
<label for="email">Email:</label>
<input type="email" id="email" name="email">

<!-- ❌ WRONG -->
Email: <input type="email" name="email">
```

### Rule 8: Use Semantic Tags Over `<div>` When Possible

```html
<!-- ✅ CORRECT -->
<header>...</header>
<nav>...</nav>
<main>...</main>
<footer>...</footer>

<!-- ❌ WRONG -->
<div>Header stuff</div>
<div>Nav links</div>
<div>Main content</div>
<div>Footer stuff</div>
```

### Rule 9: Consistent Indentation

```html
<!-- ✅ CORRECT: 4-space indentation, consistent -->
<main>
    <section>
        <h2>Title</h2>
        <p>Content here.</p>
    </section>
</main>

<!-- ❌ WRONG: Random indentation -->
<main>
<section>
        <h2>Title</h2>
    <p>Content here.</p>
        </section>
    </main>
```

### Rule 10: Descriptive Link Text

```html
<!-- ✅ CORRECT -->
<a href="about.html">Learn more about our company</a>

<!-- ❌ WRONG -->
<a href="about.html">Click here</a>
```

---

## 8.6 Common HTML Mistakes — The Complete List

### Mistake 1: Forgetting `<!DOCTYPE html>`

```html
<!-- ❌ Missing DOCTYPE → browser enters Quirks Mode -->
<html>
<head><title>Page</title></head>
<body><p>Content</p></body>
</html>

<!-- ✅ Always start with DOCTYPE -->
<!DOCTYPE html>
<html lang="en">
<head><meta charset="UTF-8"><title>Page</title></head>
<body><p>Content</p></body>
</html>
```

### Mistake 2: Using `<br>` for Spacing

```html
<!-- ❌ WRONG: Stacking <br> tags for visual space -->
<p>Paragraph one</p>
<br>
<br>
<br>
<p>Paragraph two</p>

<!-- ✅ RIGHT: CSS handles spacing -->
<p>Paragraph one</p>
<p>Paragraph two</p>
<!-- Use CSS: p { margin-bottom: 30px; } -->
```

### Mistake 3: Using Headings for Styling

```html
<!-- ❌ WRONG: Using <h4> because you want smaller text -->
<h4>This is actually just a regular note</h4>

<!-- ✅ RIGHT: Use <p> with CSS for sizing -->
<p>This is actually just a regular note</p>
<!-- Use CSS: p.note { font-size: 14px; } -->
```

### Mistake 4: Inline Elements Containing Block Elements

```html
<!-- ❌ WRONG: <a> (inline) wrapping <h2> (block) — historically invalid -->
<a href="page.html">
    <div>
        <h2>Title</h2>
        <p>Description</p>
    </div>
</a>

<!-- ✅ Better: Link inside the content -->
<div>
    <h2><a href="page.html">Title</a></h2>
    <p>Description</p>
</div>
```

### Mistake 5: Duplicate `id` Attributes

```html
<!-- ❌ WRONG: Same id used twice (ids must be UNIQUE) -->
<h2 id="section">About Us</h2>
<h2 id="section">Our Services</h2>

<!-- ✅ CORRECT: Each id is unique -->
<h2 id="about">About Us</h2>
<h2 id="services">Our Services</h2>
```

### Mistake 6: Missing Form Labels

```html
<!-- ❌ WRONG: No label — screen readers can't identify the field -->
Name: <input type="text" name="name">

<!-- ✅ CORRECT: Proper label linked to input -->
<label for="name">Name:</label>
<input type="text" id="name" name="name">
```

### Mistake 7: Tables for Layout

```html
<!-- ❌ WRONG: Using tables to create page layout -->
<table>
    <tr>
        <td>Sidebar</td>
        <td>Main Content</td>
    </tr>
</table>

<!-- ✅ RIGHT: Use semantic tags + CSS -->
<aside>Sidebar</aside>
<main>Main Content</main>
```

### Mistake 8: Forgetting `<meta charset="UTF-8">`

```html
<!-- ❌ Without charset, special characters may break -->
<!-- Characters like é, ñ, ü, ★, ❤ may show as ???  -->

<!-- ✅ Always include charset as FIRST element in <head> -->
<head>
    <meta charset="UTF-8">
    <!-- other meta tags... -->
</head>
```

### Mistake 9: Not Validating Your HTML

```html
<!-- ❌ Writing code and never checking for errors -->
<!-- You might have unclosed tags, wrong nesting, or missing attributes -->

<!-- ✅ Always validate at https://validator.w3.org -->
<!-- Paste your code → Check → Fix all errors and warnings -->
```

### Mistake 10: Writing Non-Semantic "Div Soup"

```html
<!-- ❌ "Div Soup" — meaningless divs everywhere -->
<div>
    <div>
        <div>
            <div>Logo</div>
            <div>Navigation</div>
        </div>
    </div>
    <div>
        <div>
            <div>Content</div>
        </div>
    </div>
</div>

<!-- ✅ Clean semantic HTML -->
<header>
    <img src="logo.png" alt="Logo" width="100" height="30">
    <nav>
        <ul>
            <li><a href="/">Home</a></li>
        </ul>
    </nav>
</header>
<main>
    <p>Content</p>
</main>
```

---

## 8.7 Clean, Maintainable HTML — Best Practices Checklist

### The Complete HTML Quality Checklist:

```
DOCUMENT STRUCTURE
☐ <!DOCTYPE html> present
☐ <html lang="en"> with language attribute
☐ <meta charset="UTF-8"> as first element in <head>
☐ <meta viewport> for mobile responsiveness
☐ Descriptive, unique <title> on every page
☐ <meta description> for SEO

CONTENT STRUCTURE
☐ Only ONE <h1> per page
☐ Heading hierarchy: h1 → h2 → h3 (no skipping)
☐ All text wrapped in appropriate tags (<p>, <h2>, <li>, etc.)
☐ No naked text outside of tags

SEMANTIC HTML
☐ <header> for introductory content
☐ <nav> for navigation
☐ <main> for primary content (only one)
☐ <section> for thematic groups
☐ <article> for independent content
☐ <aside> for supplementary content
☐ <footer> for closing content
☐ Minimal use of <div> (only when semantic tags don't fit)

IMAGES
☐ All images have descriptive alt text
☐ width and height attributes set
☐ Images stored in organized /images/ folder
☐ Descriptive, lowercase, hyphenated file names

LINKS
☐ Descriptive link text (never "click here")
☐ Relative URLs for internal links
☐ Absolute URLs for external links
☐ target="_blank" + rel="noopener noreferrer" for external links

FORMS
☐ Every input has a linked <label>
☐ Required fields marked with required attribute
☐ Appropriate input types used (email, tel, date, etc.)
☐ Form has action and method attributes

CODE QUALITY
☐ Consistent indentation (2 or 4 spaces)
☐ Helpful comments for sections
☐ Proper tag nesting (last opened = first closed)
☐ All tags properly closed
☐ No duplicate id attributes
☐ Code validated at validator.w3.org
```

---

## 8.8 Preparing HTML for CSS

Your HTML is the **foundation** that CSS will style. Writing CSS-ready HTML means thinking ahead about structure.

### How CSS Targets HTML:

```
CSS targets HTML elements in three main ways:

1. By TAG NAME:     p { color: blue; }         → All <p> elements
2. By CLASS:        .card { border: 1px; }     → Elements with class="card"
3. By ID:           #hero { height: 100vh; }   → Element with id="hero"
```

### Adding `class` and `id` for CSS Preparation:

```html
<!-- Classes: Reusable — same class on MULTIPLE elements -->
<div class="card">
    <h3 class="card-title">Web Design</h3>
    <p class="card-text">Beautiful designs for your brand.</p>
</div>

<div class="card">
    <h3 class="card-title">Development</h3>
    <p class="card-text">Clean, efficient code.</p>
</div>

<!-- IDs: Unique — only ONE element with this id -->
<section id="hero">
    <h1>Welcome to Our Website</h1>
</section>

<section id="services">
    <h2>Our Services</h2>
</section>
```

### Class vs ID:

| Feature | `class` | `id` |
|---------|---------|------|
| **Uniqueness** | Can be used on MULTIPLE elements | Must be UNIQUE (one per page) |
| **CSS specificity** | Lower | Higher |
| **Purpose** | Style groups of similar elements | Target ONE specific element |
| **Naming** | `.card`, `.btn`, `.nav-link` | `#hero`, `#main-content` |

### Class Naming Conventions:

```html
<!-- ✅ GOOD class names: descriptive, lowercase, hyphenated -->
<div class="hero-section">...</div>
<div class="card">...</div>
<div class="card-image">...</div>
<nav class="main-nav">...</nav>
<ul class="nav-links">...</ul>
<p class="text-highlight">...</p>
<button class="btn-primary">...</button>
<div class="footer-content">...</div>

<!-- ❌ BAD class names -->
<div class="div1">...</div>           <!-- Not descriptive -->
<div class="blue-text">...</div>      <!-- Describes style, not purpose -->
<div class="myDiv">...</div>          <!-- camelCase, not hyphenated -->
<div class="a">...</div>              <!-- Meaningless -->
<div class="left-sidebar">...</div>   <!-- Position-based, not purpose-based -->
```

### CSS-Ready HTML Structure Example:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS-Ready Page</title>
    <!-- Future CSS link -->
    <!-- <link rel="stylesheet" href="css/style.css"> -->
</head>
<body>

    <!-- Header with classes for CSS targeting -->
    <header class="site-header">
        <div class="header-content">
            <a href="index.html" class="logo-link">
                <img src="images/logo.png" alt="MyBrand Home" 
                     class="logo" width="150" height="45">
            </a>
            <nav class="main-nav">
                <ul class="nav-list">
                    <li class="nav-item"><a href="index.html" class="nav-link">Home</a></li>
                    <li class="nav-item"><a href="about.html" class="nav-link">About</a></li>
                    <li class="nav-item"><a href="contact.html" class="nav-link">Contact</a></li>
                </ul>
            </nav>
        </div>
    </header>

    <!-- Hero section -->
    <section id="hero" class="hero-section">
        <h1 class="hero-title">Build Amazing Websites</h1>
        <p class="hero-subtitle">Learn web development from scratch</p>
        <a href="pages/services.html" class="btn btn-primary">Get Started</a>
    </section>

    <!-- Main content -->
    <main class="main-content">

        <section id="services" class="services-section">
            <h2 class="section-title">Our Services</h2>

            <div class="card-grid">
                <article class="card">
                    <img src="images/design.jpg" alt="Web design service" 
                         class="card-image" width="350" height="200">
                    <h3 class="card-title">Web Design</h3>
                    <p class="card-text">Beautiful, modern designs.</p>
                    <a href="#" class="card-link">Learn More →</a>
                </article>

                <article class="card">
                    <img src="images/develop.jpg" alt="Web development service" 
                         class="card-image" width="350" height="200">
                    <h3 class="card-title">Development</h3>
                    <p class="card-text">Clean, efficient code.</p>
                    <a href="#" class="card-link">Learn More →</a>
                </article>

                <article class="card">
                    <img src="images/seo.jpg" alt="SEO optimization service" 
                         class="card-image" width="350" height="200">
                    <h3 class="card-title">SEO</h3>
                    <p class="card-text">Get found online.</p>
                    <a href="#" class="card-link">Learn More →</a>
                </article>
            </div>
        </section>

    </main>

    <!-- Footer -->
    <footer class="site-footer">
        <div class="footer-content">
            <p class="copyright">&copy; 2025 MyBrand. All rights reserved.</p>
            <nav class="footer-nav">
                <ul class="footer-links">
                    <li><a href="privacy.html">Privacy</a></li>
                    <li><a href="terms.html">Terms</a></li>
                </ul>
            </nav>
        </div>
    </footer>

</body>
</html>
```

### How the `<link>` Tag Connects CSS:

```html
<!-- In the <head> section, link to your CSS file -->
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Page</title>

    <!-- This is how you'll connect CSS (coming in your CSS course!) -->
    <link rel="stylesheet" href="css/style.css">
</head>
```

| Attribute | Purpose |
|-----------|---------|
| `rel="stylesheet"` | Tells browser this is a CSS file |
| `href="css/style.css"` | Path to the CSS file |

---

## 8.9 Capstone Project: Complete HTML-Only Website 🏆

Now let's build a **complete, professional, multi-page website** using everything you've learned. This is your **capstone project**.

### Project: "DevAcademy" — A Web Development Learning Platform

### Folder Structure:

```
dev-academy/
├── index.html
├── about.html
├── courses.html
├── contact.html
├── blog/
│   └── html-guide.html
└── images/
    ├── logo.png
    ├── hero.jpg
    ├── html-course.jpg
    ├── css-course.jpg
    ├── js-course.jpg
    ├── team-ali.jpg
    ├── team-sara.jpg
    └── blog-html.jpg
```

---

### 📄 Page 1: `index.html` (Homepage)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="DevAcademy - Learn web development from scratch. Free HTML, CSS, and JavaScript courses for beginners.">
    <meta name="author" content="Ali Khan">
    <title>DevAcademy - Learn Web Development</title>
</head>
<body>

    <!-- ===== SITE HEADER ===== -->
    <header>
        <a href="index.html">
            <img src="images/logo.png" alt="DevAcademy - Home" width="160" height="48">
        </a>

        <nav>
            <ul>
                <li><strong>Home</strong></li>
                <li><a href="about.html">About</a></li>
                <li><a href="courses.html">Courses</a></li>
                <li><a href="blog/html-guide.html">Blog</a></li>
                <li><a href="contact.html">Contact</a></li>
            </ul>
        </nav>
    </header>

    <!-- ===== HERO SECTION ===== -->
    <main>
        <section>
            <h1>Learn Web Development from Scratch</h1>
            <p>Master HTML, CSS, and JavaScript with our free, 
               beginner-friendly courses. No prior experience needed.</p>

            <figure>
                <img src="images/hero.jpg" 
                     alt="Student learning web development on a laptop" 
                     width="800" height="450">
                <figcaption>Start your coding journey today — it's completely free!</figcaption>
            </figure>

            <p><a href="courses.html">Browse Our Courses →</a></p>
        </section>

        <hr>

        <!-- ===== FEATURED COURSES ===== -->
        <section>
            <h2>Featured Courses</h2>
            <p>Our most popular courses to kickstart your web development career.</p>

            <!-- Course Card 1 -->
            <article>
                <figure>
                    <img src="images/html-course.jpg" 
                         alt="HTML course preview showing HTML code" 
                         width="350" height="200">
                </figure>
                <h3>Complete HTML Course</h3>
                <p>Learn HTML5 from zero to building real, multi-page websites.
                   <strong>8 chapters</strong> of hands-on learning.</p>
                <p><small>Difficulty: Beginner | Duration: 4 weeks</small></p>
                <a href="courses.html#html-course">Start Learning →</a>
            </article>

            <!-- Course Card 2 -->
            <article>
                <figure>
                    <img src="images/css-course.jpg" 
                         alt="CSS course preview showing styled webpage" 
                         width="350" height="200">
                </figure>
                <h3>CSS Mastery Course</h3>
                <p>Transform your HTML into beautiful, responsive designs. 
                   Master Flexbox, Grid, and modern CSS.</p>
                <p><small>Difficulty: Beginner-Intermediate | Duration: 6 weeks</small></p>
                <a href="courses.html#css-course">Start Learning →</a>
            </article>

            <!-- Course Card 3 -->
            <article>
                <figure>
                    <img src="images/js-course.jpg" 
                         alt="JavaScript course preview showing JS code" 
                         width="350" height="200">
                </figure>
                <h3>JavaScript Fundamentals</h3>
                <p>Add interactivity to your websites. Learn variables, 
                   functions, DOM manipulation, and more.</p>
                <p><small>Difficulty: Intermediate | Duration: 8 weeks</small></p>
                <a href="courses.html#js-course">Start Learning →</a>
            </article>
        </section>

        <hr>

        <!-- ===== WHY CHOOSE US ===== -->
        <section>
            <h2>Why Choose DevAcademy?</h2>

            <dl>
                <dt>100% Free</dt>
                <dd>All courses are completely free. No hidden fees, 
                    no credit card required.</dd>

                <dt>Beginner Friendly</dt>
                <dd>Designed for absolute beginners. No prior coding 
                    experience needed.</dd>

                <dt>Project-Based Learning</dt>
                <dd>Build real projects as you learn. Practice with 
                    hands-on exercises in every chapter.</dd>

                <dt>Community Support</dt>
                <dd>Join thousands of students learning together. 
                    Get help when you need it.</dd>
            </dl>
        </section>

        <hr>

        <!-- ===== STATISTICS ===== -->
        <section>
            <h2>Our Impact</h2>
            <table>
                <caption>DevAcademy Growth Statistics (2023-2025)</caption>
                <thead>
                    <tr>
                        <th>Metric</th>
                        <th>2023</th>
                        <th>2024</th>
                        <th>2025</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td>Students Enrolled</td>
                        <td>1,200</td>
                        <td>8,500</td>
                        <td>25,000+</td>
                    </tr>
                    <tr>
                        <td>Courses Available</td>
                        <td>2</td>
                        <td>5</td>
                        <td>12</td>
                    </tr>
                    <tr>
                        <td>Countries Reached</td>
                        <td>5</td>
                        <td>28</td>
                        <td>60+</td>
                    </tr>
                    <tr>
                        <td>Completion Rate</td>
                        <td>62%</td>
                        <td>71%</td>
                        <td>78%</td>
                    </tr>
                </tbody>
            </table>
        </section>

    </main>

    <!-- ===== SIDEBAR ===== -->
    <aside>
        <h2>Latest Blog Post</h2>
        <article>
            <h3><a href="blog/html-guide.html">The Complete Beginner's Guide to HTML</a></h3>
            <p>Everything you need to know about HTML in one comprehensive article.</p>
            <p><small>Published: <time datetime="2025-01-15">January 15, 2025</time></small></p>
        </article>
    </aside>

    <!-- ===== FOOTER ===== -->
    <footer>
        <nav>
            <ul>
                <li><a href="index.html">Home</a></li>
                <li><a href="about.html">About</a></li>
                <li><a href="courses.html">Courses</a></li>
                <li><a href="contact.html">Contact</a></li>
            </ul>
        </nav>

        <p>Follow us:
            <a href="https://twitter.com/devacademy" target="_blank" rel="noopener noreferrer">Twitter</a> |
            <a href="https://github.com/devacademy" target="_blank" rel="noopener noreferrer">GitHub</a> |
            <a href="https://youtube.com/devacademy" target="_blank" rel="noopener noreferrer">YouTube</a>
        </p>

        <p><small>&copy; 2025 DevAcademy. All rights reserved.</small></p>
    </footer>

</body>
</html>
```

---

### 📄 Page 2: `about.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Learn about DevAcademy's mission, team, and story.">
    <title>About Us - DevAcademy</title>
</head>
<body>

    <!-- ===== HEADER (same on all pages) ===== -->
    <header>
        <a href="index.html">
            <img src="images/logo.png" alt="DevAcademy - Home" width="160" height="48">
        </a>
        <nav>
            <ul>
                <li><a href="index.html">Home</a></li>
                <li><strong>About</strong></li>
                <li><a href="courses.html">Courses</a></li>
                <li><a href="blog/html-guide.html">Blog</a></li>
                <li><a href="contact.html">Contact</a></li>
            </ul>
        </nav>
    </header>

    <!-- ===== MAIN CONTENT ===== -->
    <main>

        <h1>About DevAcademy</h1>
        <p>We are on a mission to make web development education 
           <strong>accessible to everyone</strong>, regardless of 
           background or financial situation.</p>

        <section>
            <h2>Our Story</h2>
            <p>DevAcademy was founded in <time datetime="2023">2023</time> 
               by Ali Khan, a self-taught developer who struggled to find 
               quality, free learning resources.</p>
            <p>What started as a small project to help a few friends learn 
               HTML has grown into a platform serving over 
               <strong>25,000 students</strong> across 60+ countries.</p>

            <blockquote>
                <p>"I believe that knowledge should be free. Everyone deserves 
                   the opportunity to learn and build a career in technology."</p>
                <p>— Ali Khan, Founder</p>
            </blockquote>
        </section>

        <section>
            <h2>Our Mission</h2>
            <ul>
                <li>Provide <strong>100% free</strong> web development education</li>
                <li>Create <em>beginner-friendly</em> content that anyone can follow</li>
                <li>Focus on <mark>practical, project-based learning</mark></li>
                <li>Build a supportive community of learners</li>
                <li>Keep all content up-to-date with modern web standards</li>
            </ul>
        </section>

        <hr>

        <section>
            <h2>Meet Our Team</h2>

            <article>
                <figure>
                    <img src="images/team-ali.jpg" 
                         alt="Ali Khan, founder of DevAcademy" 
                         width="200" height="200">
                    <figcaption>Ali Khan — Founder & Lead Instructor</figcaption>
                </figure>
                <h3>Ali Khan</h3>
                <p><strong>Founder & Lead Instructor</strong></p>
                <p>Self-taught front-end developer with 5+ years of experience. 
                   Passionate about making complex topics simple and accessible.</p>
                <dl>
                    <dt>Expertise</dt>
                    <dd>HTML, CSS, JavaScript, React</dd>
                    <dt>Education</dt>
                    <dd>BS Computer Science, LUMS</dd>
                    <dt>Location</dt>
                    <dd>Lahore, Pakistan</dd>
                </dl>
            </article>

            <article>
                <figure>
                    <img src="images/team-sara.jpg" 
                         alt="Sara Ahmed, content lead at DevAcademy" 
                         width="200" height="200">
                    <figcaption>Sara Ahmed — Content Lead</figcaption>
                </figure>
                <h3>Sara Ahmed</h3>
                <p><strong>Content Lead & Curriculum Designer</strong></p>
                <p>Technical writer and educator. Sara ensures every lesson 
                   is clear, structured, and student-friendly.</p>
                <dl>
                    <dt>Expertise</dt>
                    <dd>Technical Writing, UX Design, Accessibility</dd>
                    <dt>Education</dt>
                    <dd>MS Education Technology, NUST</dd>
                    <dt>Location</dt>
                    <dd>Islamabad, Pakistan</dd>
                </dl>
            </article>
        </section>

        <hr>

        <section>
            <h2>Key Milestones</h2>
            <table>
                <caption>DevAcademy Timeline</caption>
                <thead>
                    <tr>
                        <th>Date</th>
                        <th>Milestone</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td><time datetime="2023-03">March 2023</time></td>
                        <td>DevAcademy founded</td>
                    </tr>
                    <tr>
                        <td><time datetime="2023-06">June 2023</time></td>
                        <td>First HTML course launched</td>
                    </tr>
                    <tr>
                        <td><time datetime="2023-12">December 2023</time></td>
                        <td>1,000 students enrolled</td>
                    </tr>
                    <tr>
                        <td><time datetime="2024-06">June 2024</time></td>
                        <td>CSS and JavaScript courses added</td>
                    </tr>
                    <tr>
                        <td><time datetime="2025-01">January 2025</time></td>
                        <td>25,000+ students across 60+ countries</td>
                    </tr>
                </tbody>
            </table>
        </section>

    </main>

    <!-- ===== FOOTER ===== -->
    <footer>
        <nav>
            <ul>
                <li><a href="index.html">Home</a></li>
                <li><a href="about.html">About</a></li>
                <li><a href="courses.html">Courses</a></li>
                <li><a href="contact.html">Contact</a></li>
            </ul>
        </nav>
        <p><small>&copy; 2025 DevAcademy. All rights reserved.</small></p>
    </footer>

</body>
</html>
```

---

### 📄 Page 3: `courses.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Browse all free web development courses at DevAcademy. HTML, CSS, JavaScript and more.">
    <title>Courses - DevAcademy</title>
</head>
<body>

    <!-- ===== HEADER ===== -->
    <header>
        <a href="index.html">
            <img src="images/logo.png" alt="DevAcademy - Home" width="160" height="48">
        </a>
        <nav>
            <ul>
                <li><a href="index.html">Home</a></li>
                <li><a href="about.html">About</a></li>
                <li><strong>Courses</strong></li>
                <li><a href="blog/html-guide.html">Blog</a></li>
                <li><a href="contact.html">Contact</a></li>
            </ul>
        </nav>
    </header>

    <!-- ===== MAIN CONTENT ===== -->
    <main>

        <h1>Our Courses</h1>
        <p>All courses are <strong>100% free</strong>. Choose your learning path below.</p>

        <!-- Jump Links -->
        <nav>
            <p>Jump to:</p>
            <ul>
                <li><a href="#html-course">HTML Course</a></li>
                <li><a href="#css-course">CSS Course</a></li>
                <li><a href="#js-course">JavaScript Course</a></li>
            </ul>
        </nav>

        <hr>

        <!-- HTML Course -->
        <article id="html-course">
            <header>
                <h2>Complete HTML Course</h2>
                <p><small>Difficulty: Beginner | Duration: 4 weeks | 
                   <strong>FREE</strong></small></p>
            </header>

            <figure>
                <img src="images/html-course.jpg" 
                     alt="HTML course — code displayed on screen" 
                     width="600" height="350">
                <figcaption>Master HTML5 from scratch</figcaption>
            </figure>

            <p>Learn <abbr title="HyperText Markup Language">HTML</abbr> 
               from zero to building real, multi-page, semantic websites. 
               This course covers everything you need before moving to 
               <abbr title="Cascading Style Sheets">CSS</abbr>.</p>

            <h3>What You'll Learn</h3>
            <ul>
                <li>HTML document structure</li>
                <li>Text content, headings, and paragraphs</li>
                <li>Semantic HTML elements</li>
                <li>Links, lists, and navigation</li>
                <li>Images and media</li>
                <li>Forms and tables</li>
                <li>Building multi-page websites</li>
                <li>Accessibility and SEO basics</li>
            </ul>

            <h3>Course Chapters</h3>
            <ol>
                <li>The Web, HTML &amp; How Websites Work</li>
                <li>HTML Document Structure</li>
                <li>Text Content &amp; Headings</li>
                <li>Inline Elements &amp; Formatting</li>
                <li>Lists, Links &amp; Navigation</li>
                <li>Images, Media &amp; Content Grouping</li>
                <li>Semantic HTML, Forms &amp; Structured Data</li>
                <li>Multi-Page Websites &amp; Best Practices</li>
            </ol>

            <h3>Prerequisites</h3>
            <p>None! Just bring a computer, a text editor, and enthusiasm.</p>
        </article>

        <hr>

        <!-- CSS Course -->
        <article id="css-course">
            <header>
                <h2>CSS Mastery Course</h2>
                <p><small>Difficulty: Beginner-Intermediate | Duration: 6 weeks | 
                   <strong>FREE</strong></small></p>
            </header>

            <figure>
                <img src="images/css-course.jpg" 
                     alt="CSS course — beautiful styled webpage" 
                     width="600" height="350">
                <figcaption>Transform your HTML into beautiful designs</figcaption>
            </figure>

            <p>Master <abbr title="Cascading Style Sheets">CSS</abbr> and learn 
               to create beautiful, responsive web designs. From selectors to 
               Flexbox to Grid — everything you need.</p>

            <h3>What You'll Learn</h3>
            <ul>
                <li>CSS selectors and specificity</li>
                <li>Colors, typography, and spacing</li>
                <li>The box model</li>
                <li>Flexbox layouts</li>
                <li>CSS Grid</li>
                <li>Responsive design</li>
                <li>Animations and transitions</li>
            </ul>

            <h3>Prerequisites</h3>
            <p>Complete the <a href="#html-course">HTML Course</a> first.</p>
        </article>

        <hr>

        <!-- JavaScript Course -->
        <article id="js-course">
            <header>
                <h2>JavaScript Fundamentals</h2>
                <p><small>Difficulty: Intermediate | Duration: 8 weeks | 
                   <strong>FREE</strong></small></p>
            </header>

            <figure>
                <img src="images/js-course.jpg" 
                     alt="JavaScript course — JS code on screen" 
                     width="600" height="350">
                <figcaption>Add interactivity to your websites</figcaption>
            </figure>

            <p>Learn JavaScript from the ground up. Add dynamic behavior, 
               manipulate the DOM, handle events, and build interactive 
               web applications.</p>

            <h3>What You'll Learn</h3>
            <ul>
                <li>Variables, data types, and operators</li>
                <li>Functions and scope</li>
                <li>Arrays and objects</li>
                <li>DOM manipulation</li>
                <li>Event handling</li>
                <li>Asynchronous JavaScript</li>
                <li>Building interactive projects</li>
            </ul>

            <h3>Prerequisites</h3>
            <p>Complete the <a href="#html-course">HTML Course</a> and 
               <a href="#css-course">CSS Course</a> first.</p>
        </article>

        <hr>

        <!-- Course Comparison Table -->
        <section>
            <h2>Course Comparison</h2>
            <table>
                <caption>Compare all available courses</caption>
                <thead>
                    <tr>
                        <th>Feature</th>
                        <th>HTML</th>
                        <th>CSS</th>
                        <th>JavaScript</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td>Difficulty</td>
                        <td>Beginner</td>
                        <td>Beginner-Intermediate</td>
                        <td>Intermediate</td>
                    </tr>
                    <tr>
                        <td>Duration</td>
                        <td>4 weeks</td>
                        <td>6 weeks</td>
                        <td>8 weeks</td>
                    </tr>
                    <tr>
                        <td>Chapters</td>
                        <td>8</td>
                        <td>10</td>
                        <td>12</td>
                    </tr>
                    <tr>
                        <td>Projects</td>
                        <td>1 capstone</td>
                        <td>3 projects</td>
                        <td>5 projects</td>
                    </tr>
                    <tr>
                        <td>Price</td>
                        <td><strong>Free</strong></td>
                        <td><strong>Free</strong></td>
                        <td><strong>Free</strong></td>
                    </tr>
                </tbody>
            </table>
        </section>

    </main>

    <!-- ===== FOOTER ===== -->
    <footer>
        <nav>
            <ul>
                <li><a href="index.html">Home</a></li>
                <li><a href="about.html">About</a></li>
                <li><a href="courses.html">Courses</a></li>
                <li><a href="contact.html">Contact</a></li>
            </ul>
        </nav>
        <p><small>&copy; 2025 DevAcademy. All rights reserved.</small></p>
    </footer>

</body>
</html>
```

---

### 📄 Page 4: `contact.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Contact DevAcademy. Send us a message, ask questions, or provide feedback.">
    <title>Contact Us - DevAcademy</title>
</head>
<body>

    <!-- ===== HEADER ===== -->
    <header>
        <a href="index.html">
            <img src="images/logo.png" alt="DevAcademy - Home" width="160" height="48">
        </a>
        <nav>
            <ul>
                <li><a href="index.html">Home</a></li>
                <li><a href="about.html">About</a></li>
                <li><a href="courses.html">Courses</a></li>
                <li><a href="blog/html-guide.html">Blog</a></li>
                <li><strong>Contact</strong></li>
            </ul>
        </nav>
    </header>

    <!-- ===== MAIN CONTENT ===== -->
    <main>

        <h1>Contact Us</h1>
        <p>Have questions, feedback, or just want to say hello? 
           We'd love to hear from you!</p>

        <!-- Contact Form -->
        <section>
            <h2>Send Us a Message</h2>

            <form action="/submit-contact" method="POST">

                <fieldset>
                    <legend>Your Information</legend>

                    <div>
                        <label for="fullname">Full Name:</label><br>
                        <input type="text" id="fullname" name="fullname" 
                               placeholder="Enter your full name" 
                               minlength="2" maxlength="50" required>
                    </div>

                    <br>

                    <div>
                        <label for="email">Email Address:</label><br>
                        <input type="email" id="email" name="email" 
                               placeholder="you@example.com" required>
                    </div>

                    <br>

                    <div>
                        <label for="phone">Phone (Optional):</label><br>
                        <input type="tel" id="phone" name="phone" 
                               placeholder="+92-300-1234567">
                    </div>
                </fieldset>

                <br>

                <fieldset>
                    <legend>Your Message</legend>

                    <div>
                        <label for="subject">Subject:</label><br>
                        <select id="subject" name="subject" required>
                            <option value="">-- Select Subject --</option>
                            <option value="general">General Inquiry</option>
                            <option value="course">Course Question</option>
                            <option value="technical">Technical Support</option>
                            <option value="feedback">Feedback</option>
                            <option value="partnership">Partnership</option>
                        </select>
                    </div>

                    <br>

                    <div>
                        <p>How did you find us?</p>
                        <input type="radio" id="google" name="source" value="google">
                        <label for="google">Google Search</label><br>

                        <input type="radio" id="social" name="source" value="social">
                        <label for="social">Social Media</label><br>

                        <input type="radio" id="friend" name="source" value="friend">
                        <label for="friend">Friend/Referral</label><br>

                        <input type="radio" id="other" name="source" value="other">
                        <label for="other">Other</label>
                    </div>

                    <br>

                    <div>
                        <label for="message">Your Message:</label><br>
                        <textarea id="message" name="message" rows="6" cols="50" 
                                  placeholder="Type your message here..." 
                                  minlength="10" required></textarea>
                    </div>
                </fieldset>

                <br>

                <div>
                    <input type="checkbox" id="newsletter" name="newsletter" value="yes">
                    <label for="newsletter">Subscribe to our newsletter for updates</label>
                </div>

                <br>

                <div>
                    <input type="checkbox" id="terms" name="terms" required>
                    <label for="terms">I agree to the 
                        <a href="#" target="_blank" rel="noopener noreferrer">
                            Terms and Conditions</a></label>
                </div>

                <br>

                <div>
                    <button type="submit">Send Message</button>
                    <button type="reset">Clear Form</button>
                </div>

            </form>
        </section>

        <hr>

        <!-- Direct Contact Info -->
        <section>
            <h2>Other Ways to Reach Us</h2>

            <dl>
                <dt>Email</dt>
                <dd><a href="mailto:info@devacademy.com">info@devacademy.com</a></dd>

                <dt>Phone</dt>
                <dd><a href="tel:+923001234567">+92-300-1234567</a></dd>

                <dt>Address</dt>
                <dd>
                    DevAcademy HQ<br>
                    456 Tech Avenue<br>
                    Gulberg III, Lahore<br>
                    Punjab, Pakistan
                </dd>

                <dt>Office Hours</dt>
                <dd>Monday – Friday: 9:00 AM – 6:00 PM<br>
                    Saturday: 10:00 AM – 2:00 PM<br>
                    Sunday: Closed</dd>
            </dl>
        </section>

        <hr>

        <!-- Social Links -->
        <section>
            <h2>Follow Us</h2>
            <ul>
                <li><a href="https://twitter.com/devacademy" target="_blank" 
                       rel="noopener noreferrer">Twitter</a></li>
                <li><a href="https://github.com/devacademy" target="_blank" 
                       rel="noopener noreferrer">GitHub</a></li>
                <li><a href="https://youtube.com/devacademy" target="_blank" 
                       rel="noopener noreferrer">YouTube</a></li>
                <li><a href="https://linkedin.com/company/devacademy" target="_blank" 
                       rel="noopener noreferrer">LinkedIn</a></li>
            </ul>
        </section>

    </main>

    <!-- ===== FOOTER ===== -->
    <footer>
        <nav>
            <ul>
                <li><a href="index.html">Home</a></li>
                <li><a href="about.html">About</a></li>
                <li><a href="courses.html">Courses</a></li>
                <li><a href="contact.html">Contact</a></li>
            </ul>
        </nav>
        <p><small>&copy; 2025 DevAcademy. All rights reserved.</small></p>
    </footer>

</body>
</html>
```

---

### 📄 Page 5: `blog/html-guide.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="The complete beginner's guide to HTML. Learn what HTML is, how it works, and build your first web page.">
    <meta name="author" content="Ali Khan">
    <title>The Complete Beginner's Guide to HTML - DevAcademy Blog</title>
</head>
<body>

    <!-- ===== HEADER ===== -->
    <header>
        <!-- Note: paths go UP one level because we're in /blog/ folder -->
        <a href="../index.html">
            <img src="../images/logo.png" alt="DevAcademy - Home" width="160" height="48">
        </a>
        <nav>
            <ul>
                <li><a href="../index.html">Home</a></li>
                <li><a href="../about.html">About</a></li>
                <li><a href="../courses.html">Courses</a></li>
                <li><strong>Blog</strong></li>
                <li><a href="../contact.html">Contact</a></li>
            </ul>
        </nav>
    </header>

    <!-- ===== BREADCRUMB NAVIGATION ===== -->
    <nav>
        <p>
            <a href="../index.html">Home</a> &gt;
            <a href="#">Blog</a> &gt;
            <span>The Complete Beginner's Guide to HTML</span>
        </p>
    </nav>

    <!-- ===== MAIN CONTENT ===== -->
    <main>

        <article>
            <!-- Article Header -->
            <header>
                <h1>The Complete Beginner's Guide to HTML</h1>
                <p>By <strong>Ali Khan</strong> | 
                   Published: <time datetime="2025-01-15">January 15, 2025</time> |
                   Reading time: <time datetime="PT8M">8 minutes</time></p>
            </header>

            <!-- Article Body -->
            <figure>
                <img src="../images/blog-html.jpg" 
                     alt="HTML code displayed on a computer monitor with a dark theme" 
                     width="700" height="400">
                <figcaption>HTML — The foundation of every website on the internet</figcaption>
            </figure>

            <section>
                <h2>What is HTML?</h2>
                <p><abbr title="HyperText Markup Language">HTML</abbr> stands for 
                   <strong>HyperText Markup Language</strong>. It is the standard 
                   language used to create and structure content on the web.</p>

                <p>Every website you visit — from Google to YouTube to Amazon — 
                   is built using HTML as its foundation. HTML is <em>not</em> a 
                   programming language; it is a <mark>markup language</mark> that 
                   tells browsers what your content is.</p>
            </section>

            <section>
                <h2>Why Should You Learn HTML?</h2>
                <ol>
                    <li><strong>It's the foundation</strong> — Every web technology 
                        builds on top of HTML</li>
                    <li><strong>It's beginner-friendly</strong> — Simple syntax, 
                        instant results</li>
                    <li><strong>It's universal</strong> — Every browser supports HTML</li>
                    <li><strong>It's a lifelong skill</strong> — HTML has been around 
                        since 1991 and isn't going anywhere</li>
                    <li><strong>It's required</strong> — You cannot learn CSS or 
                        JavaScript without knowing HTML first</li>
                </ol>
            </section>

            <section>
                <h2>Your First HTML Code</h2>
                <p>Here's the simplest HTML document you can create:</p>

                <figure>
                    <code>
                        &lt;!DOCTYPE html&gt;<br>
                        &lt;html lang="en"&gt;<br>
                        &lt;head&gt;<br>
                        &nbsp;&nbsp;&lt;title&gt;My First Page&lt;/title&gt;<br>
                        &lt;/head&gt;<br>
                        &lt;body&gt;<br>
                        &nbsp;&nbsp;&lt;h1&gt;Hello, World!&lt;/h1&gt;<br>
                        &lt;/body&gt;<br>
                        &lt;/html&gt;
                    </code>
                    <figcaption>A minimal HTML5 document</figcaption>
                </figure>

                <p>Save this as <code>index.html</code> and open it in your browser. 
                   You'll see your first web page!</p>
            </section>

            <section>
                <h2>What's Next?</h2>
                <p>Ready to learn more? Check out our 
                   <a href="../courses.html#html-course">Complete HTML Course</a> — 
                   it covers everything from the basics to building real, 
                   multi-page websites.</p>
            </section>

            <!-- Article Footer -->
            <footer>
                <p><strong>Tags:</strong> HTML, Beginners, Web Development, Tutorial</p>
                <p><strong>Share this article:</strong>
                    <a href="https://twitter.com/intent/tweet?text=Check+out+this+HTML+guide" 
                       target="_blank" rel="noopener noreferrer">Twitter</a> |
                    <a href="https://www.linkedin.com/sharing" 
                       target="_blank" rel="noopener noreferrer">LinkedIn</a>
                </p>
            </footer>
        </article>

    </main>

    <!-- ===== SIDEBAR ===== -->
    <aside>
        <section>
            <h2>About the Author</h2>
            <img src="../images/team-ali.jpg" alt="Ali Khan" width="120" height="120">
            <p><strong>Ali Khan</strong> — Founder of DevAcademy and front-end 
               developer with 5+ years of experience.</p>
            <p><a href="../about.html">Learn more about our team</a></p>
        </section>

        <section>
            <h2>Related Courses</h2>
            <ul>
                <li><a href="../courses.html#html-course">Complete HTML Course</a></li>
                <li><a href="../courses.html#css-course">CSS Mastery Course</a></li>
            </ul>
        </section>
    </aside>

    <!-- ===== FOOTER ===== -->
    <footer>
        <nav>
            <ul>
                <li><a href="../index.html">Home</a></li>
                <li><a href="../about.html">About</a></li>
                <li><a href="../courses.html">Courses</a></li>
                <li><a href="../contact.html">Contact</a></li>
            </ul>
        </nav>
        <p><small>&copy; 2025 DevAcademy. All rights reserved.</small></p>
    </footer>

</body>
</html>
```

---

## 8.10 What Your HTML Journey Has Covered

Let's review everything you've learned across all 8 chapters:

```
Chapter 1 ✅  The Web, HTML & How Websites Work
Chapter 2 ✅  HTML Document Structure & Clean Code
Chapter 3 ✅  Text Content, Headings & Readable Pages
Chapter 4 ✅  Inline Elements, Formatting & Semantic Meaning
Chapter 5 ✅  Lists, Links & Navigation
Chapter 6 ✅  Images, Media & Content Grouping
Chapter 7 ✅  Semantic HTML, Forms & Structured Data
Chapter 8 ✅  Multi-Page Websites, Best Practices & Capstone
```

### Complete Tag Reference:

| Category | Tags Learned |
|----------|-------------|
| **Document** | `<!DOCTYPE>`, `<html>`, `<head>`, `<body>`, `<title>`, `<meta>` |
| **Text** | `<h1>`-`<h6>`, `<p>`, `<br>`, `<hr>` |
| **Inline** | `<strong>`, `<em>`, `<mark>`, `<small>`, `<sup>`, `<sub>`, `<del>`, `<ins>`, `<abbr>`, `<code>`, `<q>`, `<span>` |
| **Lists** | `<ul>`, `<ol>`, `<li>`, `<dl>`, `<dt>`, `<dd>` |
| **Links** | `<a>` |
| **Images** | `<img>`, `<figure>`, `<figcaption>` |
| **Grouping** | `<div>`, `<span>`, `<blockquote>` |
| **Semantic** | `<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<aside>`, `<footer>`, `<time>` |
| **Tables** | `<table>`, `<tr>`, `<th>`, `<td>`, `<thead>`, `<tbody>`, `<tfoot>`, `<caption>` |
| **Forms** | `<form>`, `<input>`, `<label>`, `<textarea>`, `<select>`, `<option>`, `<optgroup>`, `<button>`, `<fieldset>`, `<legend>` |

---

## 8.11 Summary & Key Takeaways

| Concept | Key Point |
|---------|-----------|
| Folder structure | Organize files into `images/`, `pages/`, `blog/`, `css/`, `js/` |
| File naming | Lowercase, hyphens, descriptive, no spaces |
| `index.html` | Always the homepage at root level |
| Consistent navigation | Same nav on every page, current page highlighted |
| Relative paths | Use `../` to go up, `folder/` to go in |
| `class` attribute | Reusable CSS targets for groups of elements |
| `id` attribute | Unique CSS/JS target for single elements |
| HTML rules | Complete structure, one h1, sequential headings, semantic tags |
| Common mistakes | No div soup, no tables for layout, always use alt, label inputs |
| CSS preparation | Add classes/ids, use semantic structure, link CSS file |
| Validation | Always check code at validator.w3.org |
| Capstone | Complete multi-page website using ALL learned concepts |

---

## 🎓 Course Completion Certificate

```
╔══════════════════════════════════════════════════════════╗
║                                                          ║
║         📘 COMPLETE HTML COURSE — FINISHED! 🎉           ║
║                                                          ║
║   You have successfully completed all 8 chapters:        ║
║                                                          ║
║   ✅ Chapter 1: The Web, HTML & How Websites Work        ║
║   ✅ Chapter 2: HTML Document Structure & Clean Code     ║
║   ✅ Chapter 3: Text Content, Headings & Readable Pages  ║
║   ✅ Chapter 4: Inline Elements & Semantic Meaning       ║
║   ✅ Chapter 5: Lists, Links & Navigation                ║
║   ✅ Chapter 6: Images, Media & Content Grouping         ║
║   ✅ Chapter 7: Semantic HTML, Forms & Structured Data   ║
║   ✅ Chapter 8: Multi-Page Websites & Best Practices     ║
║                                                          ║
║   🏆 You are now fully ready to learn CSS!               ║
║                                                          ║
╚══════════════════════════════════════════════════════════╝
```

---

> 🎉 **Congratulations!** You've completed the **entire HTML course**! You now have the knowledge to build real, multi-page, semantic, accessible HTML websites from scratch. You are **fully prepared** to begin learning **CSS** and transform your HTML into beautiful, responsive web designs!