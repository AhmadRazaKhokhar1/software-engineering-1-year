

# 📘 Chapter 2: DOM Mastery (HTML + JS Integration)

> **🟢 Making JavaScript "talk" to HTML — Understanding the DOM tree, selecting elements, reading and updating content, manipulating classes and styles, and creating/removing elements dynamically.**

---

## 2.1 What is the DOM?

The **DOM** stands for **Document Object Model**. It's the **bridge between HTML and JavaScript** — the way JS sees and interacts with your HTML page.

When the browser loads an HTML file, it creates a **tree-like structure** in memory representing every element on the page. This tree IS the DOM.

### HTML → DOM Conversion:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>My Page</title>
</head>
<body>
    <h1 id="title">Hello World</h1>
    <p class="intro">Welcome to my site.</p>
    <ul>
        <li>Item 1</li>
        <li>Item 2</li>
    </ul>
</body>
</html>
```

### The DOM Tree This Creates:

```
                        document
                           │
                        <html>
                       /       \
                  <head>       <body>
                    │         /    |     \
                 <title>   <h1>   <p>   <ul>
                    │        │     │    /    \
               "My Page"  "Hello" "Welcome" <li>  <li>
                          "World"  "to my"   │      │
                                   "site." "Item 1" "Item 2"
```

### Key DOM Concepts:

| Concept | Explanation |
|---------|-------------|
| **Document** | The entire HTML page — the root of the DOM tree |
| **Node** | Every item in the tree (elements, text, attributes) |
| **Element** | An HTML tag (`<h1>`, `<p>`, `<div>`) |
| **Text Node** | The text inside an element |
| **Parent** | Element that contains other elements |
| **Child** | Element inside a parent element |
| **Sibling** | Elements at the same level (share the same parent) |

### Relationships Visualized:

```html
<body>                      ← PARENT of h1, p, ul
    <h1>Hello</h1>          ← CHILD of body, SIBLING of p and ul
    <p>Welcome</p>          ← CHILD of body, SIBLING of h1 and ul
    <ul>                    ← CHILD of body, PARENT of li elements
        <li>Item 1</li>     ← CHILD of ul, SIBLING of next li
        <li>Item 2</li>     ← CHILD of ul, SIBLING of prev li
    </ul>
</body>
```

### Why the DOM Matters:

```
Without DOM: JavaScript can't access or change anything on the page.
With DOM:    JavaScript can read, modify, add, and remove ANY element.

HTML is STATIC — once loaded, it doesn't change.
The DOM is LIVE — JavaScript can modify it at any time.
```

> 📌 **Key Insight:** When you "change HTML with JavaScript," you're actually changing the **DOM**. The original HTML file stays the same. The browser renders whatever the DOM currently looks like.

---

## 2.2 The `document` Object

The `document` object is JavaScript's **entry point** to the DOM. Everything starts with `document`.

```javascript
// The document object represents the entire page
console.log(document);                 // The whole DOM
console.log(document.title);           // Page title
console.log(document.URL);            // Current page URL
console.log(document.body);           // The <body> element
console.log(document.head);           // The <head> element
console.log(document.documentElement); // The <html> element
```

### Changing the Page Title:

```javascript
document.title = "New Page Title!";
// The browser tab title changes immediately!
```

---

## 2.3 Selecting Elements — `querySelector`

Before you can change an element, you need to **select** (find) it in the DOM. `querySelector` is the modern, powerful way to do this.

### `querySelector()` — Select ONE Element:

Returns the **first element** that matches the CSS selector:

```javascript
// Select by TAG name
const heading = document.querySelector("h1");

// Select by CLASS name (use . like CSS)
const intro = document.querySelector(".intro");

// Select by ID (use # like CSS)
const title = document.querySelector("#title");

// Select by ATTRIBUTE
const emailInput = document.querySelector('input[type="email"]');

// Select NESTED elements (descendant selector)
const firstLink = document.querySelector("nav a");

// Select by COMPLEX selector
const activeItem = document.querySelector("nav ul li.active a");
```

### `querySelectorAll()` — Select ALL Matching Elements:

Returns a **NodeList** (array-like collection) of ALL matching elements:

```javascript
// Select ALL paragraphs
const allParagraphs = document.querySelectorAll("p");
console.log(allParagraphs);         // NodeList [p, p, p, ...]
console.log(allParagraphs.length);  // Number of <p> elements

// Select ALL elements with class "card"
const allCards = document.querySelectorAll(".card");

// Select ALL list items inside <nav>
const navItems = document.querySelectorAll("nav li");

// Select ALL links
const allLinks = document.querySelectorAll("a");
```

### Looping Through `querySelectorAll` Results:

```javascript
const items = document.querySelectorAll(".item");

// Method 1: forEach ✅ (recommended)
items.forEach((item) => {
    console.log(item.textContent);
});

// Method 2: for...of
for (const item of items) {
    console.log(item.textContent);
}

// Method 3: Traditional for loop
for (let i = 0; i < items.length; i++) {
    console.log(items[i].textContent);
}
```

### `querySelector` vs Older Methods:

```javascript
// ✅ MODERN (use these)
document.querySelector("#title");            // By ID
document.querySelector(".intro");            // By class
document.querySelector("nav a");             // By CSS selector
document.querySelectorAll(".card");          // All matching

// ❌ OLDER (still work but less flexible)
document.getElementById("title");            // By ID only
document.getElementsByClassName("intro");    // By class only
document.getElementsByTagName("p");          // By tag only
```

> 📌 **Rule:** Always use `querySelector` and `querySelectorAll`. They accept ANY CSS selector, making them far more powerful and consistent than the older methods.

### Practical Selection Examples:

```html
<header>
    <h1 id="site-title">My Website</h1>
    <nav>
        <ul class="nav-list">
            <li><a href="/" class="nav-link active">Home</a></li>
            <li><a href="/about" class="nav-link">About</a></li>
            <li><a href="/contact" class="nav-link">Contact</a></li>
        </ul>
    </nav>
</header>

<main>
    <section class="hero">
        <h2>Welcome</h2>
        <p class="hero-text">This is a great website.</p>
        <button class="btn btn-primary">Get Started</button>
    </section>

    <section class="cards">
        <article class="card">
            <h3>Card 1</h3>
            <p>Description 1</p>
        </article>
        <article class="card">
            <h3>Card 2</h3>
            <p>Description 2</p>
        </article>
    </section>
</main>
```

```javascript
// Select specific elements
const siteTitle = document.querySelector("#site-title");
const heroText = document.querySelector(".hero-text");
const primaryBtn = document.querySelector(".btn-primary");
const activeLink = document.querySelector(".nav-link.active");
const firstCard = document.querySelector(".card");

// Select multiple elements
const allNavLinks = document.querySelectorAll(".nav-link");
const allCards = document.querySelectorAll(".card");
const allCardTitles = document.querySelectorAll(".card h3");

// Checking what was selected
console.log(siteTitle);             // <h1 id="site-title">My Website</h1>
console.log(allNavLinks.length);    // 3
console.log(allCards.length);       // 2
```

---

## 2.4 Reading Element Content

Once you've selected an element, you can **read** its content.

### `textContent` — Get/Set Plain Text:

```javascript
const heading = document.querySelector("h1");

// READ text content
console.log(heading.textContent);    // "My Website"

// SET new text content
heading.textContent = "New Title!";
// The h1 now displays "New Title!" on the page
```

### `innerHTML` — Get/Set HTML Content:

```javascript
const container = document.querySelector(".hero");

// READ HTML content (includes tags)
console.log(container.innerHTML);
// "<h2>Welcome</h2><p class="hero-text">This is a great website.</p>..."

// SET new HTML content (can include HTML tags)
container.innerHTML = "<h2>Updated!</h2><p>New content with <strong>bold</strong> text.</p>";
```

### `textContent` vs `innerHTML`:

```html
<p id="demo">Hello <strong>World</strong></p>
```

```javascript
const demo = document.querySelector("#demo");

console.log(demo.textContent);   // "Hello World" (plain text only)
console.log(demo.innerHTML);     // "Hello <strong>World</strong>" (includes HTML tags)

// Setting with textContent — treats HTML as plain text
demo.textContent = "Hello <strong>World</strong>";
// Displays: Hello <strong>World</strong> (literal text, not bold!)

// Setting with innerHTML — parses HTML tags
demo.innerHTML = "Hello <strong>World</strong>";
// Displays: Hello World (with "World" bold!)
```

| Property | Reads | Writes | HTML Tags? |
|----------|-------|--------|-----------|
| `textContent` | Plain text only | Plain text only | ❌ Treated as text |
| `innerHTML` | Text + HTML tags | Text + HTML tags | ✅ Parsed as HTML |

> ⚠️ **Security Warning:** Never use `innerHTML` with user input! It can lead to **XSS (Cross-Site Scripting)** attacks:
> ```javascript
> // ❌ DANGEROUS — user could inject malicious code
> container.innerHTML = userInput;
>
> // ✅ SAFE — textContent escapes HTML
> container.textContent = userInput;
> ```

### Reading Attribute Values:

```javascript
const link = document.querySelector("a");
const img = document.querySelector("img");
const input = document.querySelector("input");

// Read attributes
console.log(link.href);              // Full URL
console.log(link.getAttribute("href")); // Exact attribute value
console.log(img.src);                // Image source
console.log(img.alt);                // Alt text
console.log(input.type);            // Input type
console.log(input.value);           // Current input value
console.log(input.placeholder);     // Placeholder text

// Set attributes
link.href = "https://google.com";
img.alt = "New alt text";
input.placeholder = "Enter your email...";

// setAttribute for any attribute
link.setAttribute("target", "_blank");
link.setAttribute("rel", "noopener noreferrer");
```

### Reading Input Values:

```html
<input type="text" id="username" value="Ali">
<input type="checkbox" id="agree" checked>
<select id="color">
    <option value="red">Red</option>
    <option value="blue" selected>Blue</option>
</select>
```

```javascript
const username = document.querySelector("#username");
const checkbox = document.querySelector("#agree");
const select = document.querySelector("#color");

// Read current values
console.log(username.value);     // "Ali"
console.log(checkbox.checked);   // true
console.log(select.value);      // "blue"

// Update values
username.value = "Sara";
checkbox.checked = false;
select.value = "red";
```

---

## 2.5 Manipulating CSS Classes

The **best way** to change an element's appearance with JavaScript is by **adding/removing CSS classes**. This keeps styling in CSS and behavior in JS — clean separation!

### The `classList` API:

```javascript
const card = document.querySelector(".card");

// ADD a class
card.classList.add("active");
// <article class="card active">

// REMOVE a class
card.classList.remove("active");
// <article class="card">

// TOGGLE a class (add if missing, remove if present)
card.classList.toggle("active");
// If "active" is absent → adds it
// If "active" is present → removes it

// CHECK if element has a class
console.log(card.classList.contains("active"));   // true or false

// REPLACE one class with another
card.classList.replace("card", "card-large");
// <article class="card-large active">
```

### Why `classList` is Better Than Direct Style:

```javascript
// ❌ BAD: Changing styles directly in JavaScript
element.style.backgroundColor = "blue";
element.style.color = "white";
element.style.padding = "20px";
element.style.borderRadius = "8px";
// Mixes CSS into JS — hard to maintain, override, or reuse

// ✅ GOOD: Toggle a CSS class
element.classList.add("highlighted");
// All styles are defined in CSS where they belong!
```

```css
/* All styling stays in CSS */
.highlighted {
    background-color: blue;
    color: white;
    padding: 20px;
    border-radius: 8px;
}
```

### Practical `classList` Examples:

```css
/* CSS Classes */
.hidden { display: none; }
.visible { display: block; }
.active { background-color: #3498db; color: white; }
.error { border-color: #e74c3c; color: #e74c3c; }
.success { border-color: #2ecc71; color: #2ecc71; }
.fade-in { opacity: 1; transition: opacity 0.3s ease; }
.fade-out { opacity: 0; transition: opacity 0.3s ease; }
```

```javascript
// Show/hide an element
const menu = document.querySelector(".mobile-menu");
menu.classList.toggle("hidden");

// Highlight active navigation link
const navLinks = document.querySelectorAll(".nav-link");
navLinks.forEach(link => link.classList.remove("active"));
navLinks[0].classList.add("active");

// Form validation visual feedback
const input = document.querySelector("#email");
if (input.value === "") {
    input.classList.add("error");
    input.classList.remove("success");
} else {
    input.classList.add("success");
    input.classList.remove("error");
}
```

---

## 2.6 Manipulating Inline Styles

Sometimes you need to set styles directly (for dynamic values like positions or calculated sizes). Use the `style` property.

### Setting Inline Styles:

```javascript
const box = document.querySelector(".box");

// Set individual styles
box.style.backgroundColor = "#3498db";     // CSS: background-color
box.style.color = "white";
box.style.padding = "20px";
box.style.borderRadius = "8px";            // CSS: border-radius
box.style.fontSize = "18px";              // CSS: font-size
box.style.width = "300px";
box.style.marginTop = "20px";             // CSS: margin-top
```

### CSS Property → JavaScript Property:

```
CSS uses hyphens:        JavaScript uses camelCase:
background-color    →    backgroundColor
font-size           →    fontSize
border-radius       →    borderRadius
margin-top          →    marginTop
text-align          →    textAlign
z-index             →    zIndex
box-shadow          →    boxShadow
```

### Reading Inline Styles:

```javascript
// Only reads INLINE styles (set via style attribute or JS)
console.log(box.style.backgroundColor);   // "#3498db" (if set inline)
console.log(box.style.padding);           // "20px" (if set inline)

// To read COMPUTED styles (from CSS files):
const computedStyles = window.getComputedStyle(box);
console.log(computedStyles.backgroundColor);  // "rgb(52, 152, 219)"
console.log(computedStyles.padding);          // "20px"
console.log(computedStyles.fontSize);         // "18px"
```

### When to Use Inline Styles vs Classes:

| Situation | Use | Example |
|-----------|-----|---------|
| Static visual changes | Classes ✅ | `.hidden`, `.active`, `.error` |
| Predefined state changes | Classes ✅ | `.dark-theme`, `.expanded` |
| Dynamic/calculated values | Inline style | `style.width = calculatedWidth + "px"` |
| Animation positions | Inline style | `style.transform = \`translateX(${x}px)\`` |
| User-chosen colors | Inline style | `style.backgroundColor = userPickedColor` |

### Practical Inline Style Example:

```javascript
// Dynamic progress bar
const progressBar = document.querySelector(".progress-fill");
const percentage = 75;

progressBar.style.width = `${percentage}%`;
progressBar.style.backgroundColor = percentage > 50 ? "#2ecc71" : "#e74c3c";

// Dynamic element positioning
const tooltip = document.querySelector(".tooltip");
tooltip.style.top = `${mouseY + 10}px`;
tooltip.style.left = `${mouseX + 10}px`;
```

---

## 2.7 Creating New Elements

JavaScript can **create brand new HTML elements** and add them to the page — no page reload needed!

### `document.createElement()` — Create an Element:

```javascript
// Step 1: Create the element
const newParagraph = document.createElement("p");

// Step 2: Add content and attributes
newParagraph.textContent = "This paragraph was created by JavaScript!";
newParagraph.classList.add("dynamic-text");
newParagraph.id = "new-para";

// Step 3: Add it to the page
document.body.appendChild(newParagraph);
// The new <p> is now at the END of <body>
```

### Adding Elements to Specific Locations:

```javascript
const container = document.querySelector(".container");
const newCard = document.createElement("article");
newCard.classList.add("card");
newCard.innerHTML = `
    <h3>New Card</h3>
    <p>This card was created dynamically!</p>
`;

// appendChild — adds as the LAST child
container.appendChild(newCard);

// prepend — adds as the FIRST child
container.prepend(newCard);

// before — adds BEFORE the target element
const existingCard = document.querySelector(".card");
existingCard.before(newCard);

// after — adds AFTER the target element
existingCard.after(newCard);

// insertAdjacentHTML — insert HTML string at specific position
container.insertAdjacentHTML("beforeend", `
    <article class="card">
        <h3>Another Card</h3>
        <p>Created with insertAdjacentHTML</p>
    </article>
`);
```

### `insertAdjacentHTML` Positions:

```html
<!-- beforebegin -->
<div class="container">
    <!-- afterbegin -->
    <p>Existing content</p>
    <!-- beforeend -->
</div>
<!-- afterend -->
```

```javascript
const container = document.querySelector(".container");

container.insertAdjacentHTML("beforebegin", "<p>Before the container</p>");
container.insertAdjacentHTML("afterbegin", "<p>First inside container</p>");
container.insertAdjacentHTML("beforeend", "<p>Last inside container</p>");
container.insertAdjacentHTML("afterend", "<p>After the container</p>");
```

### Building Complex Elements:

```javascript
// Create a full card component
const createCard = (title, description, tag) => {
    const card = document.createElement("article");
    card.classList.add("card");
    card.innerHTML = `
        <h3 class="card-title">${title}</h3>
        <p class="card-text">${description}</p>
        <span class="card-tag">${tag}</span>
    `;
    return card;
};

// Use it
const container = document.querySelector(".card-grid");
container.appendChild(createCard("HTML Basics", "Learn the foundation.", "HTML"));
container.appendChild(createCard("CSS Styling", "Make it beautiful.", "CSS"));
container.appendChild(createCard("JavaScript", "Make it interactive.", "JS"));
```

---

## 2.8 Removing Elements

### `remove()` — Remove an Element:

```javascript
const element = document.querySelector(".old-section");
element.remove();
// The element is completely removed from the DOM
```

### `removeChild()` — Remove a Child Element:

```javascript
const parent = document.querySelector("ul");
const child = document.querySelector("ul li:last-child");
parent.removeChild(child);
// The last <li> is removed from the <ul>
```

### Clear ALL Children of an Element:

```javascript
const container = document.querySelector(".container");

// Method 1: Set innerHTML to empty ✅
container.innerHTML = "";

// Method 2: Remove children one by one
while (container.firstChild) {
    container.removeChild(container.firstChild);
}
```

### Practical Example — Remove Item from List:

```html
<ul id="task-list">
    <li>Task 1 <button class="delete-btn">✕</button></li>
    <li>Task 2 <button class="delete-btn">✕</button></li>
    <li>Task 3 <button class="delete-btn">✕</button></li>
</ul>
```

```javascript
// Remove the <li> when delete button is clicked
const deleteButtons = document.querySelectorAll(".delete-btn");

deleteButtons.forEach(btn => {
    btn.addEventListener("click", () => {
        btn.parentElement.remove();
        // btn.parentElement is the <li> containing the button
    });
});
```

---

## 2.9 Navigating the DOM Tree

You can **move between elements** using DOM navigation properties.

### Parent, Children, Siblings:

```html
<ul id="list">
    <li>Item 1</li>
    <li class="active">Item 2</li>
    <li>Item 3</li>
</ul>
```

```javascript
const activeItem = document.querySelector(".active");

// Parent
console.log(activeItem.parentElement);           // <ul id="list">

// Children (from parent)
const list = document.querySelector("#list");
console.log(list.children);                      // HTMLCollection [li, li, li]
console.log(list.children.length);               // 3
console.log(list.firstElementChild);             // <li>Item 1</li>
console.log(list.lastElementChild);              // <li>Item 3</li>

// Siblings
console.log(activeItem.previousElementSibling);  // <li>Item 1</li>
console.log(activeItem.nextElementSibling);      // <li>Item 3</li>
```

### Navigation Properties Table:

| Property | Returns |
|----------|---------|
| `parentElement` | Parent element |
| `children` | All child elements (HTMLCollection) |
| `firstElementChild` | First child element |
| `lastElementChild` | Last child element |
| `previousElementSibling` | Previous sibling element |
| `nextElementSibling` | Next sibling element |
| `closest(".selector")` | Nearest ancestor matching the selector |

### `closest()` — Find Nearest Ancestor:

```html
<div class="card-grid">
    <article class="card">
        <h3>Title</h3>
        <p>Description</p>
        <button class="delete-btn">Delete</button>
    </article>
</div>
```

```javascript
const btn = document.querySelector(".delete-btn");

// Find the nearest ancestor with class "card"
const card = btn.closest(".card");
console.log(card);   // <article class="card">...</article>

// Very useful for event delegation (Chapter 3)
card.remove();   // Remove the entire card when button is clicked
```

> 📌 **`closest()` is incredibly useful** — it searches UP the DOM tree from the current element, finding the first ancestor that matches the selector. This is essential for event delegation patterns.

---

## 2.10 Real-World DOM Manipulation Patterns

### Pattern 1: Updating a Counter Display

```html
<div class="counter">
    <button id="decrement">-</button>
    <span id="count">0</span>
    <button id="increment">+</button>
</div>
```

```javascript
const countDisplay = document.querySelector("#count");
const incrementBtn = document.querySelector("#increment");
const decrementBtn = document.querySelector("#decrement");

let count = 0;

incrementBtn.addEventListener("click", () => {
    count++;
    countDisplay.textContent = count;
});

decrementBtn.addEventListener("click", () => {
    count--;
    countDisplay.textContent = count;
});
```

### Pattern 2: Dynamic List Builder

```html
<div class="list-builder">
    <input type="text" id="item-input" placeholder="Add an item...">
    <button id="add-btn">Add</button>
    <ul id="item-list"></ul>
</div>
```

```javascript
const input = document.querySelector("#item-input");
const addBtn = document.querySelector("#add-btn");
const list = document.querySelector("#item-list");

addBtn.addEventListener("click", () => {
    const text = input.value.trim();

    if (text === "") return;     // Don't add empty items

    // Create new list item with delete button
    const li = document.createElement("li");
    li.innerHTML = `
        ${text}
        <button class="delete-btn">✕</button>
    `;

    // Add delete functionality
    li.querySelector(".delete-btn").addEventListener("click", () => {
        li.remove();
    });

    // Add to list and clear input
    list.appendChild(li);
    input.value = "";
    input.focus();
});
```

### Pattern 3: Content Tab Switcher

```html
<div class="tabs">
    <button class="tab-btn active" data-tab="html">HTML</button>
    <button class="tab-btn" data-tab="css">CSS</button>
    <button class="tab-btn" data-tab="js">JavaScript</button>
</div>

<div class="tab-content">
    <div class="tab-panel active" id="html">
        <h3>HTML</h3>
        <p>HTML structures content on the web.</p>
    </div>
    <div class="tab-panel" id="css">
        <h3>CSS</h3>
        <p>CSS styles and layouts web pages.</p>
    </div>
    <div class="tab-panel" id="js">
        <h3>JavaScript</h3>
        <p>JavaScript adds interactivity.</p>
    </div>
</div>
```

```css
.tab-panel { display: none; }
.tab-panel.active { display: block; }
.tab-btn.active { background-color: #3498db; color: white; }
```

```javascript
const tabButtons = document.querySelectorAll(".tab-btn");
const tabPanels = document.querySelectorAll(".tab-panel");

tabButtons.forEach(btn => {
    btn.addEventListener("click", () => {
        // Remove active from all buttons and panels
        tabButtons.forEach(b => b.classList.remove("active"));
        tabPanels.forEach(p => p.classList.remove("active"));

        // Add active to clicked button and matching panel
        btn.classList.add("active");
        const targetPanel = document.querySelector(`#${btn.dataset.tab}`);
        targetPanel.classList.add("active");
    });
});
```

---

## 2.11 `data-*` Attributes — Custom Data in HTML

HTML5 allows you to store **custom data** on elements using `data-*` attributes. JavaScript accesses them via the `dataset` property.

### Setting Data Attributes in HTML:

```html
<article class="card" data-id="42" data-category="technology" data-author="Ali">
    <h3>Card Title</h3>
    <p>Card description</p>
</article>

<button data-action="delete" data-target="42">Delete</button>
```

### Reading Data Attributes in JavaScript:

```javascript
const card = document.querySelector(".card");

console.log(card.dataset.id);          // "42"
console.log(card.dataset.category);    // "technology"
console.log(card.dataset.author);      // "Ali"

// Setting data attributes
card.dataset.status = "published";
// Adds: data-status="published" to the element

// Using data attributes for functionality
const btn = document.querySelector("button");
console.log(btn.dataset.action);       // "delete"
console.log(btn.dataset.target);       // "42"
```

### Naming Convention:

```html
<!-- HTML: kebab-case with data- prefix -->
<div data-user-name="Ali" data-is-active="true" data-post-count="15"></div>
```

```javascript
// JavaScript: camelCase in dataset
const div = document.querySelector("div");
console.log(div.dataset.userName);     // "Ali"     (data-user-name → userName)
console.log(div.dataset.isActive);     // "true"    (data-is-active → isActive)
console.log(div.dataset.postCount);    // "15"      (data-post-count → postCount)
```

> 📌 **Key Use Case:** Data attributes are perfect for storing metadata that JavaScript needs but shouldn't be visible on the page — like IDs, categories, states, or configuration.

---

## 2.12 Mini-Project: Dynamic FAQ / Accordion 🏆

Let's build a **real interactive FAQ accordion** using everything from this chapter.

### File Structure:

```
faq-accordion/
├── index.html
├── css/
│   └── style.css
└── js/
    └── script.js
```

### `index.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FAQ Accordion — DOM Project</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="css/style.css">
</head>
<body>

    <main class="container">
        <header class="faq-header">
            <h1>Frequently Asked Questions</h1>
            <p>Find answers to common questions about our services.</p>
            <div class="controls">
                <button id="expand-all" class="control-btn">Expand All</button>
                <button id="collapse-all" class="control-btn">Collapse All</button>
            </div>
        </header>

        <section class="faq-list" id="faq-list">

            <article class="faq-item" data-category="general">
                <button class="faq-question">
                    <span>What is web development?</span>
                    <span class="faq-icon">+</span>
                </button>
                <div class="faq-answer">
                    <p>Web development is the process of building and maintaining 
                       websites. It includes web design, web content development, 
                       client-side scripting, server-side scripting, and network 
                       security configuration.</p>
                </div>
            </article>

            <article class="faq-item" data-category="courses">
                <button class="faq-question">
                    <span>Are the courses really free?</span>
                    <span class="faq-icon">+</span>
                </button>
                <div class="faq-answer">
                    <p>Yes! All our courses are completely free. We believe 
                       education should be accessible to everyone, regardless 
                       of financial situation. No credit card required, no 
                       hidden fees.</p>
                </div>
            </article>

            <article class="faq-item" data-category="courses">
                <button class="faq-question">
                    <span>Do I need prior experience?</span>
                    <span class="faq-icon">+</span>
                </button>
                <div class="faq-answer">
                    <p>Not at all! Our courses are designed for absolute beginners. 
                       We start from the very basics and build up step by step. 
                       All you need is a computer, a text editor, and enthusiasm.</p>
                </div>
            </article>

            <article class="faq-item" data-category="technical">
                <button class="faq-question">
                    <span>What software do I need?</span>
                    <span class="faq-icon">+</span>
                </button>
                <div class="faq-answer">
                    <p>You need a modern web browser (we recommend Chrome or Firefox) 
                       and a code editor (we recommend Visual Studio Code — it's free). 
                       That's it! No expensive software required.</p>
                </div>
            </article>

            <article class="faq-item" data-category="general">
                <button class="faq-question">
                    <span>How long does each course take?</span>
                    <span class="faq-icon">+</span>
                </button>
                <div class="faq-answer">
                    <p>Each course is self-paced, but here are typical timelines: 
                       HTML (4 weeks), CSS (6 weeks), JavaScript (8 weeks). 
                       You can go faster or slower depending on your schedule 
                       and prior knowledge.</p>
                </div>
            </article>

            <article class="faq-item" data-category="technical">
                <button class="faq-question">
                    <span>Will I get a certificate?</span>
                    <span class="faq-icon">+</span>
                </button>
                <div class="faq-answer">
                    <p>We provide a completion certificate for each course. 
                       However, the real value is in the skills and projects 
                       you build — those speak louder than any certificate 
                       in the industry.</p>
                </div>
            </article>

        </section>

        <footer class="faq-footer">
            <p>Still have questions? <a href="#">Contact us</a></p>
            <p id="faq-count"></p>
        </footer>
    </main>

    <script src="js/script.js"></script>
</body>
</html>
```

### `css/style.css`:

```css
/* ==========================================
   CSS RESET & VARIABLES
   ========================================== */

*, *::before, *::after {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

:root {
    --primary: #667eea;
    --primary-dark: #5a6fd6;
    --dark: #1a1a2e;
    --text: #333;
    --text-light: #777;
    --bg: #f4f6f9;
    --white: #fff;
    --border: #e2e8f0;
    --shadow: 0 2px 10px rgba(0, 0, 0, 0.06);
    --shadow-lg: 0 8px 25px rgba(0, 0, 0, 0.1);
    --radius: 10px;
    --transition: all 0.3s ease;
    --font: 'Inter', sans-serif;
}

body {
    font-family: var(--font);
    font-size: 16px;
    line-height: 1.6;
    color: var(--text);
    background-color: var(--bg);
    min-height: 100vh;
    padding: 40px 20px;
}

/* ==========================================
   CONTAINER
   ========================================== */

.container {
    max-width: 750px;
    margin: 0 auto;
}

/* ==========================================
   HEADER
   ========================================== */

.faq-header {
    text-align: center;
    margin-bottom: 40px;
}

.faq-header h1 {
    font-size: clamp(1.5rem, 4vw, 2.2rem);
    color: var(--dark);
    margin-bottom: 8px;
}

.faq-header p {
    color: var(--text-light);
    font-size: 1rem;
    margin-bottom: 20px;
}

.controls {
    display: flex;
    justify-content: center;
    gap: 10px;
}

.control-btn {
    padding: 8px 20px;
    font-size: 0.85rem;
    font-family: var(--font);
    font-weight: 500;
    color: var(--primary);
    background: transparent;
    border: 1.5px solid var(--primary);
    border-radius: 20px;
    cursor: pointer;
    transition: var(--transition);
}

.control-btn:hover {
    background-color: var(--primary);
    color: var(--white);
}

/* ==========================================
   FAQ ITEMS
   ========================================== */

.faq-item {
    background-color: var(--white);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    margin-bottom: 12px;
    overflow: hidden;
    box-shadow: var(--shadow);
    transition: var(--transition);
}

.faq-item:hover {
    box-shadow: var(--shadow-lg);
}

.faq-item.active {
    border-color: var(--primary);
}

/* ==========================================
   QUESTION BUTTON
   ========================================== */

.faq-question {
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 20px 25px;
    font-size: 1rem;
    font-family: var(--font);
    font-weight: 600;
    color: var(--dark);
    background: none;
    border: none;
    cursor: pointer;
    text-align: left;
    transition: var(--transition);
}

.faq-question:hover {
    color: var(--primary);
}

.faq-item.active .faq-question {
    color: var(--primary);
}

/* ==========================================
   FAQ ICON
   ========================================== */

.faq-icon {
    font-size: 1.5rem;
    font-weight: 300;
    color: var(--primary);
    transition: transform 0.3s ease;
    flex-shrink: 0;
    margin-left: 15px;
}

.faq-item.active .faq-icon {
    transform: rotate(45deg);
}

/* ==========================================
   ANSWER
   ========================================== */

.faq-answer {
    max-height: 0;
    overflow: hidden;
    transition: max-height 0.4s ease, padding 0.4s ease;
    padding: 0 25px;
}

.faq-item.active .faq-answer {
    max-height: 300px;
    padding: 0 25px 20px;
}

.faq-answer p {
    color: var(--text-light);
    font-size: 0.95rem;
    line-height: 1.7;
}

/* ==========================================
   FOOTER
   ========================================== */

.faq-footer {
    text-align: center;
    margin-top: 40px;
    color: var(--text-light);
    font-size: 0.9rem;
}

.faq-footer a {
    color: var(--primary);
    text-decoration: none;
    font-weight: 600;
}

.faq-footer a:hover {
    text-decoration: underline;
}

#faq-count {
    margin-top: 10px;
    font-size: 0.85rem;
    color: var(--text-light);
}

/* ==========================================
   RESPONSIVE
   ========================================== */

@media (max-width: 480px) {
    body {
        padding: 20px 10px;
    }

    .faq-question {
        padding: 16px 18px;
        font-size: 0.95rem;
    }

    .faq-answer {
        padding: 0 18px;
    }

    .faq-item.active .faq-answer {
        padding: 0 18px 16px;
    }
}
```

### `js/script.js`:

```javascript
// ==========================================
// FAQ ACCORDION — JavaScript
// ==========================================

// Select all FAQ items
const faqItems = document.querySelectorAll(".faq-item");
const expandAllBtn = document.querySelector("#expand-all");
const collapseAllBtn = document.querySelector("#collapse-all");
const faqCountDisplay = document.querySelector("#faq-count");

// Display total FAQ count
faqCountDisplay.textContent = `Total FAQs: ${faqItems.length}`;


// --- Toggle Individual FAQ ---
faqItems.forEach(item => {
    const question = item.querySelector(".faq-question");

    question.addEventListener("click", () => {
        // Check if this item is already active
        const isActive = item.classList.contains("active");

        // Close ALL items first (optional: for "one at a time" behavior)
        faqItems.forEach(faq => faq.classList.remove("active"));

        // If it WASN'T active, open it. If it WAS, leave all closed.
        if (!isActive) {
            item.classList.add("active");
        }
    });
});


// --- Expand All ---
expandAllBtn.addEventListener("click", () => {
    faqItems.forEach(item => {
        item.classList.add("active");
    });
});


// --- Collapse All ---
collapseAllBtn.addEventListener("click", () => {
    faqItems.forEach(item => {
        item.classList.remove("active");
    });
});


// --- Bonus: Log category when clicked ---
faqItems.forEach(item => {
    item.addEventListener("click", () => {
        const category = item.dataset.category;
        console.log(`FAQ Category: ${category}`);
    });
});
```

### What This Project Demonstrates:

```
✅ querySelector / querySelectorAll for selecting elements
✅ textContent for reading and updating text
✅ classList.add(), remove(), contains() for state management
✅ CSS-driven animations (max-height transition) triggered by JS class toggle
✅ data-* attributes for storing metadata
✅ forEach for iterating over NodeLists
✅ Event listeners (click) — preview for Chapter 3
✅ Conditional logic (isActive check)
✅ Reusable patterns (expand all, collapse all)
✅ DOM navigation (item.querySelector for scoped selection)
✅ Responsive CSS + interactive JS working together
✅ Clean code organization with comments
```

---

## 2.13 Complete DOM Methods Reference

### Selecting Elements:

| Method | Returns | Example |
|--------|---------|---------|
| `querySelector(selector)` | First match | `document.querySelector(".card")` |
| `querySelectorAll(selector)` | All matches (NodeList) | `document.querySelectorAll("li")` |
| `getElementById(id)` | Element by ID | `document.getElementById("title")` |

### Reading/Writing Content:

| Property/Method | Purpose | Example |
|----------------|---------|---------|
| `textContent` | Get/set plain text | `el.textContent = "Hello"` |
| `innerHTML` | Get/set HTML content | `el.innerHTML = "<b>Bold</b>"` |
| `value` | Get/set input value | `input.value` |
| `getAttribute(attr)` | Read any attribute | `el.getAttribute("href")` |
| `setAttribute(attr, val)` | Set any attribute | `el.setAttribute("src", "img.jpg")` |

### Class Manipulation:

| Method | Purpose | Example |
|--------|---------|---------|
| `classList.add(name)` | Add a class | `el.classList.add("active")` |
| `classList.remove(name)` | Remove a class | `el.classList.remove("active")` |
| `classList.toggle(name)` | Toggle a class | `el.classList.toggle("active")` |
| `classList.contains(name)` | Check if has class | `el.classList.contains("active")` |
| `classList.replace(old, new)` | Replace a class | `el.classList.replace("old", "new")` |

### Creating/Removing Elements:

| Method | Purpose | Example |
|--------|---------|---------|
| `createElement(tag)` | Create new element | `document.createElement("div")` |
| `appendChild(el)` | Add as last child | `parent.appendChild(child)` |
| `prepend(el)` | Add as first child | `parent.prepend(child)` |
| `before(el)` | Add before element | `target.before(newEl)` |
| `after(el)` | Add after element | `target.after(newEl)` |
| `remove()` | Remove element | `el.remove()` |
| `insertAdjacentHTML(pos, html)` | Insert HTML string | `el.insertAdjacentHTML("beforeend", html)` |

### DOM Navigation:

| Property | Returns | Example |
|----------|---------|---------|
| `parentElement` | Parent element | `el.parentElement` |
| `children` | Child elements | `el.children` |
| `firstElementChild` | First child | `el.firstElementChild` |
| `lastElementChild` | Last child | `el.lastElementChild` |
| `previousElementSibling` | Prev sibling | `el.previousElementSibling` |
| `nextElementSibling` | Next sibling | `el.nextElementSibling` |
| `closest(selector)` | Nearest ancestor | `el.closest(".card")` |

---

## 2.14 Summary & Key Takeaways

| Concept | Key Point |
|---------|-----------|
| DOM | Tree-like representation of HTML that JS can manipulate |
| `document` | Entry point to access the DOM |
| `querySelector` | Select ONE element using CSS selector |
| `querySelectorAll` | Select ALL matching elements (returns NodeList) |
| `textContent` | Read/write plain text (safe from XSS) |
| `innerHTML` | Read/write HTML content (careful with user input!) |
| `.value` | Read/write form input values |
| `classList` | Add, remove, toggle, check CSS classes |
| `style.property` | Set inline styles (use camelCase) |
| `createElement` | Create new DOM elements with JS |
| `appendChild` / `prepend` | Add elements to the DOM |
| `remove()` | Delete elements from the DOM |
| `dataset` | Access `data-*` attributes |
| `closest()` | Find nearest ancestor matching selector |
| CSS + JS pattern | JS toggles classes → CSS handles visual changes |

---

> 🎉 **Congratulations!** You've completed Chapter 2. You now understand the DOM, can select any element, read and update content, manipulate classes and styles, create and remove elements dynamically, and build interactive components like the FAQ accordion!

---