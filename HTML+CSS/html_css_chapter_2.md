

# 📘 Chapter 2: CSS Box Model & Layout Foundations

> **🟢 Controlling spacing and layout — Mastering the CSS Box Model, display properties, backgrounds, and building real page sections.**

---

## 2.1 The Most Important Concept in CSS — The Box Model

Every single HTML element on a web page is a **rectangular box**. Even circles, text, and images — they all live inside invisible boxes. Understanding this is the **key to mastering CSS layout**.

> 🧠 **Think of it this way:** Every element is a package being shipped. The content is the item inside, padding is the bubble wrap, border is the cardboard box, and margin is the space between packages on the shelf.

### The Four Layers of the Box Model:

```
┌─────────────────────────────────────────────────┐
│                   MARGIN                        │  ← Space OUTSIDE the box
│   ┌─────────────────────────────────────────┐   │
│   │               BORDER                    │   │  ← Edge of the box
│   │   ┌─────────────────────────────────┐   │   │
│   │   │           PADDING               │   │   │  ← Space INSIDE the box
│   │   │   ┌─────────────────────────┐   │   │   │
│   │   │   │                         │   │   │   │
│   │   │   │        CONTENT          │   │   │   │  ← Actual text/image
│   │   │   │                         │   │   │   │
│   │   │   └─────────────────────────┘   │   │   │
│   │   │                                 │   │   │
│   │   └─────────────────────────────────┘   │   │
│   │                                         │   │
│   └─────────────────────────────────────────┘   │
│                                                 │
└─────────────────────────────────────────────────┘
```

### The Four Layers Explained:

| Layer | What It Is | Analogy |
|-------|-----------|---------|
| **Content** | The actual text, image, or child elements | The item in the package |
| **Padding** | Space BETWEEN content and border (inside the box) | Bubble wrap around the item |
| **Border** | The visible edge/outline of the box | The cardboard box itself |
| **Margin** | Space OUTSIDE the border (between this box and others) | Space between packages on a shelf |

---

## 2.2 Content — Width & Height

The **content area** is the innermost layer. By default, `width` and `height` only control the content area.

### Setting Width and Height:

```css
.box {
    width: 300px;
    height: 200px;
}
```

### Width Values:

```css
/* Fixed width in pixels */
.box { width: 400px; }

/* Percentage of parent element */
.box { width: 80%; }

/* Full width of parent (default for block elements) */
.box { width: 100%; }

/* Automatic — browser calculates */
.box { width: auto; }

/* Maximum and minimum width */
.box {
    width: 80%;
    max-width: 1200px;    /* Never wider than 1200px */
    min-width: 300px;     /* Never narrower than 300px */
}
```

### Height Values:

```css
/* Fixed height */
.box { height: 400px; }

/* Percentage (requires parent to have defined height) */
.box { height: 100%; }

/* Automatic — grows with content (default) */
.box { height: auto; }

/* Maximum and minimum height */
.box {
    min-height: 200px;     /* At least 200px tall */
    max-height: 600px;     /* No taller than 600px */
}
```

### Important Default Behaviors:

```
BLOCK elements (div, p, section, etc.):
✅ Width: 100% of parent by default
✅ Height: auto (grows with content) by default

INLINE elements (span, a, strong, etc.):
✅ Width: Only as wide as content
✅ Height: Only as tall as content
❌ Width and height properties are IGNORED
```

> 📌 **Key Insight:** Block elements stretch to fill their parent's width. Inline elements only take the space they need. You CANNOT set `width` or `height` on inline elements.

---

## 2.3 Padding — Inner Spacing

**Padding** is the space **between the content and the border**. It pushes the content inward, creating breathing room inside the box.

### Syntax:

```css
.box {
    padding: 20px;    /* 20px on ALL four sides */
}
```

### Individual Sides:

```css
.box {
    padding-top: 10px;
    padding-right: 20px;
    padding-bottom: 10px;
    padding-left: 20px;
}
```

### Shorthand Notation:

```css
/* All four sides the same */
.box { padding: 20px; }

/* Vertical | Horizontal */
.box { padding: 10px 20px; }
/*          top/bottom  left/right */

/* Top | Horizontal | Bottom */
.box { padding: 10px 20px 30px; }
/*         top   left/right  bottom */

/* Top | Right | Bottom | Left (clockwise) */
.box { padding: 10px 20px 30px 40px; }
/*         top   right  bottom  left */
```

### Clockwise Memory Trick:

```
Think of a clock starting at 12:

         TOP (12 o'clock)
          ↓
    padding: 10px   20px   30px   40px;
              ↑       ↑      ↑      ↑
             TOP   RIGHT  BOTTOM  LEFT

         T R B L → Think: "TRouBLe"
```

### Visual Example:

```css
.card {
    padding: 20px;
    background-color: lightblue;
}
```

```
WITHOUT padding:                WITH padding: 20px;
┌──────────────────┐            ┌──────────────────────┐
│Hello World       │            │                      │
└──────────────────┘            │   Hello World        │
                                │                      │
Content touches the edges       └──────────────────────┘
                                Content has breathing room
```

### Real-World Padding Examples:

```css
/* Card with comfortable inner spacing */
.card {
    padding: 25px 30px;
}

/* Button with horizontal emphasis */
.button {
    padding: 12px 30px;
}

/* Section with generous vertical spacing */
.section {
    padding: 60px 20px;
}

/* Header with balanced spacing */
header {
    padding: 20px 40px;
}
```

> 📌 **Key Point:** Padding has a **background color** — it shares the element's `background-color`. This is different from margin, which is always transparent.

---

## 2.4 Border — The Box Edge

**Border** is the visible edge around the box, sitting between padding and margin.

### Syntax:

```css
.box {
    border: 2px solid #333;
    /*      width  style  color */
}
```

### Border Shorthand:

```css
/* width | style | color */
.box { border: 1px solid black; }
.box { border: 3px dashed #3498db; }
.box { border: 2px dotted red; }
```

### Individual Properties:

```css
.box {
    border-width: 2px;
    border-style: solid;
    border-color: #333;
}
```

### Individual Sides:

```css
.box {
    border-top: 2px solid red;
    border-right: 1px dashed gray;
    border-bottom: 3px solid blue;
    border-left: none;
}
```

### Border Styles:

| Style | Appearance |
|-------|-----------|
| `solid` | ─────────── (continuous line) |
| `dashed` | - - - - - - (dashed line) |
| `dotted` | · · · · · · · (dotted line) |
| `double` | ═══════════ (double line) |
| `groove` | 3D grooved effect |
| `ridge` | 3D ridged effect |
| `inset` | 3D inset effect |
| `outset` | 3D outset effect |
| `none` | No border |
| `hidden` | Hidden border |

### `border-radius` — Rounded Corners:

```css
/* All corners the same */
.box { border-radius: 10px; }

/* Circle (if width = height) */
.avatar { 
    width: 100px;
    height: 100px;
    border-radius: 50%;     /* Makes a perfect circle */
}

/* Individual corners */
.box {
    border-top-left-radius: 10px;
    border-top-right-radius: 10px;
    border-bottom-right-radius: 0;
    border-bottom-left-radius: 0;
}

/* Shorthand: top-left | top-right | bottom-right | bottom-left */
.box { border-radius: 10px 10px 0 0; }
```

### Visual Examples:

```
border-radius: 0;          border-radius: 10px;      border-radius: 50%;
┌──────────────┐            ╭──────────────╮           ╭──────────╮
│              │            │              │          ╭│          │╮
│              │            │              │          │            │
│              │            │              │          ╰│          │╯
└──────────────┘            ╰──────────────╯           ╰──────────╯
Square corners              Rounded corners            Circle/Pill
```

### Real-World Border Examples:

```css
/* Card with subtle border */
.card {
    border: 1px solid #e0e0e0;
    border-radius: 8px;
}

/* Left accent border */
.alert {
    border-left: 4px solid #e74c3c;
    padding: 15px;
}

/* Bottom border as separator */
.nav-item {
    border-bottom: 2px solid transparent;
}

.nav-item:hover {
    border-bottom: 2px solid #3498db;
}

/* Profile image circle */
.avatar {
    width: 80px;
    height: 80px;
    border-radius: 50%;
    border: 3px solid #3498db;
}

/* Button with rounded corners */
.button {
    border: 2px solid #3498db;
    border-radius: 25px;
    padding: 10px 30px;
}
```

---

## 2.5 Margin — Outer Spacing

**Margin** is the space **outside the border**. It creates distance between the element and its neighbors.

### Syntax:

```css
.box {
    margin: 20px;    /* 20px on ALL four sides */
}
```

### Individual Sides:

```css
.box {
    margin-top: 10px;
    margin-right: 20px;
    margin-bottom: 30px;
    margin-left: 20px;
}
```

### Shorthand (Same as Padding):

```css
/* All sides */
.box { margin: 20px; }

/* Vertical | Horizontal */
.box { margin: 10px 20px; }

/* Top | Right | Bottom | Left (TRouBLe) */
.box { margin: 10px 20px 30px 40px; }
```

### `margin: auto` — Centering Block Elements:

```css
/* CENTER a block element horizontally */
.container {
    width: 800px;
    margin: 0 auto;
    /*     top/bottom  left/right
           0px         auto (split remaining space equally) */
}
```

```
Full browser width: 1200px
┌──────────────────────────────────────────────────────┐
│   auto margin    ┌──────────────────┐   auto margin  │
│   (200px)        │  .container      │   (200px)      │
│                  │  width: 800px    │                 │
│                  └──────────────────┘                 │
└──────────────────────────────────────────────────────┘
         ↑                                    ↑
    Equal space                          Equal space
    = CENTERED!
```

> 📌 **Key Technique:** `margin: 0 auto;` is the classic way to center a block element horizontally. The element MUST have a defined `width`.

### Margin Collapse — The Tricky Part:

When two vertical margins **touch**, they **collapse** into one — only the **larger** margin is used.

```css
.paragraph-1 {
    margin-bottom: 30px;    /* 30px bottom margin */
}

.paragraph-2 {
    margin-top: 20px;       /* 20px top margin */
}
```

```
Expected: 30px + 20px = 50px gap between them
Actual:   30px gap (the larger margin wins!)

This ONLY happens with VERTICAL margins (top/bottom).
Horizontal margins (left/right) do NOT collapse.
```

### Visual Demonstration of Margin Collapse:

```
WITHOUT collapse (what you might expect):
┌──────────────────┐
│   Paragraph 1    │
└──────────────────┘
        30px
        ────
        20px
┌──────────────────┐
│   Paragraph 2    │
└──────────────────┘
     Total: 50px

WITH collapse (what actually happens):
┌──────────────────┐
│   Paragraph 1    │
└──────────────────┘
        30px        ← Larger margin wins
┌──────────────────┐
│   Paragraph 2    │
└──────────────────┘
     Total: 30px
```

> 📌 **Remember:** Margin collapse is a feature, not a bug. It prevents excessive spacing between elements.

### Negative Margins:

```css
/* Negative margins pull elements CLOSER or OVERLAP them */
.box {
    margin-top: -10px;     /* Pulls element UP by 10px */
    margin-left: -20px;    /* Pulls element LEFT by 20px */
}
```

> ⚠️ Use negative margins sparingly. They can cause overlapping and unexpected layouts.

### Padding vs Margin — The Key Differences:

| Feature | Padding | Margin |
|---------|---------|--------|
| **Position** | INSIDE the border | OUTSIDE the border |
| **Background** | ✅ Shares element's background color | ❌ Always transparent |
| **Collapse** | ❌ Never collapses | ✅ Vertical margins collapse |
| **Negative values** | ❌ Cannot be negative | ✅ Can be negative |
| **Purpose** | Inner breathing room | Space between elements |
| **Click area** | ✅ Part of clickable area | ❌ Not clickable |
| **Auto value** | Not commonly used | ✅ `margin: auto` for centering |

### Visual Comparison:

```
Padding pushes content INWARD:
┌────────────────────────┐
│ ░░░░░░░░░░░░░░░░░░░░░░ │  ← Padding (has background color)
│ ░░ ┌──────────────┐ ░░ │
│ ░░ │   Content     │ ░░ │
│ ░░ └──────────────┘ ░░ │
│ ░░░░░░░░░░░░░░░░░░░░░░ │
└────────────────────────┘

Margin pushes other elements OUTWARD:
                                    ← Margin (transparent)
┌────────────────────────┐
│       Content          │
└────────────────────────┘
                                    ← Margin (transparent)
┌────────────────────────┐
│       Content          │
└────────────────────────┘
```

---

## 2.6 The `box-sizing` Property — Fixing the Box Model

By default, `width` and `height` ONLY control the **content area**. Padding and border are **added on top**, making the box BIGGER than expected.

### The Problem (Default Box Sizing):

```css
.box {
    width: 300px;
    padding: 20px;
    border: 5px solid black;
}

/* ACTUAL rendered width:
   Content:    300px
   + Padding:  20px × 2 (left + right) = 40px
   + Border:   5px × 2 (left + right) = 10px
   ─────────────────
   TOTAL:      350px    ← WIDER than expected!
*/
```

```
You set width: 300px, but the box is actually 350px!

Expected:
|←────── 300px ──────→|

Actual:
|←──────────── 350px ──────────→|
|5px|20px|  300px content  |20px|5px|
 bdr  pad                   pad  bdr
```

### The Solution: `box-sizing: border-box`

With `border-box`, padding and border are **included WITHIN** the specified width/height:

```css
.box {
    width: 300px;
    padding: 20px;
    border: 5px solid black;
    box-sizing: border-box;
}

/* ACTUAL rendered width:
   TOTAL:     300px (padding and border are INCLUDED)
   Border:    5px × 2 = 10px
   Padding:   20px × 2 = 40px
   Content:   300px - 40px - 10px = 250px (auto-adjusted)
*/
```

```
With border-box, the total is exactly what you set:

|←────── 300px ──────→|
|5px|20px| 250px  |20px|5px|
 bdr  pad  content  pad  bdr

The box stays at 300px total!
```

### Comparison:

| Box Sizing | Width Calculation | Total Width |
|-----------|-------------------|-------------|
| `content-box` (default) | width + padding + border | **Bigger** than set width |
| `border-box` | width INCLUDES padding + border | **Exactly** the set width ✅ |

### The Universal Reset:

Every professional CSS file starts with this:

```css
/* Apply border-box to ALL elements */
*, *::before, *::after {
    box-sizing: border-box;
}
```

> 📌 **Rule:** ALWAYS include `box-sizing: border-box` in your CSS reset. It makes sizing predictable and is the **industry standard**.

### Complete CSS Reset Template:

```css
/* ==========================================
   CSS RESET — Include at the top of every project
   ========================================== */

*, *::before, *::after {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Poppins', Arial, sans-serif;
    font-size: 16px;
    line-height: 1.6;
    color: #333;
    background-color: #f9f9f9;
}

img {
    max-width: 100%;        /* Images never overflow their container */
    height: auto;           /* Maintain aspect ratio */
    display: block;         /* Remove inline image gap */
}

a {
    text-decoration: none;
    color: inherit;
}

ul, ol {
    list-style: none;       /* Remove default bullets/numbers */
}
```

---

## 2.7 Visualizing the Complete Box Model

Let's see all four layers working together with real numbers:

```css
.card {
    width: 300px;
    height: 200px;
    padding: 20px;
    border: 3px solid #3498db;
    margin: 15px;
    box-sizing: border-box;
}
```

### Visual Breakdown:

```
                    15px margin
        ┌───────────────────────────────┐
        │         3px border            │
        │   ┌───────────────────────┐   │
        │   │     20px padding      │   │
        │   │   ┌───────────────┐   │   │
   15px │   │   │               │   │   │ 15px
 margin │   │   │   CONTENT     │   │   │ margin
        │   │   │   254 × 154   │   │   │
        │   │   │               │   │   │
        │   │   └───────────────┘   │   │
        │   │                       │   │
        │   └───────────────────────┘   │
        │                               │
        └───────────────────────────────┘
                    15px margin

Total space occupied: 300 + 30 = 330px wide
                      200 + 30 = 230px tall
(Including margins on both sides)
```

### DevTools — Inspecting the Box Model:

> 💡 **Pro Tip:** You can see the box model of ANY element in your browser:
>
> 1. Right-click on any element on a webpage
> 2. Click **"Inspect"** (or press `F12`)
> 3. Select the element in the Elements panel
> 4. Look at the **"Computed"** tab or the **box model diagram**

```
Browser DevTools shows:
┌─────────────────────────────────┐
│  margin: 15                     │
│  ┌───────────────────────────┐  │
│  │  border: 3                │  │
│  │  ┌─────────────────────┐  │  │
│  │  │  padding: 20        │  │  │
│  │  │  ┌───────────────┐  │  │  │
│  │  │  │ 254 × 154     │  │  │  │
│  │  │  └───────────────┘  │  │  │
│  │  └─────────────────────┘  │  │
│  └───────────────────────────┘  │
└─────────────────────────────────┘
```

---

## 2.8 The `display` Property — Controlling Element Behavior

The `display` property is one of the **most important properties in CSS**. It controls how elements behave in the layout flow.

### `display: block`

Block elements take up the **full width** and stack **vertically**.

```css
.block-example {
    display: block;
}
```

```
Behavior:
✅ Takes full available width
✅ Starts on a new line
✅ Width and height CAN be set
✅ Margins and padding work on ALL sides

Default block elements: <div>, <p>, <h1>-<h6>, <section>, <article>, <header>, <footer>, <main>, <nav>, <ul>, <ol>, <li>, <form>
```

```
┌──────────────────────────────────────────┐
│  Block Element 1 (full width)            │
└──────────────────────────────────────────┘
┌──────────────────────────────────────────┐
│  Block Element 2 (full width)            │
└──────────────────────────────────────────┘
┌──────────────────────────────────────────┐
│  Block Element 3 (full width)            │
└──────────────────────────────────────────┘
```

### `display: inline`

Inline elements take up only the **width of their content** and sit **side by side**.

```css
.inline-example {
    display: inline;
}
```

```
Behavior:
✅ Takes only content width
✅ Sits in the same line as neighbors
❌ Width and height are IGNORED
❌ Vertical margin/padding DON'T push other elements

Default inline elements: <span>, <a>, <strong>, <em>, <img>, <code>, <small>
```

```
┌─────────┐┌───────────┐┌─────────────┐
│ Inline 1 ││ Inline 2   ││ Inline 3     │
└─────────┘└───────────┘└─────────────┘
 (all on the same line, side by side)
```

### `display: inline-block`

The **best of both worlds** — sits inline like inline elements, but allows width/height like block elements.

```css
.inline-block-example {
    display: inline-block;
}
```

```
Behavior:
✅ Sits in the same line as neighbors (like inline)
✅ Width and height CAN be set (like block)
✅ Margins and padding work on ALL sides (like block)
✅ Perfect for buttons, navigation items, cards side by side
```

```
┌──────────┐  ┌──────────┐  ┌──────────┐
│          │  │          │  │          │
│ Inline   │  │ Inline   │  │ Inline   │
│ Block 1  │  │ Block 2  │  │ Block 3  │
│          │  │          │  │          │
└──────────┘  └──────────┘  └──────────┘
(side by side, but with controllable width/height)
```

### Practical `inline-block` Example:

```html
<nav>
    <a href="/" class="nav-link">Home</a>
    <a href="/about" class="nav-link">About</a>
    <a href="/contact" class="nav-link">Contact</a>
</nav>
```

```css
.nav-link {
    display: inline-block;
    padding: 10px 20px;
    background-color: #3498db;
    color: white;
    margin-right: 5px;
}
```

### `display: none` — Hiding Elements

Completely **removes** the element from the page. It takes up NO space.

```css
.hidden {
    display: none;
}
```

```
Before display: none:
┌──────────┐
│ Element 1 │
└──────────┘
┌──────────┐
│ Element 2 │   ← This element
└──────────┘
┌──────────┐
│ Element 3 │
└──────────┘

After display: none on Element 2:
┌──────────┐
│ Element 1 │
└──────────┘
┌──────────┐
│ Element 3 │   ← Element 3 moves up, Element 2 is GONE
└──────────┘
```

### `display: none` vs `visibility: hidden`:

```css
/* display: none — Element is REMOVED from layout */
.gone {
    display: none;
    /* Takes NO space. Other elements fill the gap. */
}

/* visibility: hidden — Element is INVISIBLE but still takes space */
.invisible {
    visibility: hidden;
    /* Still takes space. Other elements don't move. */
}
```

```
display: none:              visibility: hidden:
┌──────────┐                ┌──────────┐
│ Element 1 │                │ Element 1 │
└──────────┘                └──────────┘
┌──────────┐                         
│ Element 3 │ ← moved up    (empty space where Element 2 was)
└──────────┘                
                            ┌──────────┐
                            │ Element 3 │ ← stays in place
                            └──────────┘
```

### Complete Display Comparison:

| Display Value | Width | Height | Sits Inline? | New Line? | Common Use |
|--------------|-------|--------|-------------|-----------|-----------|
| `block` | ✅ Settable | ✅ Settable | ❌ No | ✅ Yes | Sections, divs, paragraphs |
| `inline` | ❌ Ignored | ❌ Ignored | ✅ Yes | ❌ No | Links, spans, text formatting |
| `inline-block` | ✅ Settable | ✅ Settable | ✅ Yes | ❌ No | Buttons, nav items, cards |
| `none` | N/A | N/A | N/A | N/A | Hiding elements |

### Converting Between Display Types:

```css
/* Make a <span> behave like a block element */
span {
    display: block;
    width: 200px;
    height: 50px;
}

/* Make a <div> sit inline */
div.tag {
    display: inline-block;
    padding: 5px 10px;
}

/* Make a <li> sit horizontally */
nav li {
    display: inline-block;
}
```

---

## 2.9 Overflow — Handling Content That Doesn't Fit

When content is **bigger than its container**, you need to control what happens. The `overflow` property handles this.

### Syntax:

```css
.box {
    width: 300px;
    height: 150px;
    overflow: hidden;
}
```

### Overflow Values:

```css
/* visible — Content overflows and IS visible (DEFAULT) */
.box { overflow: visible; }

/* hidden — Content is CLIPPED at the box edge */
.box { overflow: hidden; }

/* scroll — ALWAYS shows scrollbars */
.box { overflow: scroll; }

/* auto — Shows scrollbars ONLY when content overflows */
.box { overflow: auto; }
```

### Visual Comparison:

```
overflow: visible (default)     overflow: hidden
┌──────────────────┐            ┌──────────────────┐
│  Lorem ipsum     │            │  Lorem ipsum     │
│  dolor sit amet, │            │  dolor sit amet, │
│  consectetur     │            │  consectetur     │
└──────────────────┘            └──────────────────┘
   adipiscing elit.             Content is CLIPPED here
   More text leaks              Nothing visible beyond
   outside the box!             the box boundary.


overflow: scroll                overflow: auto
┌──────────────────┤ ▲          ┌──────────────────┤ ▲
│  Lorem ipsum     │ █          │  Lorem ipsum     │ █
│  dolor sit amet, │ █          │  dolor sit amet, │ █
│  consectetur     │ █          │  consectetur     │ █
└──────────────────┤ ▼          └──────────────────┤ ▼
Always has scrollbars           Scrollbar appears ONLY
(even if content fits)          when content overflows
```

### Controlling X and Y Overflow Separately:

```css
.box {
    overflow-x: auto;      /* Horizontal scrollbar when needed */
    overflow-y: hidden;    /* Clip vertical overflow */
}
```

### Real-World Overflow Use Cases:

```css
/* Card with fixed height — clip overflowing text */
.card {
    height: 200px;
    overflow: hidden;
}

/* Scrollable code block */
.code-block {
    max-height: 300px;
    overflow: auto;
    background-color: #1e1e1e;
    padding: 15px;
}

/* Chat message container */
.chat-window {
    height: 400px;
    overflow-y: auto;    /* Vertical scroll for messages */
    overflow-x: hidden;  /* No horizontal scroll */
}

/* Image container — hide overflow for rounded corners */
.image-container {
    border-radius: 10px;
    overflow: hidden;      /* Clips image to rounded border */
}
```

---

## 2.10 CSS Backgrounds

Backgrounds add visual depth and style to elements. CSS provides powerful background controls.

### `background-color`:

```css
body { background-color: #f9f9f9; }
header { background-color: #2c3e50; }
.card { background-color: white; }
.highlight { background-color: rgba(255, 255, 0, 0.3); }
```

### `background-image`:

```css
.hero {
    background-image: url('images/hero-bg.jpg');
}

/* Multiple backgrounds */
.section {
    background-image: url('images/pattern.png'), url('images/bg.jpg');
}
```

### `background-repeat`:

```css
/* Don't repeat the image */
.hero { background-repeat: no-repeat; }

/* Repeat horizontally only */
.banner { background-repeat: repeat-x; }

/* Repeat vertically only */
.sidebar { background-repeat: repeat-y; }

/* Repeat both ways (default) */
.pattern { background-repeat: repeat; }
```

### `background-position`:

```css
.hero {
    background-position: center center;  /* Centered */
}

/* Other values */
.box { background-position: top left; }
.box { background-position: bottom right; }
.box { background-position: center top; }
.box { background-position: 50% 30%; }       /* Percentage */
.box { background-position: 100px 200px; }   /* Pixel offset */
```

### `background-size`:

```css
/* Cover — fills entire container (may crop) */
.hero { background-size: cover; }

/* Contain — fits entire image (may leave space) */
.hero { background-size: contain; }

/* Specific dimensions */
.hero { background-size: 300px 200px; }

/* Percentage */
.hero { background-size: 100% auto; }
```

### Visual Comparison:

```
background-size: cover          background-size: contain
┌─────────────────────┐         ┌─────────────────────┐
│ ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│         │                     │
│ ▓▓▓  IMAGE FILLS  ▓▓│         │   ┌─────────────┐   │
│ ▓▓▓  ENTIRE BOX   ▓▓│         │   │   IMAGE     │   │
│ ▓▓▓  (may crop)   ▓▓│         │   │   FITS      │   │
│ ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│         │   │   INSIDE    │   │
└─────────────────────┘         │   └─────────────┘   │
Fills box, may crop edges       └─────────────────────┘
                                Fits inside, may leave space
```

### `background-attachment`:

```css
/* Image scrolls WITH the page (default) */
.hero { background-attachment: scroll; }

/* Image stays FIXED while page scrolls (parallax effect) */
.hero { background-attachment: fixed; }
```

### Background Shorthand:

```css
/* Shorthand: color image repeat position/size */
.hero {
    background: #2c3e50 url('images/hero.jpg') no-repeat center center / cover;
}

/* This is equivalent to: */
.hero {
    background-color: #2c3e50;
    background-image: url('images/hero.jpg');
    background-repeat: no-repeat;
    background-position: center center;
    background-size: cover;
}
```

### Real-World Hero Section:

```css
.hero {
    background: linear-gradient(rgba(0, 0, 0, 0.5), rgba(0, 0, 0, 0.5)),
                url('images/hero-bg.jpg') no-repeat center center / cover;
    color: white;
    text-align: center;
    padding: 100px 20px;
    min-height: 400px;
}
```

> 📌 This creates a **dark overlay** over the background image, making text readable. The `linear-gradient` acts as a semi-transparent layer on top.

### Gradient Backgrounds:

```css
/* Linear gradient (top to bottom) */
.header {
    background: linear-gradient(to bottom, #3498db, #2c3e50);
}

/* Left to right */
.banner {
    background: linear-gradient(to right, #e74c3c, #f39c12);
}

/* Diagonal */
.box {
    background: linear-gradient(135deg, #667eea, #764ba2);
}

/* Multi-color */
.rainbow {
    background: linear-gradient(to right, red, orange, yellow, green, blue, purple);
}
```

---

## 2.11 Building Basic Page Sections

Let's combine everything to build a **real page layout** with header, content, and footer:

### `index.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Box Model Practice - MyWebsite</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="css/style.css">
</head>
<body>

    <!-- Header Section -->
    <header class="site-header">
        <h1>MyWebsite</h1>
        <p>Beautiful websites, built with CSS</p>
    </header>

    <!-- Navigation -->
    <nav class="main-nav">
        <a href="#" class="nav-link">Home</a>
        <a href="#" class="nav-link">About</a>
        <a href="#" class="nav-link">Services</a>
        <a href="#" class="nav-link">Portfolio</a>
        <a href="#" class="nav-link">Contact</a>
    </nav>

    <!-- Hero Section -->
    <section class="hero">
        <h2>Build Something Amazing</h2>
        <p>We create modern, responsive websites that help businesses grow.</p>
    </section>

    <!-- Main Content -->
    <main class="content-wrapper">

        <!-- About Section -->
        <section class="content-section">
            <h2>About Us</h2>
            <p>We are a team of passionate web developers dedicated to creating 
               beautiful, functional websites. With years of experience in HTML, 
               CSS, and JavaScript, we bring your vision to life.</p>
        </section>

        <!-- Services Cards -->
        <section class="content-section">
            <h2>Our Services</h2>

            <div class="card">
                <h3>Web Design</h3>
                <p>Beautiful, modern designs that capture your brand identity 
                   and engage your audience from the first click.</p>
            </div>

            <div class="card">
                <h3>Web Development</h3>
                <p>Clean, efficient code built with the latest web technologies. 
                   Fast, accessible, and SEO-friendly.</p>
            </div>

            <div class="card">
                <h3>SEO Optimization</h3>
                <p>Improve your search engine rankings and drive more organic 
                   traffic to your website with our proven strategies.</p>
            </div>
        </section>

        <!-- Stats Section -->
        <section class="stats-section">
            <div class="stat-box">
                <h3>150+</h3>
                <p>Projects Completed</p>
            </div>
            <div class="stat-box">
                <h3>50+</h3>
                <p>Happy Clients</p>
            </div>
            <div class="stat-box">
                <h3>5+</h3>
                <p>Years Experience</p>
            </div>
        </section>

        <!-- Call to Action -->
        <section class="cta-section">
            <h2>Ready to Get Started?</h2>
            <p>Contact us today and let us build something amazing together.</p>
            <a href="#" class="cta-button">Get In Touch</a>
        </section>

    </main>

    <!-- Footer -->
    <footer class="site-footer">
        <p>Follow us: Twitter | GitHub | LinkedIn</p>
        <p>&copy; 2025 MyWebsite. All rights reserved.</p>
    </footer>

</body>
</html>
```

### `css/style.css`:

```css
/* ==========================================
   CSS RESET
   ========================================== */

*, *::before, *::after {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

img {
    max-width: 100%;
    height: auto;
    display: block;
}

a {
    text-decoration: none;
    color: inherit;
}

ul, ol {
    list-style: none;
}


/* ==========================================
   GLOBAL STYLES
   ========================================== */

body {
    font-family: 'Inter', Arial, sans-serif;
    font-size: 16px;
    line-height: 1.7;
    color: #333;
    background-color: #f0f2f5;
}


/* ==========================================
   HEADER
   ========================================== */

.site-header {
    background-color: #1a1a2e;
    color: white;
    text-align: center;
    padding: 40px 20px;
}

.site-header h1 {
    font-size: 2.5rem;
    font-weight: 700;
    margin-bottom: 8px;
    letter-spacing: 2px;
}

.site-header p {
    font-size: 1.1rem;
    font-weight: 300;
    color: rgba(255, 255, 255, 0.7);
}


/* ==========================================
   NAVIGATION
   ========================================== */

.main-nav {
    background-color: #16213e;
    text-align: center;
    padding: 15px 20px;
}

.nav-link {
    display: inline-block;
    color: #e0e0e0;
    padding: 8px 20px;
    margin: 0 5px;
    font-size: 0.95rem;
    text-transform: uppercase;
    letter-spacing: 1px;
    border-radius: 4px;
}

.nav-link:hover {
    background-color: rgba(255, 255, 255, 0.1);
    color: #64b5f6;
}


/* ==========================================
   HERO SECTION
   ========================================== */

.hero {
    background: linear-gradient(135deg, #667eea, #764ba2);
    color: white;
    text-align: center;
    padding: 80px 20px;
}

.hero h2 {
    font-size: 2.2rem;
    font-weight: 700;
    margin-bottom: 15px;
    color: white;
}

.hero p {
    font-size: 1.2rem;
    font-weight: 300;
    color: rgba(255, 255, 255, 0.9);
    max-width: 600px;
    margin: 0 auto;
}


/* ==========================================
   MAIN CONTENT WRAPPER
   ========================================== */

.content-wrapper {
    max-width: 900px;
    margin: 0 auto;
    padding: 40px 20px;
}


/* ==========================================
   CONTENT SECTIONS
   ========================================== */

.content-section {
    margin-bottom: 40px;
}

.content-section h2 {
    font-size: 1.8rem;
    font-weight: 600;
    color: #1a1a2e;
    margin-bottom: 15px;
    padding-bottom: 10px;
    border-bottom: 3px solid #667eea;
}

.content-section p {
    color: #555;
    margin-bottom: 15px;
}


/* ==========================================
   CARDS
   ========================================== */

.card {
    background-color: #ffffff;
    border: 1px solid #e0e0e0;
    border-radius: 8px;
    padding: 25px 30px;
    margin-bottom: 20px;
    border-left: 5px solid #667eea;
}

.card h3 {
    font-size: 1.3rem;
    font-weight: 600;
    color: #1a1a2e;
    margin-bottom: 10px;
}

.card p {
    font-size: 0.95rem;
    color: #666;
}

.card:hover {
    border-left-color: #764ba2;
    background-color: #fafafe;
}


/* ==========================================
   STATS SECTION
   ========================================== */

.stats-section {
    text-align: center;
    margin-bottom: 40px;
    padding: 40px 0;
}

.stat-box {
    display: inline-block;
    width: 200px;
    padding: 25px;
    margin: 10px 15px;
    background-color: #ffffff;
    border-radius: 10px;
    border: 1px solid #e0e0e0;
}

.stat-box h3 {
    font-size: 2.2rem;
    font-weight: 700;
    color: #667eea;
    margin-bottom: 5px;
}

.stat-box p {
    font-size: 0.9rem;
    color: #888;
    text-transform: uppercase;
    letter-spacing: 1px;
}


/* ==========================================
   CALL TO ACTION
   ========================================== */

.cta-section {
    background-color: #1a1a2e;
    color: white;
    text-align: center;
    padding: 50px 30px;
    border-radius: 10px;
    margin-bottom: 40px;
}

.cta-section h2 {
    font-size: 1.8rem;
    color: white;
    margin-bottom: 10px;
    border-bottom: none;
}

.cta-section p {
    color: rgba(255, 255, 255, 0.7);
    margin-bottom: 25px;
}

.cta-button {
    display: inline-block;
    background-color: #667eea;
    color: white;
    padding: 14px 40px;
    border-radius: 30px;
    font-size: 1rem;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 1px;
}

.cta-button:hover {
    background-color: #764ba2;
}


/* ==========================================
   FOOTER
   ========================================== */

.site-footer {
    background-color: #1a1a2e;
    color: rgba(255, 255, 255, 0.6);
    text-align: center;
    padding: 30px 20px;
}

.site-footer p {
    font-size: 0.9rem;
    color: rgba(255, 255, 255, 0.6);
    margin-bottom: 5px;
}
```

### What This Project Demonstrates:

```
✅ CSS Reset with box-sizing: border-box
✅ Content width, max-width, height
✅ Padding on all elements (cards, sections, buttons)
✅ Borders with radius, accent borders, bottom borders
✅ Margin for spacing, margin: auto for centering
✅ Display: inline-block for nav links and stat boxes
✅ Overflow control (max-width prevents overflow)
✅ Background colors, gradients, and RGBA transparency
✅ Hover effects on cards, links, and buttons
✅ Complete page sections: header, nav, hero, main, footer
✅ Clean, organized CSS with section comments
```

---

## 2.12 Summary & Key Takeaways

| Concept | Key Point |
|---------|-----------|
| Box Model | Every element = Content + Padding + Border + Margin |
| Content | The actual text/image; controlled by `width` and `height` |
| Padding | Inner spacing between content and border; has background color |
| Border | Visible edge with style, width, color; `border-radius` for rounding |
| Margin | Outer spacing between elements; transparent; can be `auto` for centering |
| `box-sizing: border-box` | Padding + border included in width/height — ALWAYS use it |
| Margin Collapse | Vertical margins merge; larger one wins |
| `margin: 0 auto` | Centers block elements horizontally (needs defined width) |
| `display: block` | Full width, new line, settable dimensions |
| `display: inline` | Content width only, same line, dimensions ignored |
| `display: inline-block` | Same line + settable dimensions (best of both) |
| `display: none` | Completely removes element from layout |
| `overflow` | Controls content that exceeds container: `hidden`, `scroll`, `auto` |
| `background-color` | Solid color background |
| `background-image` | Image background with repeat, position, size controls |
| `background-size: cover` | Image fills container (may crop) |
| Gradients | `linear-gradient()` for color transitions |
| CSS Reset | Universal reset for consistent cross-browser rendering |

---

> 🎉 **Congratulations!** You've completed Chapter 2. You now understand the CSS Box Model, spacing with padding and margin, display properties, overflow handling, backgrounds, and how to build real page sections!

---