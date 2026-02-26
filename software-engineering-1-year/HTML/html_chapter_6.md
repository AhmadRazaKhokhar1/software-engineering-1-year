

# 📘 Chapter 6: Images, Media & Content Grouping

> **🔵 Visual content + structure — Learning how to add images, understand file paths, and group content properly using `<div>` and `<span>` to prepare your HTML for CSS.**

---

## 6.1 Why Images & Content Grouping Matter

Text alone can communicate information, but **images make websites come alive**. And **content grouping** is how we organize our HTML into logical sections that CSS can style later.

| Concept | Purpose |
|---------|---------|
| **Images** | Add visual content — photos, icons, illustrations |
| **Alt Text** | Describe images for accessibility and SEO |
| **File Paths** | Tell the browser WHERE to find image files |
| **`<div>`** | Group block-level content into sections |
| **`<span>`** | Group inline content for styling |
| **Content Grouping** | Organize HTML logically for CSS preparation |

> 🧠 **Think about it:** Every website you visit — Amazon, YouTube, Instagram — relies heavily on images AND well-grouped content sections. Mastering both is essential.

---

## 6.2 The `<img>` Tag — Adding Images

The `<img>` tag embeds an image into your web page. It is a **void element** (self-closing — no closing tag needed).

### Syntax:

```html
<img src="image-path.jpg" alt="Description of the image">
```

| Attribute | Purpose |
|-----------|---------|
| `src` | **Source** — the path/URL to the image file |
| `alt` | **Alternative text** — describes the image |

### Basic Example:

```html
<img src="photo.jpg" alt="A beautiful sunset over the ocean">
```

### Key Facts About `<img>`:

```
✅ It is an INLINE element (sits within text flow)
✅ It is a VOID element (no closing tag)
✅ The src attribute is REQUIRED
✅ The alt attribute is REQUIRED for accessibility
✅ It does NOT have any content between tags
```

---

## 6.3 The `src` Attribute — Where Is the Image?

The `src` (source) attribute tells the browser **where to find the image file**. It can be a relative path or an absolute URL.

### Using Local Images (Relative Path):

```
my-website/
├── index.html
├── logo.png
├── images/
│   ├── hero.jpg
│   ├── team.jpg
│   └── products/
│       ├── product1.jpg
│       └── product2.jpg
└── pages/
    └── about.html
```

#### From `index.html`:

```html
<!-- Same folder -->
<img src="logo.png" alt="Company logo">

<!-- Inside images subfolder -->
<img src="images/hero.jpg" alt="Hero banner image">
<img src="images/team.jpg" alt="Our team photo">

<!-- Deeper subfolder -->
<img src="images/products/product1.jpg" alt="Product 1 photo">
```

#### From `pages/about.html`:

```html
<!-- Go up one folder, then find the image -->
<img src="../logo.png" alt="Company logo">

<!-- Go up one folder, then into images -->
<img src="../images/hero.jpg" alt="Hero banner image">

<!-- Go up one folder, then deeper into images/products -->
<img src="../images/products/product1.jpg" alt="Product 1 photo">
```

### Using Online Images (Absolute URL):

```html
<!-- Image from an external website -->
<img src="https://example.com/images/photo.jpg" alt="External photo">

<!-- Image from a CDN or image service -->
<img src="https://via.placeholder.com/300x200" alt="Placeholder image">
```

### ⚠️ Important Notes on External Images:

```
❌ External images can be REMOVED or CHANGED without your control
❌ They require internet connection to load
❌ Using someone else's images may violate COPYRIGHT
❌ They can slow down your page (loading from another server)

✅ Always prefer LOCAL images stored in your project folder
✅ If using external images, ensure you have permission
```

---

## 6.4 The `alt` Attribute — Accessibility & SEO

The `alt` attribute provides **alternative text** that describes the image. This is one of the **most important attributes in HTML**.

### Why `alt` Text is Critical:

| Reason | Explanation |
|--------|-------------|
| **Accessibility** | Screen readers read the `alt` text aloud for visually impaired users |
| **Broken Images** | If the image fails to load, `alt` text is displayed instead |
| **SEO** | Search engines can't "see" images — they read `alt` text to understand them |
| **Slow Connections** | Users on slow internet see `alt` text before images load |

### Writing Good Alt Text:

```html
<!-- ✅ GOOD: Descriptive and meaningful -->
<img src="sunset.jpg" alt="A golden sunset over the Pacific Ocean with palm trees">

<img src="team.jpg" alt="Five team members standing in front of the office building">

<img src="chart.jpg" alt="Bar chart showing sales growth from 2020 to 2024">

<img src="logo.png" alt="WebDev Academy logo">
```

```html
<!-- ❌ BAD: Vague, unhelpful, or missing -->
<img src="sunset.jpg" alt="image">
<img src="sunset.jpg" alt="photo">
<img src="sunset.jpg" alt="pic1234.jpg">
<img src="sunset.jpg" alt="">       <!-- Empty alt for non-decorative image -->
<img src="sunset.jpg">              <!-- Missing alt entirely -->
```

### Alt Text Rules:

| Rule | Example |
|------|---------|
| **Describe what the image SHOWS** | `alt="A red sports car parked on the street"` |
| **Keep it concise** (under 125 characters) | Don't write a whole paragraph |
| **Don't start with "Image of" or "Picture of"** | Screen readers already announce "image" |
| **Include relevant keywords** (naturally) | Helps SEO without keyword stuffing |
| **Empty alt for decorative images** | `alt=""` for images with no informational value |

### When to Use Empty `alt=""`:

```html
<!-- Decorative images that add no information -->
<img src="divider-line.png" alt="">
<img src="decorative-pattern.jpg" alt="">
<img src="background-texture.png" alt="">
```

> 📌 **Rule:** An empty `alt=""` tells screen readers to **skip** the image entirely. Use this ONLY for purely decorative images that add no content value.

### What Happens When an Image Fails to Load:

```html
<img src="nonexistent.jpg" alt="Photo of our team at the conference">
```

```
If the image fails to load, the browser shows:
┌─────────────────────────────────────┐
│  [✕] Photo of our team at the      │
│      conference                     │
└─────────────────────────────────────┘
The alt text appears with a broken image icon.
```

---

## 6.5 Image Attributes — `width`, `height`, `title`

### `width` and `height` — Setting Image Dimensions:

```html
<!-- Setting exact dimensions in pixels -->
<img src="photo.jpg" alt="Landscape photo" width="600" height="400">

<!-- Setting only width (height adjusts proportionally) -->
<img src="photo.jpg" alt="Landscape photo" width="300">

<!-- Setting only height (width adjusts proportionally) -->
<img src="photo.jpg" alt="Landscape photo" height="200">
```

### Why Set Width and Height?

| Benefit | Explanation |
|---------|-------------|
| **Prevents layout shift** | Browser reserves space before image loads |
| **Faster perceived loading** | Page doesn't "jump" when images appear |
| **Better user experience** | Content stays stable during loading |

### How Layout Shift Works:

```
WITHOUT width/height:
┌──────────────────────┐        ┌──────────────────────┐
│ Text paragraph       │   →    │ Text paragraph       │
│ More text            │        │ [IMAGE LOADS HERE]    │
│ Even more text       │        │                      │
└──────────────────────┘        │ More text ← JUMPED!  │
                                │ Even more text       │
                                └──────────────────────┘

WITH width/height:
┌──────────────────────┐        ┌──────────────────────┐
│ Text paragraph       │   →    │ Text paragraph       │
│ [Reserved Space]     │        │ [IMAGE LOADS HERE]    │
│                      │        │                      │
│ More text            │        │ More text ← STABLE!  │
│ Even more text       │        │ Even more text       │
└──────────────────────┘        └──────────────────────┘
```

> 📌 **Best Practice:** Always include `width` and `height` attributes. They help browsers reserve the correct space.

### `title` — Tooltip on Hover:

```html
<img src="photo.jpg" 
     alt="Team photo at annual meetup" 
     title="Our team at the 2024 annual meetup"
     width="600" 
     height="400">
```

| Attribute | What It Does |
|-----------|-------------|
| `alt` | Read by screen readers; shown when image fails |
| `title` | Shows as a **tooltip** when user **hovers** over the image |

```
Hovering over the image shows:
┌─────────────────────────────────────┐
│            [IMAGE]                  │
│                                     │
│  ┌─────────────────────────────┐    │
│  │ Our team at the 2024 annual │    │  ← Tooltip
│  │ meetup                      │    │
│  └─────────────────────────────┘    │
└─────────────────────────────────────┘
```

> ⚠️ **Note:** `title` is a BONUS, not a replacement for `alt`. Screen readers may or may not read `title`. Always write proper `alt` text.

---

## 6.6 Supported Image Formats

| Format | Extension | Best For | Transparency? |
|--------|-----------|----------|--------------|
| **JPEG** | `.jpg` / `.jpeg` | Photographs, complex images | ❌ No |
| **PNG** | `.png` | Logos, icons, images needing transparency | ✅ Yes |
| **GIF** | `.gif` | Simple animations, small icons | ✅ Yes (limited) |
| **SVG** | `.svg` | Logos, icons, scalable graphics | ✅ Yes |
| **WebP** | `.webp` | Modern web images (smaller file size) | ✅ Yes |

### Choosing the Right Format:

```
📷 Photograph of a person → JPEG
🎨 Logo with transparent background → PNG or SVG
🔄 Small animation → GIF
📐 Icon or scalable graphic → SVG
🌐 Modern optimized image → WebP
```

### Example with Different Formats:

```html
<!-- Photograph -->
<img src="images/hero-photo.jpg" alt="Mountain landscape" width="800" height="500">

<!-- Logo with transparency -->
<img src="images/logo.png" alt="Company logo" width="200" height="60">

<!-- Animated element -->
<img src="images/loading.gif" alt="Loading animation" width="50" height="50">

<!-- Scalable icon -->
<img src="images/icon.svg" alt="Settings icon" width="32" height="32">
```

---

## 6.7 Folder Organization for Images

A well-organized folder structure makes your project **maintainable and professional**.

### ❌ Bad Organization:

```
my-website/
├── index.html
├── photo1.jpg
├── photo2.jpg
├── logo.png
├── banner.jpg
├── icon1.png
├── icon2.png
├── bg-pattern.jpg
├── team-photo.jpg
└── product-image.jpg

(All files dumped in one folder — messy!)
```

### ✅ Good Organization:

```
my-website/
├── index.html
├── about.html
├── contact.html
├── images/
│   ├── logo.png
│   ├── hero-banner.jpg
│   ├── team/
│   │   ├── member1.jpg
│   │   ├── member2.jpg
│   │   └── member3.jpg
│   ├── products/
│   │   ├── product1.jpg
│   │   ├── product2.jpg
│   │   └── product3.jpg
│   └── icons/
│       ├── email-icon.png
│       ├── phone-icon.png
│       └── location-icon.png
└── pages/
    ├── services.html
    └── blog.html
```

### File Naming Best Practices:

```
✅ GOOD file names:
hero-banner.jpg
team-photo.jpg
product-wireless-headphones.jpg
logo-dark.png

❌ BAD file names:
IMG_20240115_001.jpg       (camera default — meaningless)
photo (2).jpg              (spaces and parentheses)
LOGO.PNG                   (all caps)
my super cool image!.jpg   (spaces and special characters)
```

| Rule | Example |
|------|---------|
| Use **lowercase** letters | `hero-banner.jpg` |
| Use **hyphens** for spaces | `team-photo.jpg` not `team photo.jpg` |
| Be **descriptive** | `sunset-beach.jpg` not `img001.jpg` |
| **No special characters** | No `!`, `@`, `#`, `&`, spaces |
| Keep names **short but clear** | `logo.png` not `the-main-company-logo-image-final-v2.png` |

---

## 6.8 Images as Links — Clickable Images

You can wrap an `<img>` tag inside an `<a>` tag to make an image **clickable**.

### Syntax:

```html
<a href="destination.html">
    <img src="image.jpg" alt="Description">
</a>
```

### Real-World Examples:

```html
<!-- Clickable logo that goes to homepage -->
<a href="index.html">
    <img src="images/logo.png" alt="WebDev Academy - Go to Homepage" width="200" height="60">
</a>

<!-- Product image that links to product page -->
<a href="products/headphones.html">
    <img src="images/products/headphones.jpg" 
         alt="ProSound Wireless Headphones - Click to view details" 
         width="300" 
         height="300">
</a>

<!-- Social media icon linking to Twitter -->
<a href="https://twitter.com/webdev" target="_blank" rel="noopener noreferrer">
    <img src="images/icons/twitter-icon.png" 
         alt="Follow us on Twitter" 
         width="32" 
         height="32">
</a>

<!-- Thumbnail that links to full-size image -->
<a href="images/full-size-photo.jpg">
    <img src="images/thumbnail-photo.jpg" 
         alt="Mountain landscape - Click for full size" 
         width="150" 
         height="100">
</a>
```

### Important Alt Text for Linked Images:

```html
<!-- ✅ GOOD: Alt text describes where the link goes -->
<a href="index.html">
    <img src="logo.png" alt="Homepage - WebDev Academy">
</a>

<!-- ❌ BAD: Alt text doesn't mention the link destination -->
<a href="index.html">
    <img src="logo.png" alt="Logo">
</a>
```

> 📌 **Rule:** When an image is a link, the `alt` text should describe **both the image AND the link destination**. Screen readers need to tell users where the link will take them.

---

## 6.9 The `<figure>` and `<figcaption>` Tags

The `<figure>` tag wraps an image (or diagram, code, etc.) with its **caption**. The `<figcaption>` provides the caption text.

### Syntax:

```html
<figure>
    <img src="photo.jpg" alt="Description" width="600" height="400">
    <figcaption>Caption text goes here</figcaption>
</figure>
```

### Why Use `<figure>` and `<figcaption>`?

| Benefit | Explanation |
|---------|-------------|
| **Semantic meaning** | Browser knows the image and caption are related |
| **Accessibility** | Screen readers associate the caption with the image |
| **SEO** | Search engines understand the image context better |
| **Clean structure** | Groups image + caption as a single unit |

### Real-World Examples:

```html
<!-- Blog post with captioned image -->
<h2>Our New Office</h2>
<p>We recently moved to a beautiful new workspace in downtown Lahore.</p>

<figure>
    <img src="images/new-office.jpg" 
         alt="Modern open-plan office with large windows and standing desks" 
         width="700" 
         height="450">
    <figcaption>Our new open-plan office space in Lahore (Photo: Ali Khan, 2025)</figcaption>
</figure>

<p>The new space allows our team to collaborate more effectively.</p>


<!-- Science article with diagram -->
<h2>The Water Cycle</h2>
<figure>
    <img src="images/water-cycle-diagram.png" 
         alt="Diagram showing evaporation, condensation, and precipitation in the water cycle" 
         width="600" 
         height="400">
    <figcaption>Figure 1: The three stages of the water cycle</figcaption>
</figure>


<!-- Photo gallery item -->
<figure>
    <img src="images/sunset.jpg" 
         alt="Vibrant orange and purple sunset over the Arabian Sea" 
         width="500" 
         height="350">
    <figcaption>Sunset at Clifton Beach, Karachi — December 2024</figcaption>
</figure>
```

### `<figcaption>` Placement:

```html
<!-- ✅ Caption AFTER image (most common) -->
<figure>
    <img src="photo.jpg" alt="Description" width="400" height="300">
    <figcaption>Caption below the image</figcaption>
</figure>

<!-- ✅ Caption BEFORE image (also valid) -->
<figure>
    <figcaption>Caption above the image</figcaption>
    <img src="photo.jpg" alt="Description" width="400" height="300">
</figure>
```

### `<figure>` Without Images:

`<figure>` isn't just for images — it can wrap any **self-contained content**:

```html
<!-- Code snippet as a figure -->
<figure>
    <code>&lt;h1&gt;Hello World&lt;/h1&gt;</code>
    <figcaption>Example: A basic HTML heading</figcaption>
</figure>

<!-- Quotation as a figure -->
<figure>
    <blockquote>
        <p>The best way to predict the future is to create it.</p>
    </blockquote>
    <figcaption>— Abraham Lincoln</figcaption>
</figure>
```

---

## 6.10 The `<div>` Tag — Block-Level Content Grouping

The `<div>` tag is a **generic block-level container**. It has **no semantic meaning** — its only purpose is to **group content** for styling or layout.

### Syntax:

```html
<div>
    <!-- Grouped content goes here -->
</div>
```

### Key Characteristics:

| Feature | Detail |
|---------|--------|
| **Type** | Block-level element |
| **Semantic meaning** | ❌ None — it's a generic container |
| **Purpose** | Group elements for CSS styling/layout |
| **Default visual** | No visual appearance (invisible box) |
| **Can contain** | Any block or inline elements |

### Why Use `<div>`?

```
Without <div>: Individual elements floating around with no grouping
With <div>:    Related elements grouped together → CSS can style the group
```

### Basic Example:

```html
<!-- Without div: All elements are separate -->
<h2>Our Services</h2>
<p>We offer web design.</p>
<p>Contact us for more info.</p>

<h2>About Us</h2>
<p>We are a web agency.</p>
<p>Founded in 2023.</p>


<!-- With div: Elements are grouped logically -->
<div>
    <h2>Our Services</h2>
    <p>We offer web design.</p>
    <p>Contact us for more info.</p>
</div>

<div>
    <h2>About Us</h2>
    <p>We are a web agency.</p>
    <p>Founded in 2023.</p>
</div>
```

> Both look the **same** in the browser right now. But with CSS, you can style each `<div>` differently (background color, borders, spacing, layout).

### Real-World `<div>` Usage:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Div Example - My Website</title>
</head>
<body>

    <!-- Header section -->
    <div>
        <h1>My Website</h1>
        <p>Building the web, one page at a time.</p>
    </div>

    <!-- Navigation section -->
    <div>
        <ul>
            <li><a href="index.html">Home</a></li>
            <li><a href="about.html">About</a></li>
            <li><a href="contact.html">Contact</a></li>
        </ul>
    </div>

    <!-- Main content section -->
    <div>
        <h2>Welcome</h2>
        <p>Thank you for visiting our website. We specialize 
           in creating beautiful, accessible web experiences.</p>

        <!-- Card 1 -->
        <div>
            <h3>Web Design</h3>
            <img src="images/design.jpg" alt="Web design illustration" width="300" height="200">
            <p>Beautiful designs that capture your brand.</p>
        </div>

        <!-- Card 2 -->
        <div>
            <h3>Development</h3>
            <img src="images/code.jpg" alt="Code on screen" width="300" height="200">
            <p>Clean, efficient code that works perfectly.</p>
        </div>

        <!-- Card 3 -->
        <div>
            <h3>SEO</h3>
            <img src="images/seo.jpg" alt="SEO analytics dashboard" width="300" height="200">
            <p>Get found online with our SEO services.</p>
        </div>
    </div>

    <!-- Footer section -->
    <div>
        <p><small>&copy; 2025 My Website. All rights reserved.</small></p>
    </div>

</body>
</html>
```

---

## 6.11 The `<span>` Tag — Inline Content Grouping

The `<span>` tag is a **generic inline container**. Like `<div>`, it has **no semantic meaning** — it exists purely for **targeting specific text** for CSS styling.

### Syntax:

```html
<p>This is <span>targeted text</span> in a sentence.</p>
```

### Key Characteristics:

| Feature | Detail |
|---------|--------|
| **Type** | Inline element |
| **Semantic meaning** | ❌ None — generic inline container |
| **Purpose** | Target specific text for CSS styling |
| **Default visual** | No visual appearance |
| **Can contain** | Only inline elements and text |

### Examples:

```html
<!-- Targeting specific words for future CSS styling -->
<p>The price is <span>$49.99</span> per month.</p>

<p>Welcome, <span>Ali Khan</span>! You have <span>3</span> new messages.</p>

<p>Status: <span>Active</span></p>

<p>Our office is in <span>Lahore</span>, <span>Pakistan</span>.</p>
```

> 📌 **Right now**, these `<span>` elements look exactly the same as regular text. The power comes when you add CSS:
> ```css
> /* Future CSS example */
> span { color: red; font-weight: bold; }
> ```

---

## 6.12 `<div>` vs `<span>` — Complete Comparison

| Feature | `<div>` | `<span>` |
|---------|---------|----------|
| **Type** | Block-level | Inline |
| **Starts new line?** | ✅ Yes | ❌ No |
| **Takes full width?** | ✅ Yes | ❌ Only content width |
| **Semantic meaning?** | ❌ None | ❌ None |
| **Purpose** | Group block content | Group inline content |
| **Can contain** | Block + inline elements | Only inline elements + text |
| **Common use** | Page sections, cards, wrappers | Words, phrases, small text targets |

### Visual Comparison:

```html
<!-- <div> creates separate blocks -->
<div>Block 1</div>
<div>Block 2</div>
<div>Block 3</div>

<!-- Displays as:
Block 1
Block 2
Block 3
(each on its own line, full width)
-->


<!-- <span> stays inline -->
<p>
    <span>Word 1</span>
    <span>Word 2</span>
    <span>Word 3</span>
</p>

<!-- Displays as:
Word 1 Word 2 Word 3
(all on the same line)
-->
```

### When to Use Each:

```html
<!-- Use <div> for grouping large sections -->
<div>
    <h2>Section Title</h2>
    <p>Section content paragraph.</p>
    <img src="photo.jpg" alt="Section image" width="300" height="200">
</div>

<!-- Use <span> for targeting small inline text -->
<p>Your order total is <span>$129.99</span>.</p>
```

---

## 6.13 Grouping Content Correctly — Preparing for CSS

Proper content grouping in HTML makes CSS styling **10x easier**. Here's how to think about it:

### The "What Can CSS Style?" Mindset:

```
CSS styles HTML ELEMENTS.
If you want to style something → it needs to BE an element.
If you want to style a GROUP of things → they need a CONTAINER element.
```

### Example: Building a Card Component

```html
<!-- A "card" is a common web design pattern -->
<!-- We group the card content inside a <div> -->

<div>
    <img src="images/blog-post.jpg" 
         alt="Person coding on laptop" 
         width="350" 
         height="200">
    <h3>Getting Started with HTML</h3>
    <p>Learn the fundamentals of HTML in this 
       beginner-friendly guide.</p>
    <p><small>Published: January 2025</small></p>
    <a href="blog/post1.html">Read More →</a>
</div>
```

### Example: Full Page Layout Grouping

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Well-Grouped Page</title>
</head>
<body>

    <!-- ===== HEADER GROUP ===== -->
    <div>
        <img src="images/logo.png" alt="TechBlog Logo" width="150" height="45">
        <h1>TechBlog</h1>
        <p>Your daily dose of web development tips</p>
    </div>

    <!-- ===== NAVIGATION GROUP ===== -->
    <div>
        <ul>
            <li><a href="index.html">Home</a></li>
            <li><a href="articles.html">Articles</a></li>
            <li><a href="about.html">About</a></li>
            <li><a href="contact.html">Contact</a></li>
        </ul>
    </div>

    <!-- ===== MAIN CONTENT GROUP ===== -->
    <div>

        <!-- Featured Article -->
        <div>
            <h2>Featured Article</h2>
            <figure>
                <img src="images/featured.jpg" 
                     alt="Computer screen showing HTML code" 
                     width="700" 
                     height="400">
                <figcaption>Learning HTML in 2025</figcaption>
            </figure>
            <p>HTML remains the foundation of every website. 
               In this comprehensive guide, we explore everything 
               you need to know to get started.</p>
            <a href="blog/featured.html">Read Full Article →</a>
        </div>

        <hr>

        <!-- Recent Articles Grid -->
        <h2>Recent Articles</h2>

        <!-- Article Card 1 -->
        <div>
            <img src="images/css-article.jpg" 
                 alt="CSS styling example" 
                 width="300" 
                 height="180">
            <h3>CSS Flexbox Guide</h3>
            <p>Master CSS Flexbox with practical examples.</p>
            <p><small>January 10, 2025</small></p>
            <a href="blog/flexbox.html">Read More →</a>
        </div>

        <!-- Article Card 2 -->
        <div>
            <img src="images/js-article.jpg" 
                 alt="JavaScript code on screen" 
                 width="300" 
                 height="180">
            <h3>JavaScript for Beginners</h3>
            <p>Start your JavaScript journey with these basics.</p>
            <p><small>January 8, 2025</small></p>
            <a href="blog/javascript.html">Read More →</a>
        </div>

        <!-- Article Card 3 -->
        <div>
            <img src="images/responsive-article.jpg" 
                 alt="Website on multiple devices" 
                 width="300" 
                 height="180">
            <h3>Responsive Design 101</h3>
            <p>Make your websites look great on any device.</p>
            <p><small>January 5, 2025</small></p>
            <a href="blog/responsive.html">Read More →</a>
        </div>

    </div>

    <!-- ===== SIDEBAR GROUP ===== -->
    <div>
        <h2>About the Author</h2>
        <img src="images/author.jpg" 
             alt="Ali Khan, author photo" 
             width="150" 
             height="150">
        <p><strong>Ali Khan</strong></p>
        <p>Front-end developer and educator. 
           I write about HTML, CSS, and JavaScript.</p>

        <h3>Follow Me</h3>
        <ul>
            <li><a href="https://twitter.com/ali" 
                   target="_blank" 
                   rel="noopener noreferrer">Twitter</a></li>
            <li><a href="https://github.com/ali" 
                   target="_blank" 
                   rel="noopener noreferrer">GitHub</a></li>
        </ul>
    </div>

    <!-- ===== FOOTER GROUP ===== -->
    <div>
        <p><small>&copy; 2025 TechBlog. All rights reserved.</small></p>
        <ul>
            <li><a href="privacy.html">Privacy Policy</a></li>
            <li><a href="terms.html">Terms of Service</a></li>
        </ul>
    </div>

</body>
</html>
```

### How CSS Will Use These Groups:

```
When you learn CSS, you'll be able to:

<div> (Header)     → Add background color, padding, center content
<div> (Navigation) → Make links horizontal, add hover effects
<div> (Main)       → Set max-width, center on page
<div> (Cards)      → Add borders, shadows, arrange in grid
<div> (Sidebar)    → Float to the side, set width
<div> (Footer)     → Add dark background, white text

Without grouping → CSS has nothing to target!
```

---

## 6.14 Common Mistakes with Images & Grouping

### Mistake 1: Missing `alt` Attribute

```html
<!-- ❌ WRONG -->
<img src="photo.jpg">

<!-- ✅ CORRECT -->
<img src="photo.jpg" alt="Mountain landscape at sunset">
```

### Mistake 2: Wrong File Path

```html
<!-- ❌ WRONG: Incorrect path or typo -->
<img src="imges/photo.jpg" alt="Photo">    <!-- "imges" typo -->
<img src="Photo.jpg" alt="Photo">          <!-- Case sensitive! file is "photo.jpg" -->

<!-- ✅ CORRECT -->
<img src="images/photo.jpg" alt="Photo">
<img src="photo.jpg" alt="Photo">
```

### Mistake 3: Using `<div>` When Semantic Tags Exist

```html
<!-- ❌ Using <div> for everything (we'll learn better tags in Chapter 7) -->
<div>Navigation here</div>
<div>Header here</div>
<div>Footer here</div>

<!-- ✅ Better: Use semantic tags (Chapter 7) -->
<nav>Navigation here</nav>
<header>Header here</header>
<footer>Footer here</footer>
```

> 📌 We use `<div>` in this chapter to understand grouping. In Chapter 7, you'll learn **semantic alternatives** that are even better.

### Mistake 4: Using `<div>` Where `<span>` Should Be Used

```html
<!-- ❌ WRONG: <div> inside a <p> (block inside inline context) -->
<p>The total is <div>$99.99</div> after tax.</p>

<!-- ✅ CORRECT: <span> for inline targeting -->
<p>The total is <span>$99.99</span> after tax.</p>
```

### Mistake 5: Overusing `<div>` (Div Soup)

```html
<!-- ❌ "Div Soup" — unnecessary nesting -->
<div>
    <div>
        <div>
            <div>
                <p>Just a simple paragraph buried in divs.</p>
            </div>
        </div>
    </div>
</div>

<!-- ✅ CLEAN: Only use <div> when needed for grouping -->
<p>Just a simple paragraph — no extra divs needed.</p>
```

### Mistake 6: Spaces in File Names

```html
<!-- ❌ WRONG: Spaces in file name cause errors -->
<img src="images/my photo.jpg" alt="Photo">

<!-- ✅ CORRECT: Use hyphens instead of spaces -->
<img src="images/my-photo.jpg" alt="Photo">
```

---

## 6.15 Complete Reference Table

| Element | Type | Purpose | Semantic? |
|---------|------|---------|-----------|
| `<img>` | Inline / Void | Embeds an image | ✅ Yes |
| `<figure>` | Block | Groups image with caption | ✅ Yes |
| `<figcaption>` | Block | Caption for a `<figure>` | ✅ Yes |
| `<div>` | Block | Generic block container | ❌ No |
| `<span>` | Inline | Generic inline container | ❌ No |

| Attribute | Used On | Purpose |
|-----------|---------|---------|
| `src` | `<img>` | Path to image file |
| `alt` | `<img>` | Alternative text description |
| `width` | `<img>` | Image width in pixels |
| `height` | `<img>` | Image height in pixels |
| `title` | `<img>` (and others) | Tooltip on hover |

---

## 6.16 Summary & Key Takeaways

| Concept | Key Point |
|---------|-----------|
| `<img>` | Void, inline element that embeds images |
| `src` attribute | Path to the image file (relative or absolute) |
| `alt` attribute | REQUIRED — describes image for accessibility & SEO |
| `width` / `height` | Prevents layout shift; set image dimensions |
| Image formats | JPEG for photos, PNG for transparency, SVG for icons |
| File organization | Keep images in an `images/` folder with descriptive names |
| Images as links | Wrap `<img>` inside `<a>` for clickable images |
| `<figure>` + `<figcaption>` | Semantic grouping of image + caption |
| `<div>` | Generic block container for grouping sections |
| `<span>` | Generic inline container for targeting text |
| `<div>` vs `<span>` | Block vs inline; sections vs text |
| Content grouping | Organize HTML into logical groups for CSS |
| Div soup | Avoid unnecessary `<div>` nesting |

---

> 🎉 **Congratulations!** You've completed Chapter 6. You now know how to add images, organize files, write proper alt text, and group content using `<div>` and `<span>` to prepare your HTML for CSS styling!

---