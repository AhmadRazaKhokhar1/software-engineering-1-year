

# 📘 Chapter 1: CSS Fundamentals & Connecting CSS to HTML

> **🔵 Understanding CSS and applying basic styles — Learning what CSS is, how to connect it to HTML, CSS syntax, colors, and text styling.**

---

## 1.1 What is CSS and Why Does It Exist?

In the HTML course, you learned how to **structure** content. But structure alone looks plain and boring. **CSS** is what makes websites beautiful.

**CSS** stands for **Cascading Style Sheets**.

Let's break that down:

| Word | Meaning |
|------|---------|
| **Cascading** | Styles flow downward and can override each other in a specific order |
| **Style** | Visual appearance — colors, fonts, spacing, layout |
| **Sheets** | Separate files/documents containing style rules |

### What CSS Does:

```
✅ Controls COLORS (text, backgrounds, borders)
✅ Controls TYPOGRAPHY (fonts, sizes, weight, spacing)
✅ Controls LAYOUT (positioning, alignment, grids)
✅ Controls SPACING (margins, padding, gaps)
✅ Controls RESPONSIVENESS (adapting to different screen sizes)
✅ Controls ANIMATIONS (transitions, hover effects)
```

### What CSS Does NOT Do:

```
❌ Does NOT define content or structure (that's HTML)
❌ Does NOT add logic or interactivity (that's JavaScript)
❌ Does NOT store or process data (that's backend)
```

> 🧠 **Remember the house analogy from HTML?**
> - **HTML** = Bricks, walls, doors, windows (structure)
> - **CSS** = Paint, wallpaper, furniture, lighting (style) ← **We are HERE**
> - **JavaScript** = Electricity, plumbing, smart devices (behavior)

---

## 1.2 HTML vs CSS — Clear Responsibilities

Understanding the separation between HTML and CSS is **critical** for writing clean, professional code.

| Responsibility | HTML | CSS |
|----------------|------|-----|
| **Content** | ✅ "There is a heading here" | ❌ |
| **Structure** | ✅ "This is a navigation list" | ❌ |
| **Meaning** | ✅ "This text is important" | ❌ |
| **Color** | ❌ | ✅ "Make the heading blue" |
| **Font** | ❌ | ✅ "Use Arial font" |
| **Spacing** | ❌ | ✅ "Add 20px padding" |
| **Layout** | ❌ | ✅ "Put these side by side" |
| **Size** | ❌ | ✅ "Make this 300px wide" |

### The Golden Rule:

```
HTML answers: WHAT is the content?
CSS answers:  HOW does it look?
```

### ❌ Bad Practice — Mixing Style into HTML:

```html
<!-- ❌ WRONG: Using HTML attributes for styling -->
<h1 align="center">Welcome</h1>
<p><font color="red" size="5">Red text</font></p>
<table bgcolor="yellow" width="500">...</table>

<!-- These HTML styling attributes are OBSOLETE -->
```

### ✅ Good Practice — Separating Structure and Style:

```html
<!-- HTML: Structure only -->
<h1>Welcome</h1>
<p>Important text here</p>
```

```css
/* CSS: Style only */
h1 {
    text-align: center;
}
p {
    color: red;
    font-size: 20px;
}
```

> 📌 **Rule:** Never use HTML for styling. Always use CSS. This separation makes your code cleaner, maintainable, and professional.

---

## 1.3 Three Ways to Add CSS to HTML

There are three methods to connect CSS to your HTML. Each has its purpose, but **one is the clear winner** for real projects.

### Method 1: Inline CSS

CSS is written directly on an HTML element using the `style` attribute.

```html
<h1 style="color: blue; text-align: center;">Hello World</h1>
<p style="font-size: 18px; color: gray;">This is a paragraph.</p>
```

| Pros | Cons |
|------|------|
| Quick for testing | ❌ Mixes HTML and CSS (messy) |
| Overrides other styles | ❌ Cannot reuse styles |
| | ❌ Hard to maintain |
| | ❌ Makes HTML bloated |
| | ❌ Not scalable |

> ⚠️ **Verdict:** Avoid inline CSS. Use it ONLY for quick testing or email templates.

---

### Method 2: Internal CSS (Embedded)

CSS is written inside a `<style>` tag in the `<head>` section of the HTML file.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Internal CSS Example</title>

    <!-- Internal CSS -->
    <style>
        h1 {
            color: blue;
            text-align: center;
        }
        p {
            font-size: 18px;
            color: gray;
        }
    </style>
</head>
<body>
    <h1>Hello World</h1>
    <p>This is a paragraph.</p>
</body>
</html>
```

| Pros | Cons |
|------|------|
| Styles are separated from content | ❌ Only works for ONE page |
| Easy to read and organize | ❌ Cannot reuse across multiple pages |
| Good for single-page projects | ❌ Makes HTML file longer |
| Good for learning/practice | ❌ Not scalable for large sites |

> ⚠️ **Verdict:** Good for learning and single-page projects. Not ideal for multi-page websites.

---

### Method 3: External CSS ✅ (Best Practice)

CSS is written in a **separate `.css` file** and linked to the HTML using the `<link>` tag.

**File Structure:**

```
my-website/
├── index.html
├── about.html
├── css/
│   └── style.css
└── images/
    └── logo.png
```

**`index.html`:**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>External CSS Example</title>

    <!-- Link to external CSS file -->
    <link rel="stylesheet" href="css/style.css">
</head>
<body>
    <h1>Hello World</h1>
    <p>This is a paragraph.</p>
</body>
</html>
```

**`css/style.css`:**

```css
h1 {
    color: blue;
    text-align: center;
}

p {
    font-size: 18px;
    color: gray;
}
```

| Pros | Cons |
|------|------|
| ✅ Complete separation of HTML and CSS | Requires extra file |
| ✅ One CSS file styles ALL pages | Extra HTTP request (negligible) |
| ✅ Easy to maintain and update | |
| ✅ Browser caches the CSS file (faster) | |
| ✅ Industry standard | |
| ✅ Scales to large projects | |

> ✅ **Verdict:** Always use external CSS for real projects. This is the **professional standard**.

### The `<link>` Tag Explained:

```html
<link rel="stylesheet" href="css/style.css">
```

| Attribute | Purpose |
|-----------|---------|
| `rel="stylesheet"` | Tells the browser this is a CSS file |
| `href="css/style.css"` | Path to the CSS file (relative URL) |

### Linking CSS from Different Folder Levels:

```html
<!-- From index.html (root level) -->
<link rel="stylesheet" href="css/style.css">

<!-- From pages/about.html (one level deep) -->
<link rel="stylesheet" href="../css/style.css">

<!-- From blog/posts/article.html (two levels deep) -->
<link rel="stylesheet" href="../../css/style.css">
```

---

### Comparison Table: All Three Methods

| Feature | Inline | Internal | External ✅ |
|---------|--------|----------|------------|
| **Where written** | On HTML element | In `<head>` `<style>` | Separate `.css` file |
| **Reusable across pages** | ❌ No | ❌ No | ✅ Yes |
| **Separation of concerns** | ❌ No | Partial | ✅ Full |
| **Maintainability** | ❌ Poor | Okay | ✅ Excellent |
| **Scalability** | ❌ Terrible | Limited | ✅ Excellent |
| **Performance** | N/A | N/A | ✅ Cached by browser |
| **Professional use** | ❌ Rarely | Sometimes | ✅ Always |

---

## 1.4 CSS Syntax — The Building Blocks

Every CSS rule follows the **exact same structure**. Once you learn it, you can write any CSS.

### The CSS Rule Structure:

```css
selector {
    property: value;
    property: value;
    property: value;
}
```

### Anatomy of a CSS Rule:

```css
h1 {
    color: blue;
    font-size: 32px;
    text-align: center;
}
```

| Part | What It Is | Example |
|------|-----------|---------|
| **Selector** | WHAT to style (which HTML element) | `h1` |
| **Declaration Block** | The `{ }` containing all styles | `{ ... }` |
| **Property** | WHICH aspect to change | `color`, `font-size` |
| **Value** | HOW to change it | `blue`, `32px` |
| **Declaration** | One complete property: value pair | `color: blue;` |
| **Semicolon** | Ends each declaration | `;` |

### Visual Breakdown:

```
    selector
       ↓
      h1    {                    ← Opening brace
          color: blue;           ← Declaration (property: value;)
          ^^^^^  ^^^^
          property value
          font-size: 32px;       ← Another declaration
          text-align: center;    ← Another declaration
      }                          ← Closing brace
```

### Multiple Selectors with Same Styles:

```css
/* Apply same styles to multiple elements */
h1, h2, h3 {
    color: navy;
    font-family: Arial, sans-serif;
}
```

> The comma `,` means "AND" — style h1 AND h2 AND h3.

### Important Syntax Rules:

```css
/* ✅ CORRECT */
h1 {
    color: blue;
    font-size: 24px;
}

/* ❌ WRONG: Missing semicolons */
h1 {
    color: blue
    font-size: 24px
}

/* ❌ WRONG: Missing colon */
h1 {
    color blue;
}

/* ❌ WRONG: Missing braces */
h1
    color: blue;

/* ❌ WRONG: Using = instead of : */
h1 {
    color = blue;
}
```

---

## 1.5 CSS Selectors — Targeting HTML Elements

Selectors tell CSS **which HTML element(s) to style**. There are several types:

### 1. Element (Tag) Selector:

Targets ALL elements of that type:

```css
/* Styles ALL <p> elements on the page */
p {
    color: gray;
    font-size: 16px;
}

/* Styles ALL <h1> elements */
h1 {
    color: navy;
}

/* Styles ALL <a> (link) elements */
a {
    color: blue;
    text-decoration: none;
}
```

### 2. Class Selector (`.`):

Targets elements with a **specific class attribute**. Uses a dot `.` before the class name.

```html
<!-- HTML -->
<p class="intro">This is the introduction.</p>
<p>This is a regular paragraph.</p>
<p class="intro">This is also an introduction.</p>
```

```css
/* CSS: Only styles elements with class="intro" */
.intro {
    font-size: 20px;
    color: darkblue;
    font-weight: bold;
}
```

> 📌 Classes are **reusable** — multiple elements can have the same class.

### 3. ID Selector (`#`):

Targets the element with a **specific id attribute**. Uses a hash `#` before the id name.

```html
<!-- HTML -->
<h1 id="page-title">Welcome to My Website</h1>
```

```css
/* CSS: Only styles the element with id="page-title" */
#page-title {
    color: darkred;
    text-align: center;
    font-size: 36px;
}
```

> 📌 IDs are **unique** — only ONE element per page can have a specific id.

### 4. Universal Selector (`*`):

Targets **ALL elements** on the page:

```css
/* Styles EVERY element */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
```

> This is commonly used as a **CSS reset** to remove default browser styles.

### 5. Grouping Selector (`,`):

Applies the **same styles to multiple selectors**:

```css
/* Instead of writing the same styles three times... */
h1 {
    font-family: Arial, sans-serif;
    color: navy;
}
h2 {
    font-family: Arial, sans-serif;
    color: navy;
}
h3 {
    font-family: Arial, sans-serif;
    color: navy;
}

/* ...group them with commas: */
h1, h2, h3 {
    font-family: Arial, sans-serif;
    color: navy;
}
```

### 6. Descendant Selector (space):

Targets elements **inside** another element:

```css
/* Only styles <a> elements that are INSIDE <nav> */
nav a {
    color: white;
    text-decoration: none;
}

/* Only styles <li> elements inside <ul> that's inside <nav> */
nav ul li {
    display: inline;
}

/* Only styles <p> inside elements with class "card" */
.card p {
    color: gray;
    font-size: 14px;
}
```

### Selector Priority (Specificity):

When multiple rules target the same element, CSS uses **specificity** to decide which wins:

```
Priority (Low → High):

1. Element selector        → p { }              (specificity: 1)
2. Class selector          → .intro { }         (specificity: 10)
3. ID selector             → #title { }         (specificity: 100)
4. Inline style            → style="..."        (specificity: 1000)
5. !important              → color: red !important;  (overrides everything)
```

### Example of Specificity:

```html
<p id="special" class="highlight">Which color wins?</p>
```

```css
p {
    color: black;            /* Specificity: 1 */
}

.highlight {
    color: yellow;           /* Specificity: 10 — HIGHER */
}

#special {
    color: red;              /* Specificity: 100 — HIGHEST → WINS! */
}
```

> 📌 The paragraph will be **red** because `#special` has the highest specificity.

### Complete Selector Reference:

| Selector | Syntax | Targets | Specificity |
|----------|--------|---------|------------|
| Element | `p` | All `<p>` elements | 1 |
| Class | `.intro` | Elements with `class="intro"` | 10 |
| ID | `#hero` | Element with `id="hero"` | 100 |
| Universal | `*` | All elements | 0 |
| Grouping | `h1, h2, p` | All listed elements | Per selector |
| Descendant | `nav a` | `<a>` inside `<nav>` | Sum of parts |

---

## 1.6 CSS Comments

Just like HTML, CSS has comments that browsers ignore. They're notes for developers.

### Syntax:

```css
/* This is a CSS comment */

/* 
   This is a 
   multi-line CSS comment 
*/
```

### Practical Usage:

```css
/* ==========================================
   GLOBAL STYLES
   ========================================== */

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

/* --- Typography --- */
body {
    font-family: Arial, sans-serif;
    font-size: 16px;
    line-height: 1.6;
    color: #333;
}

/* --- Headings --- */
h1 {
    font-size: 36px;
    color: navy;
    /* TODO: Add responsive font size later */
}

/* --- Navigation --- */
nav {
    background-color: #333;
}

nav a {
    color: white;
    text-decoration: none;  /* Removes underline from links */
}
```

> 📌 **Best Practice:** Use comments to organize your CSS into sections. This makes large stylesheets much easier to navigate.

---

## 1.7 Color Formats in CSS

CSS offers multiple ways to define colors. Understanding all formats is essential.

### 1. Named Colors:

CSS has **147 predefined color names**:

```css
h1 { color: red; }
p { color: darkblue; }
body { background-color: whitesmoke; }
a { color: tomato; }
```

**Common Named Colors:**

| Color Name | Preview Description |
|-----------|-------------------|
| `red` | Pure red |
| `blue` | Pure blue |
| `green` | Dark green |
| `black` | Pure black |
| `white` | Pure white |
| `gray` | Medium gray |
| `navy` | Dark blue |
| `tomato` | Red-orange |
| `coral` | Light red-orange |
| `gold` | Golden yellow |
| `teal` | Blue-green |
| `whitesmoke` | Very light gray |

> Named colors are great for learning but limited for professional design.

### 2. HEX Colors:

A **6-digit code** preceded by `#`, representing Red, Green, Blue values:

```css
h1 { color: #ff0000; }         /* Pure red */
p { color: #333333; }          /* Dark gray */
body { background-color: #f5f5f5; }  /* Light gray */
a { color: #0066cc; }          /* Medium blue */
```

### How HEX Works:

```
Format: #RRGGBB

Each pair (RR, GG, BB) is a value from 00 to FF (0-255 in hex)

#FF0000 → Red: FF (max), Green: 00 (none), Blue: 00 (none) = RED
#00FF00 → Red: 00, Green: FF (max), Blue: 00 = GREEN
#0000FF → Red: 00, Green: 00, Blue: FF (max) = BLUE
#FFFFFF → All max = WHITE
#000000 → All zero = BLACK
#808080 → All medium = GRAY
```

### HEX Shorthand:

When both digits in each pair are the same, you can shorten:

```css
#ff0000 → #f00     /* Red */
#00ff00 → #0f0     /* Green */
#0000ff → #00f     /* Blue */
#ffffff → #fff     /* White */
#000000 → #000     /* Black */
#336699 → #369     /* Blue-gray */
```

### 3. RGB Colors:

Uses the `rgb()` function with values from **0 to 255** for each channel:

```css
h1 { color: rgb(255, 0, 0); }           /* Red */
p { color: rgb(51, 51, 51); }           /* Dark gray */
body { background-color: rgb(245, 245, 245); }  /* Light gray */
```

### How RGB Works:

```
Format: rgb(red, green, blue)

Each value ranges from 0 (none) to 255 (full)

rgb(255, 0, 0)     = Red
rgb(0, 255, 0)     = Green
rgb(0, 0, 255)     = Blue
rgb(255, 255, 255) = White
rgb(0, 0, 0)       = Black
rgb(128, 128, 128) = Gray
```

### 4. RGBA Colors (With Transparency):

Same as RGB but with an **alpha channel** (opacity) from **0 to 1**:

```css
/* Fully opaque (solid) */
h1 { color: rgba(255, 0, 0, 1); }

/* 50% transparent */
p { background-color: rgba(0, 0, 0, 0.5); }

/* 20% transparent */
div { background-color: rgba(0, 0, 255, 0.2); }

/* Fully transparent (invisible) */
span { color: rgba(0, 0, 0, 0); }
```

### Alpha Values Explained:

```
1.0 = Fully visible (100% opaque)
0.8 = Mostly visible (80% opaque)
0.5 = Half transparent (50% opaque)
0.2 = Mostly transparent (20% opaque)
0.0 = Completely invisible (0% opaque)
```

### Real-World RGBA Usage:

```css
/* Semi-transparent black overlay on images */
.overlay {
    background-color: rgba(0, 0, 0, 0.6);
}

/* Subtle shadow/background */
.card {
    background-color: rgba(255, 255, 255, 0.9);
}

/* Faded text */
.muted-text {
    color: rgba(0, 0, 0, 0.5);
}
```

### Color Format Comparison:

| Format | Syntax | Transparency? | Example |
|--------|--------|--------------|---------|
| Named | `red` | ❌ No | `color: tomato;` |
| HEX | `#RRGGBB` | ❌ No | `color: #ff6347;` |
| RGB | `rgb(R, G, B)` | ❌ No | `color: rgb(255, 99, 71);` |
| RGBA | `rgba(R, G, B, A)` | ✅ Yes | `color: rgba(255, 99, 71, 0.5);` |

> 📌 **Pro Tip:** In professional projects, **HEX** is most commonly used for solid colors, and **RGBA** when you need transparency.

---

## 1.8 Styling Text — Fonts

CSS gives you complete control over how text looks.

### `font-family` — Choosing Fonts:

```css
/* Single font */
body {
    font-family: Arial;
}

/* Font stack (fallback system) */
body {
    font-family: "Segoe UI", Arial, sans-serif;
}
```

### How Font Stacks Work:

```css
font-family: "Segoe UI", Tahoma, Arial, sans-serif;
/*
   Browser tries:
   1. "Segoe UI"    → If installed, USE IT
   2. Tahoma        → If Segoe UI not found, try this
   3. Arial         → If Tahoma not found, try this
   4. sans-serif    → If nothing else found, use ANY sans-serif font
*/
```

> 📌 **Rule:** Always end your font stack with a **generic font family** as the final fallback.

### Generic Font Families:

| Generic Family | Style | Example Fonts |
|---------------|-------|---------------|
| `serif` | Has decorative strokes | Times New Roman, Georgia |
| `sans-serif` | Clean, no strokes | Arial, Helvetica, Verdana |
| `monospace` | Fixed-width characters | Courier New, Consolas |
| `cursive` | Handwriting style | Comic Sans MS, Brush Script |
| `fantasy` | Decorative/display | Impact, Papyrus |

### Common Professional Font Stacks:

```css
/* Clean and modern (most common) */
body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", 
                 Roboto, "Helvetica Neue", Arial, sans-serif;
}

/* Traditional and readable */
body {
    font-family: Georgia, "Times New Roman", Times, serif;
}

/* Code/monospace */
code {
    font-family: "Courier New", Consolas, "Liberation Mono", monospace;
}
```

### Using Google Fonts (Free Web Fonts):

```html
<!-- Step 1: Add this link in <head> BEFORE your CSS link -->
<head>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="css/style.css">
</head>
```

```css
/* Step 2: Use the font in CSS */
body {
    font-family: 'Poppins', sans-serif;
}
```

> 🔗 Browse free fonts at: [https://fonts.google.com](https://fonts.google.com)

---

## 1.9 Styling Text — Font Size

### `font-size` — Controlling Text Size:

```css
h1 { font-size: 36px; }
h2 { font-size: 28px; }
h3 { font-size: 22px; }
p  { font-size: 16px; }
small { font-size: 12px; }
```

### Common Units for Font Size:

| Unit | Type | Relative To | Example |
|------|------|-------------|---------|
| `px` | Absolute | Fixed pixels | `font-size: 16px;` |
| `em` | Relative | Parent element's font size | `font-size: 1.5em;` |
| `rem` | Relative | Root (`<html>`) font size | `font-size: 1.5rem;` |
| `%` | Relative | Parent element's font size | `font-size: 120%;` |

### `px` (Pixels) — Fixed Size:

```css
/* Pixels give you exact control */
h1 { font-size: 36px; }    /* Always exactly 36px */
p  { font-size: 16px; }    /* Always exactly 16px */
```

### `rem` (Root EM) — Relative to Root:

```css
/* Default root font size is 16px in most browsers */
html { font-size: 16px; }   /* Root size = 16px */

h1 { font-size: 2rem; }     /* 2 × 16px = 32px */
h2 { font-size: 1.5rem; }   /* 1.5 × 16px = 24px */
p  { font-size: 1rem; }     /* 1 × 16px = 16px */
small { font-size: 0.75rem; } /* 0.75 × 16px = 12px */
```

> 📌 **Best Practice:** Use `rem` for font sizes. It scales consistently and is easier to maintain than `px`.

### `em` — Relative to Parent:

```css
/* em is relative to the PARENT element's font size */
.parent {
    font-size: 20px;
}

.parent .child {
    font-size: 1.5em;    /* 1.5 × 20px = 30px */
}

.parent .child .grandchild {
    font-size: 1.5em;    /* 1.5 × 30px = 45px — COMPOUNDS! */
}
```

> ⚠️ **Warning:** `em` units **compound** — they multiply with each nesting level. This can lead to unexpected sizes. Prefer `rem` for font sizes.

---

## 1.10 Styling Text — Font Weight

### `font-weight` — Bold and Thin Text:

```css
/* Keyword values */
h1 { font-weight: bold; }       /* Bold */
p  { font-weight: normal; }     /* Regular */
span { font-weight: lighter; }  /* Lighter than parent */

/* Numeric values (100-900) */
h1 { font-weight: 700; }        /* Bold */
h2 { font-weight: 600; }        /* Semi-bold */
p  { font-weight: 400; }        /* Normal/Regular */
span { font-weight: 300; }      /* Light */
```

### Weight Scale:

| Value | Name | Appearance |
|-------|------|-----------|
| `100` | Thin | Very light |
| `200` | Extra Light | Very light |
| `300` | Light | Light |
| `400` | Normal / Regular | Default body text |
| `500` | Medium | Slightly bold |
| `600` | Semi Bold | Noticeably bold |
| `700` | Bold | Standard bold |
| `800` | Extra Bold | Very bold |
| `900` | Black | Maximum bold |

```css
/* Common combinations */
body {
    font-weight: 400;       /* Regular text */
}

h1, h2, h3 {
    font-weight: 700;       /* Bold headings */
}

.subtitle {
    font-weight: 300;       /* Light subtitle */
}

.important {
    font-weight: 600;       /* Semi-bold for emphasis */
}
```

> 📌 **Note:** Not all fonts support all weights. If a font doesn't have weight 300, the browser picks the closest available weight.

---

## 1.11 Styling Text — Text Alignment & Other Properties

### `text-align` — Horizontal Alignment:

```css
h1 { text-align: center; }       /* Centered */
p  { text-align: left; }         /* Left-aligned (default) */
.quote { text-align: right; }    /* Right-aligned */
.article { text-align: justify; } /* Justified (both edges even) */
```

### Visual Result:

```
text-align: left;                 text-align: center;
┌──────────────────────┐          ┌──────────────────────┐
│ Text aligns to the   │          │   Text is centered   │
│ left edge of the     │          │   within the element │
│ container.           │          │                      │
└──────────────────────┘          └──────────────────────┘

text-align: right;                text-align: justify;
┌──────────────────────┐          ┌──────────────────────┐
│   Text aligns to the │          │ Text  stretches   to │
│     right edge of    │          │ fill   both   edges  │
│        the container │          │ of  the   container. │
└──────────────────────┘          └──────────────────────┘
```

### `text-decoration` — Underlines, Strikethrough:

```css
a { text-decoration: none; }             /* Removes underline from links */
.underlined { text-decoration: underline; }
.strikethrough { text-decoration: line-through; }
.overlined { text-decoration: overline; }
```

### `text-transform` — Changing Case:

```css
h1 { text-transform: uppercase; }     /* HELLO WORLD */
h2 { text-transform: capitalize; }    /* Hello World */
p  { text-transform: lowercase; }     /* hello world */
.normal { text-transform: none; }     /* As written in HTML */
```

### `line-height` — Space Between Lines:

```css
/* Line height controls the vertical space between lines of text */
p {
    font-size: 16px;
    line-height: 1.6;     /* 1.6 × 16px = 25.6px between lines */
}

/* Common values */
body { line-height: 1.6; }     /* ✅ Best for body text */
h1 { line-height: 1.2; }       /* Tighter for headings */
```

### `letter-spacing` — Space Between Letters:

```css
h1 { letter-spacing: 2px; }         /* Spread out letters */
.tight { letter-spacing: -0.5px; }  /* Tighter letters */
.logo { letter-spacing: 5px; }      /* Very spread out */
```

### `word-spacing` — Space Between Words:

```css
p { word-spacing: 3px; }            /* Wider word gaps */
```

### `text-indent` — First Line Indentation:

```css
p { text-indent: 30px; }   /* Indent first line of paragraph */
```

---

## 1.12 Complete Text Styling Reference

| Property | Purpose | Example |
|----------|---------|---------|
| `font-family` | Font type | `font-family: Arial, sans-serif;` |
| `font-size` | Text size | `font-size: 16px;` |
| `font-weight` | Bold/thin | `font-weight: 700;` |
| `font-style` | Italic/normal | `font-style: italic;` |
| `color` | Text color | `color: #333;` |
| `text-align` | Horizontal alignment | `text-align: center;` |
| `text-decoration` | Underline/strike/none | `text-decoration: none;` |
| `text-transform` | Case change | `text-transform: uppercase;` |
| `line-height` | Space between lines | `line-height: 1.6;` |
| `letter-spacing` | Space between letters | `letter-spacing: 1px;` |
| `word-spacing` | Space between words | `word-spacing: 2px;` |
| `text-indent` | First line indent | `text-indent: 20px;` |

---

## 1.13 Basic Page Styling — Putting It All Together

Let's create a **complete, styled web page** using everything learned in this chapter.

### File Structure:

```
my-styled-site/
├── index.html
├── css/
│   └── style.css
└── images/
    └── logo.png
```

### `index.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="A beautifully styled webpage using CSS fundamentals">
    <title>TechBlog - Web Development Articles</title>

    <!-- Google Font -->
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap" rel="stylesheet">

    <!-- External CSS -->
    <link rel="stylesheet" href="css/style.css">
</head>
<body>

    <!-- Header -->
    <header>
        <h1>TechBlog</h1>
        <p>Your daily source for web development knowledge</p>
    </header>

    <!-- Navigation -->
    <nav>
        <ul>
            <li><a href="index.html">Home</a></li>
            <li><a href="about.html">About</a></li>
            <li><a href="courses.html">Courses</a></li>
            <li><a href="contact.html">Contact</a></li>
        </ul>
    </nav>

    <!-- Main Content -->
    <main>

        <section>
            <h2>Welcome to TechBlog</h2>
            <p>Discover tutorials, tips, and best practices for modern 
               web development. Whether you are a beginner or an experienced 
               developer, we have something for everyone.</p>
        </section>

        <section>
            <h2>Latest Articles</h2>

            <article class="blog-card">
                <h3>Getting Started with HTML</h3>
                <p class="meta">By Ali Khan | January 15, 2025</p>
                <p>HTML is the foundation of every website. Learn the basics 
                   in this comprehensive beginner guide.</p>
                <a href="#" class="read-more">Read More →</a>
            </article>

            <article class="blog-card">
                <h3>CSS Fundamentals Explained</h3>
                <p class="meta">By Sara Ahmed | January 18, 2025</p>
                <p>Understand how CSS works and start styling your HTML 
                   pages with confidence.</p>
                <a href="#" class="read-more">Read More →</a>
            </article>

            <article class="blog-card">
                <h3>JavaScript for Beginners</h3>
                <p class="meta">By Omar Farooq | January 20, 2025</p>
                <p>Add interactivity to your websites with JavaScript. 
                   This guide covers the absolute basics.</p>
                <a href="#" class="read-more">Read More →</a>
            </article>
        </section>

        <section id="about-section">
            <h2>About Us</h2>
            <p>TechBlog is a free learning platform dedicated to making 
               web development accessible to everyone. Our mission is to 
               provide clear, practical, and up-to-date content.</p>
        </section>

    </main>

    <!-- Footer -->
    <footer>
        <p>&copy; 2025 TechBlog. All rights reserved.</p>
        <p>Follow us: 
            <a href="#">Twitter</a> | 
            <a href="#">GitHub</a> | 
            <a href="#">YouTube</a>
        </p>
    </footer>

</body>
</html>
```

### `css/style.css`:

```css
/* ==========================================
   CSS RESET & GLOBAL STYLES
   ========================================== */

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Poppins', Arial, sans-serif;
    font-size: 16px;
    line-height: 1.6;
    color: #333333;
    background-color: #f9f9f9;
}


/* ==========================================
   TYPOGRAPHY
   ========================================== */

h1 {
    font-size: 2.5rem;
    font-weight: 700;
    color: #ffffff;
}

h2 {
    font-size: 1.8rem;
    font-weight: 600;
    color: #2c3e50;
    margin-bottom: 15px;
}

h3 {
    font-size: 1.3rem;
    font-weight: 600;
    color: #34495e;
    margin-bottom: 8px;
}

p {
    font-size: 1rem;
    font-weight: 400;
    color: #555555;
    margin-bottom: 12px;
}


/* ==========================================
   HEADER
   ========================================== */

header {
    background-color: #2c3e50;
    color: #ffffff;
    text-align: center;
    padding: 40px 20px;
}

header p {
    color: rgba(255, 255, 255, 0.8);
    font-size: 1.1rem;
    font-weight: 300;
}


/* ==========================================
   NAVIGATION
   ========================================== */

nav {
    background-color: #34495e;
    text-align: center;
    padding: 12px 0;
}

nav ul {
    list-style: none;
}

nav ul li {
    display: inline;
    margin: 0 15px;
}

nav a {
    color: #ecf0f1;
    text-decoration: none;
    font-size: 1rem;
    font-weight: 400;
    text-transform: uppercase;
    letter-spacing: 1px;
}

nav a:hover {
    color: #3498db;
}


/* ==========================================
   MAIN CONTENT
   ========================================== */

main {
    max-width: 900px;
    margin: 30px auto;
    padding: 0 20px;
}

section {
    margin-bottom: 40px;
}


/* ==========================================
   BLOG CARDS
   ========================================== */

.blog-card {
    background-color: #ffffff;
    padding: 25px;
    margin-bottom: 20px;
    border-left: 4px solid #3498db;
}

.blog-card h3 {
    color: #2c3e50;
}

.meta {
    font-size: 0.85rem;
    color: #999999;
    font-style: italic;
    margin-bottom: 10px;
}

.read-more {
    color: #3498db;
    text-decoration: none;
    font-weight: 600;
}

.read-more:hover {
    text-decoration: underline;
}


/* ==========================================
   ABOUT SECTION
   ========================================== */

#about-section {
    background-color: #eaf2f8;
    padding: 30px;
    text-align: center;
}


/* ==========================================
   FOOTER
   ========================================== */

footer {
    background-color: #2c3e50;
    color: #ecf0f1;
    text-align: center;
    padding: 25px 20px;
}

footer p {
    color: rgba(255, 255, 255, 0.7);
    font-size: 0.9rem;
}

footer a {
    color: #3498db;
    text-decoration: none;
}

footer a:hover {
    text-decoration: underline;
}
```

### What This CSS Demonstrates:

```
✅ External CSS file properly linked
✅ CSS reset with universal selector (*)
✅ Font families with Google Fonts
✅ Font sizes using rem units
✅ Font weights (300, 400, 600, 700)
✅ Named colors, HEX colors, and RGBA colors
✅ Text alignment, decoration, and transform
✅ Element selectors (body, h1, p, nav, footer)
✅ Class selectors (.blog-card, .meta, .read-more)
✅ ID selectors (#about-section)
✅ Descendant selectors (nav a, nav ul li)
✅ Hover pseudo-class (nav a:hover)
✅ Comments organizing the stylesheet
✅ Line-height and letter-spacing
✅ Clean, organized CSS structure
```

---

## 1.14 The `:hover` Pseudo-Class (Bonus)

The `:hover` pseudo-class applies styles when the user **hovers their mouse** over an element.

```css
/* Change link color on hover */
a {
    color: blue;
    text-decoration: none;
}

a:hover {
    color: red;
    text-decoration: underline;
}

/* Change button background on hover */
button {
    background-color: #3498db;
    color: white;
}

button:hover {
    background-color: #2980b9;
}

/* Change card background on hover */
.card {
    background-color: white;
}

.card:hover {
    background-color: #f0f0f0;
}
```

> 📌 Hover effects give users **visual feedback** — they know the element is interactive.

---

## 1.15 Summary & Key Takeaways

| Concept | Key Point |
|---------|-----------|
| CSS | Controls visual appearance (style) of HTML content |
| HTML vs CSS | HTML = structure, CSS = style — always separate them |
| Inline CSS | `style=""` attribute — avoid in real projects |
| Internal CSS | `<style>` in `<head>` — okay for single pages |
| External CSS | Separate `.css` file — always use for real projects ✅ |
| `<link>` tag | Connects CSS file: `<link rel="stylesheet" href="css/style.css">` |
| CSS Syntax | `selector { property: value; }` |
| Element Selector | `p { }` — targets all `<p>` elements |
| Class Selector | `.intro { }` — targets elements with `class="intro"` |
| ID Selector | `#hero { }` — targets element with `id="hero"` |
| Specificity | ID (100) > Class (10) > Element (1) |
| Named Colors | `red`, `blue`, `tomato` |
| HEX Colors | `#ff0000`, `#333`, `#3498db` |
| RGB/RGBA | `rgb(255, 0, 0)`, `rgba(0, 0, 0, 0.5)` |
| `font-family` | Font type with fallback stack |
| `font-size` | Text size (`px`, `rem`, `em`) |
| `font-weight` | Boldness (100-900 or `bold`/`normal`) |
| `text-align` | `left`, `center`, `right`, `justify` |
| `text-decoration` | `none`, `underline`, `line-through` |
| `line-height` | Space between text lines (use `1.6` for body) |
| `:hover` | Styles applied when mouse hovers over element |

---

> 🎉 **Congratulations!** You've completed Chapter 1 of the HTML + CSS course. You now understand what CSS is, how to connect it to HTML, CSS syntax, selectors, color formats, and text styling!

---