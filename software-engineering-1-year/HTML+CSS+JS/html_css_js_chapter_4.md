

# 📘 Chapter 4: CSS Control via JavaScript

> **🔵 Bridge CSS knowledge with JS power — Toggling classes, animations via class changes, inline styles vs CSS classes, responsive behavior with JS, and building a theme switcher with animated menu.**

---

## 4.1 Why Control CSS with JavaScript?

CSS handles **static styling** — how things look by default and on simple interactions like `:hover`. But for **dynamic, conditional, and user-driven style changes**, JavaScript takes over.

### What CSS Can Do Alone:

```css
/* CSS handles these well */
a:hover { color: red; }                    /* Hover state */
input:focus { border-color: blue; }        /* Focus state */
.hidden { display: none; }                 /* Predefined states */
@media (max-width: 768px) { ... }          /* Responsive breakpoints */
```

### What Requires JavaScript:

```
✅ Toggle dark/light theme based on user preference
✅ Add/remove classes based on LOGIC (form validation, user actions)
✅ Animate elements based on SCROLL position
✅ Change styles based on DATA (API responses, calculations)
✅ Show/hide elements based on CONDITIONS
✅ Respond to user settings (font size, colors)
✅ Create complex UI interactions (accordions, tabs, modals)
✅ Apply styles that depend on RUNTIME values
```

### The Golden Rule:

```
CSS defines WHAT styles exist         → .active { background: blue; }
JavaScript decides WHEN to apply them → element.classList.add("active")

CSS = the paint colors available
JS  = the painter deciding which color to use and when
```

> 📌 **Best Practice:** Define ALL visual styles in CSS. Use JavaScript ONLY to toggle classes or set dynamic values. This keeps your code clean, maintainable, and performant.

---

## 4.2 Three Ways to Control CSS with JavaScript

### Method 1: Toggling CSS Classes ✅ (Best Practice)

```javascript
// JavaScript toggles a class
element.classList.add("active");
element.classList.remove("active");
element.classList.toggle("active");
```

```css
/* CSS defines ALL visual styles */
.active {
    background-color: #3498db;
    color: white;
    transform: scale(1.05);
    box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
}
```

### Method 2: Inline Styles (For Dynamic/Calculated Values)

```javascript
// JavaScript sets specific style values
element.style.width = `${calculatedWidth}px`;
element.style.backgroundColor = userSelectedColor;
element.style.transform = `translateX(${position}px)`;
```

### Method 3: CSS Custom Properties (Variables) ✅ (Modern & Powerful)

```javascript
// JavaScript updates CSS variables
document.documentElement.style.setProperty("--primary-color", "#e74c3c");
document.documentElement.style.setProperty("--font-size", "18px");
```

```css
/* CSS uses the variables */
body {
    color: var(--primary-color);
    font-size: var(--font-size);
}
```

### When to Use Each:

| Method | When to Use | Example |
|--------|-------------|---------|
| **Class toggling** ✅ | Predefined state changes | Active/inactive, open/closed, dark/light |
| **Inline styles** | Dynamic/calculated values | Position, width based on data, user-picked colors |
| **CSS variables** ✅ | Theme-wide changes, user preferences | Theme colors, font size, spacing |

---

## 4.3 Deep Dive — Class Toggling Patterns

### Pattern 1: Show/Hide Toggle

```html
<button id="toggle-btn">Toggle Menu</button>
<nav id="menu" class="menu">
    <a href="#">Home</a>
    <a href="#">About</a>
    <a href="#">Contact</a>
</nav>
```

```css
.menu {
    max-height: 0;
    overflow: hidden;
    opacity: 0;
    transition: max-height 0.4s ease, opacity 0.3s ease;
}

.menu.open {
    max-height: 300px;
    opacity: 1;
}
```

```javascript
const toggleBtn = document.querySelector("#toggle-btn");
const menu = document.querySelector("#menu");

toggleBtn.addEventListener("click", () => {
    menu.classList.toggle("open");
    
    // Update button text based on state
    const isOpen = menu.classList.contains("open");
    toggleBtn.textContent = isOpen ? "Close Menu" : "Toggle Menu";
});
```

### Pattern 2: Active State Switching

```html
<div class="tab-buttons">
    <button class="tab-btn active" data-tab="overview">Overview</button>
    <button class="tab-btn" data-tab="features">Features</button>
    <button class="tab-btn" data-tab="pricing">Pricing</button>
</div>

<div class="tab-panels">
    <div class="tab-panel active" id="overview">Overview content...</div>
    <div class="tab-panel" id="features">Features content...</div>
    <div class="tab-panel" id="pricing">Pricing content...</div>
</div>
```

```css
.tab-btn {
    padding: 10px 24px;
    border: none;
    background: transparent;
    color: #777;
    font-weight: 500;
    cursor: pointer;
    border-bottom: 3px solid transparent;
    transition: all 0.3s ease;
}

.tab-btn.active {
    color: #3498db;
    border-bottom-color: #3498db;
}

.tab-panel {
    display: none;
    opacity: 0;
    transition: opacity 0.3s ease;
}

.tab-panel.active {
    display: block;
    opacity: 1;
}
```

```javascript
const tabButtons = document.querySelectorAll(".tab-btn");
const tabPanels = document.querySelectorAll(".tab-panel");

tabButtons.forEach(btn => {
    btn.addEventListener("click", () => {
        // Remove active from ALL buttons and panels
        tabButtons.forEach(b => b.classList.remove("active"));
        tabPanels.forEach(p => p.classList.remove("active"));

        // Add active to clicked button
        btn.classList.add("active");

        // Add active to matching panel
        const targetId = btn.dataset.tab;
        document.querySelector(`#${targetId}`).classList.add("active");
    });
});
```

### Pattern 3: Multi-Class State Management

```css
/* Multiple states for a card component */
.card { 
    background: white; 
    border: 2px solid #e0e0e0;
    transition: all 0.3s ease;
}

.card.selected {
    border-color: #3498db;
    box-shadow: 0 0 0 3px rgba(52, 152, 219, 0.2);
}

.card.disabled {
    opacity: 0.5;
    pointer-events: none;
}

.card.loading {
    position: relative;
}

.card.loading::after {
    content: "Loading...";
    position: absolute;
    inset: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    background: rgba(255, 255, 255, 0.8);
    font-weight: 600;
}

.card.error {
    border-color: #e74c3c;
    background-color: #fef2f2;
}

.card.success {
    border-color: #2ecc71;
    background-color: #f0fdf4;
}
```

```javascript
const card = document.querySelector(".card");

// Set loading state
const setCardState = (state) => {
    // Remove ALL state classes first
    card.classList.remove("selected", "disabled", "loading", "error", "success");

    // Add the new state
    if (state) {
        card.classList.add(state);
    }
};

// Usage
setCardState("loading");      // Show loading overlay
setCardState("success");      // Show success state
setCardState("error");        // Show error state
setCardState(null);           // Reset to default
```

### Pattern 4: Scroll-Triggered Classes

```css
.fade-in-section {
    opacity: 0;
    transform: translateY(30px);
    transition: opacity 0.6s ease, transform 0.6s ease;
}

.fade-in-section.visible {
    opacity: 1;
    transform: translateY(0);
}
```

```javascript
const sections = document.querySelectorAll(".fade-in-section");

const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            entry.target.classList.add("visible");
        }
    });
}, {
    threshold: 0.15       // Trigger when 15% of element is visible
});

sections.forEach(section => observer.observe(section));
```

> 📌 `IntersectionObserver` is the modern, performant way to detect when elements enter the viewport — much better than listening to scroll events.

---

## 4.4 CSS Animations Triggered by Class Changes

CSS transitions and animations can be **triggered by adding/removing classes** with JavaScript. This is the most performant and cleanest way to animate elements.

### Transition-Based Animations:

```css
/* Define the transition on the BASE state */
.modal-overlay {
    position: fixed;
    inset: 0;
    background: rgba(0, 0, 0, 0);
    visibility: hidden;
    transition: background 0.3s ease, visibility 0.3s ease;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1000;
}

.modal-content {
    background: white;
    padding: 40px;
    border-radius: 12px;
    transform: scale(0.8) translateY(20px);
    opacity: 0;
    transition: transform 0.3s ease, opacity 0.3s ease;
    max-width: 500px;
    width: 90%;
}

/* ACTIVE state — triggered by JS adding the class */
.modal-overlay.active {
    background: rgba(0, 0, 0, 0.5);
    visibility: visible;
}

.modal-overlay.active .modal-content {
    transform: scale(1) translateY(0);
    opacity: 1;
}
```

```javascript
const modal = document.querySelector(".modal-overlay");
const openBtn = document.querySelector("#open-modal");
const closeBtn = document.querySelector("#close-modal");

openBtn.addEventListener("click", () => {
    modal.classList.add("active");
});

closeBtn.addEventListener("click", () => {
    modal.classList.remove("active");
});

// Close when clicking overlay (not content)
modal.addEventListener("click", (e) => {
    if (e.target === modal) {
        modal.classList.remove("active");
    }
});

// Close on Escape key
document.addEventListener("keydown", (e) => {
    if (e.key === "Escape" && modal.classList.contains("active")) {
        modal.classList.remove("active");
    }
});
```

### Keyframe Animations Triggered by Classes:

```css
/* Define the animation */
@keyframes slideInFromRight {
    from {
        transform: translateX(100%);
        opacity: 0;
    }
    to {
        transform: translateX(0);
        opacity: 1;
    }
}

@keyframes slideOutToRight {
    from {
        transform: translateX(0);
        opacity: 1;
    }
    to {
        transform: translateX(100%);
        opacity: 0;
    }
}

/* Apply animation when class is added */
.notification {
    position: fixed;
    top: 20px;
    right: 20px;
    padding: 16px 24px;
    background: #2ecc71;
    color: white;
    border-radius: 8px;
    box-shadow: 0 4px 15px rgba(0, 0, 0, 0.15);
    transform: translateX(100%);
    opacity: 0;
}

.notification.show {
    animation: slideInFromRight 0.4s ease forwards;
}

.notification.hide {
    animation: slideOutToRight 0.4s ease forwards;
}
```

```javascript
const showNotification = (message, duration = 3000) => {
    // Create notification element
    const notification = document.createElement("div");
    notification.classList.add("notification");
    notification.textContent = message;
    document.body.appendChild(notification);

    // Trigger enter animation
    requestAnimationFrame(() => {
        notification.classList.add("show");
    });

    // Auto-dismiss after duration
    setTimeout(() => {
        notification.classList.remove("show");
        notification.classList.add("hide");

        // Remove from DOM after animation completes
        notification.addEventListener("animationend", () => {
            notification.remove();
        });
    }, duration);
};

// Usage
showNotification("Settings saved successfully!");
showNotification("Welcome back, Ali!", 5000);
```

### `animationend` and `transitionend` Events:

```javascript
// Run code AFTER a CSS transition finishes
element.addEventListener("transitionend", () => {
    console.log("Transition complete!");
    // Safe to remove element, change state, etc.
});

// Run code AFTER a CSS animation finishes
element.addEventListener("animationend", () => {
    console.log("Animation complete!");
    element.remove();       // Remove after exit animation
});
```

> 📌 **Key Insight:** These events let you synchronize JavaScript logic with CSS animation timing — essential for complex UI interactions.

---

## 4.5 Inline Styles vs CSS Classes — When to Use Each

### When to Use CSS Classes (Most of the Time):

```javascript
// ✅ Predefined visual states
element.classList.add("active");
element.classList.add("error");
element.classList.add("dark-theme");
element.classList.add("expanded");
element.classList.add("loading");
```

```css
/* All styling stays in CSS */
.active { background: #3498db; color: white; }
.error { border-color: #e74c3c; }
.dark-theme { background: #1a1a2e; color: #ecf0f1; }
.expanded { max-height: 500px; }
.loading { opacity: 0.5; pointer-events: none; }
```

### When to Use Inline Styles (Dynamic Values):

```javascript
// ✅ Values calculated at runtime
progressBar.style.width = `${percentage}%`;

// ✅ User-selected values
element.style.backgroundColor = colorPicker.value;

// ✅ Positions based on mouse/scroll
tooltip.style.top = `${mouseY + 10}px`;
tooltip.style.left = `${mouseX + 10}px`;

// ✅ Sizes based on data
chart.style.height = `${dataValue * 3}px`;

// ✅ Animations with dynamic endpoints
element.style.transform = `rotate(${angle}deg)`;
```

### The Hybrid Approach (Best of Both Worlds):

```css
/* CSS defines the TRANSITION and base styles */
.progress-bar {
    height: 8px;
    background: #e0e0e0;
    border-radius: 4px;
    overflow: hidden;
}

.progress-fill {
    height: 100%;
    background: linear-gradient(90deg, #667eea, #764ba2);
    border-radius: 4px;
    transition: width 0.5s ease;       /* CSS handles the animation */
    width: 0;                          /* Default */
}
```

```javascript
// JavaScript sets ONLY the dynamic value
const updateProgress = (percent) => {
    const fill = document.querySelector(".progress-fill");
    fill.style.width = `${percent}%`;   // JS sets the value, CSS animates it
};

updateProgress(75);   // Smoothly animates to 75% thanks to CSS transition
```

### ❌ Anti-Pattern: Doing Everything in JavaScript:

```javascript
// ❌ BAD — too much CSS in JavaScript
element.style.backgroundColor = "#3498db";
element.style.color = "white";
element.style.padding = "20px";
element.style.borderRadius = "8px";
element.style.boxShadow = "0 4px 15px rgba(0,0,0,0.2)";
element.style.transition = "all 0.3s ease";
element.style.transform = "scale(1.05)";

// ✅ GOOD — one line in JS, all styles in CSS
element.classList.add("highlighted");
```

---

## 4.6 Controlling CSS Custom Properties (Variables) with JavaScript

CSS Custom Properties (variables) can be **read and modified** with JavaScript, enabling powerful theme systems and dynamic styling.

### Setting CSS Variables with JavaScript:

```javascript
// Set a variable on the ROOT element (affects entire page)
document.documentElement.style.setProperty("--primary-color", "#e74c3c");
document.documentElement.style.setProperty("--bg-color", "#1a1a2e");
document.documentElement.style.setProperty("--text-color", "#ecf0f1");
document.documentElement.style.setProperty("--font-size", "18px");
```

### Reading CSS Variables with JavaScript:

```javascript
// Read a CSS variable value
const styles = getComputedStyle(document.documentElement);
const primaryColor = styles.getPropertyValue("--primary-color").trim();
console.log(primaryColor);   // "#e74c3c"
```

### Setting Variables on Specific Elements:

```javascript
// Set a variable on a SPECIFIC element (scoped)
const card = document.querySelector(".card");
card.style.setProperty("--card-bg", "#f0f0f0");
card.style.setProperty("--card-border", "#3498db");
```

```css
.card {
    --card-bg: white;
    --card-border: #e0e0e0;

    background-color: var(--card-bg);
    border: 2px solid var(--card-border);
}
```

### Dynamic Color Theme with CSS Variables:

```css
:root {
    --primary: #667eea;
    --bg: #ffffff;
    --text: #333333;
    --card-bg: #f8f9fa;
    --border: #e0e0e0;
    --shadow: 0 2px 10px rgba(0, 0, 0, 0.06);
}

body {
    background-color: var(--bg);
    color: var(--text);
    transition: background-color 0.4s ease, color 0.4s ease;
}

.card {
    background-color: var(--card-bg);
    border: 1px solid var(--border);
    box-shadow: var(--shadow);
}
```

```javascript
// Apply a completely custom theme
const applyTheme = (theme) => {
    const root = document.documentElement;
    Object.entries(theme).forEach(([property, value]) => {
        root.style.setProperty(property, value);
    });
};

// Theme objects
const oceanTheme = {
    "--primary": "#0077b6",
    "--bg": "#caf0f8",
    "--text": "#023e8a",
    "--card-bg": "#ade8f4",
    "--border": "#90e0ef"
};

const sunsetTheme = {
    "--primary": "#e76f51",
    "--bg": "#fef9ef",
    "--text": "#264653",
    "--card-bg": "#f4a261",
    "--border": "#e9c46a"
};

// Apply theme on button click
document.querySelector("#ocean-btn").addEventListener("click", () => {
    applyTheme(oceanTheme);
});

document.querySelector("#sunset-btn").addEventListener("click", () => {
    applyTheme(sunsetTheme);
});
```

---

## 4.7 Responsive Behavior with JavaScript

While CSS handles responsive design through media queries, some responsive behaviors **require JavaScript**.

### Reading Viewport Dimensions:

```javascript
// Current viewport size
console.log(window.innerWidth);    // e.g., 1200
console.log(window.innerHeight);   // e.g., 800

// Listen for resize events
window.addEventListener("resize", () => {
    console.log(`Width: ${window.innerWidth}, Height: ${window.innerHeight}`);
});
```

### JavaScript Media Query Matching:

```javascript
// matchMedia — the JS equivalent of CSS @media queries
const isMobile = window.matchMedia("(max-width: 768px)");

// Check current state
console.log(isMobile.matches);     // true or false

// LISTEN for changes (responsive!)
isMobile.addEventListener("change", (e) => {
    if (e.matches) {
        console.log("Switched to mobile view!");
        // Run mobile-specific JavaScript
    } else {
        console.log("Switched to desktop view!");
        // Run desktop-specific JavaScript
    }
});
```

### Practical: Responsive Navigation

```html
<header class="header">
    <a href="#" class="logo">DevStudio</a>
    <button class="hamburger" id="hamburger" aria-label="Toggle menu">
        <span></span>
        <span></span>
        <span></span>
    </button>
    <nav class="nav" id="nav">
        <a href="#" class="nav-link">Home</a>
        <a href="#" class="nav-link">About</a>
        <a href="#" class="nav-link">Services</a>
        <a href="#" class="nav-link">Contact</a>
    </nav>
</header>
```

```css
/* Desktop navigation */
.header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 15px 30px;
    background: #1a1a2e;
}

.logo {
    color: white;
    font-size: 1.4rem;
    font-weight: 700;
    text-decoration: none;
}

.nav {
    display: flex;
    gap: 25px;
}

.nav-link {
    color: #ccc;
    text-decoration: none;
    font-size: 0.95rem;
    transition: color 0.3s ease;
}

.nav-link:hover {
    color: white;
}

.hamburger {
    display: none;
    flex-direction: column;
    gap: 5px;
    background: none;
    border: none;
    cursor: pointer;
    padding: 5px;
}

.hamburger span {
    display: block;
    width: 25px;
    height: 2px;
    background: white;
    transition: all 0.3s ease;
}

/* Mobile styles */
@media (max-width: 768px) {
    .hamburger {
        display: flex;
    }

    .nav {
        position: fixed;
        top: 0;
        right: -100%;
        width: 280px;
        height: 100vh;
        background: #16213e;
        flex-direction: column;
        padding: 80px 30px 30px;
        gap: 20px;
        transition: right 0.4s ease;
        z-index: 999;
    }

    .nav.open {
        right: 0;
    }

    .nav-link {
        font-size: 1.1rem;
    }

    /* Hamburger animation when menu is open */
    .hamburger.active span:nth-child(1) {
        transform: rotate(45deg) translate(5px, 5px);
    }

    .hamburger.active span:nth-child(2) {
        opacity: 0;
    }

    .hamburger.active span:nth-child(3) {
        transform: rotate(-45deg) translate(5px, -5px);
    }
}

/* Overlay when menu is open */
.overlay {
    position: fixed;
    inset: 0;
    background: rgba(0, 0, 0, 0.5);
    opacity: 0;
    visibility: hidden;
    transition: all 0.3s ease;
    z-index: 998;
}

.overlay.active {
    opacity: 1;
    visibility: visible;
}
```

```javascript
const hamburger = document.querySelector("#hamburger");
const nav = document.querySelector("#nav");

// Create overlay element
const overlay = document.createElement("div");
overlay.classList.add("overlay");
document.body.appendChild(overlay);

// Toggle mobile menu
const toggleMenu = () => {
    hamburger.classList.toggle("active");
    nav.classList.toggle("open");
    overlay.classList.toggle("active");
    document.body.style.overflow = nav.classList.contains("open") ? "hidden" : "";
};

hamburger.addEventListener("click", toggleMenu);
overlay.addEventListener("click", toggleMenu);

// Close menu when a link is clicked
nav.querySelectorAll(".nav-link").forEach(link => {
    link.addEventListener("click", () => {
        if (nav.classList.contains("open")) {
            toggleMenu();
        }
    });
});

// Close menu on Escape key
document.addEventListener("keydown", (e) => {
    if (e.key === "Escape" && nav.classList.contains("open")) {
        toggleMenu();
    }
});

// Close menu when resizing to desktop
const mobileQuery = window.matchMedia("(max-width: 768px)");
mobileQuery.addEventListener("change", (e) => {
    if (!e.matches && nav.classList.contains("open")) {
        toggleMenu();
    }
});
```

---

## 4.8 Theme Switcher — Light/Dark Mode

Dark mode is one of the most requested features on modern websites. Let's build a complete, professional theme switcher.

### The Architecture:

```
1. CSS Variables define theme colors
2. A CSS class on <html> switches ALL variables at once
3. JavaScript toggles the class
4. localStorage saves the user's preference
5. System preference is detected as default
```

### CSS Theme Setup:

```css
/* ==========================================
   LIGHT THEME (Default)
   ========================================== */

:root {
    --bg-primary: #ffffff;
    --bg-secondary: #f4f6f9;
    --bg-card: #ffffff;
    --text-primary: #1a1a2e;
    --text-secondary: #555555;
    --text-muted: #999999;
    --border-color: #e2e8f0;
    --primary: #667eea;
    --primary-dark: #5a6fd6;
    --shadow: 0 2px 10px rgba(0, 0, 0, 0.06);
    --shadow-lg: 0 8px 30px rgba(0, 0, 0, 0.1);
    --header-bg: #1a1a2e;
    --header-text: #ffffff;
    --code-bg: #f1f3f5;
    --toggle-bg: #e0e0e0;
    --toggle-circle: #ffffff;
}


/* ==========================================
   DARK THEME (Applied via class on <html>)
   ========================================== */

html.dark-theme {
    --bg-primary: #0f0f1a;
    --bg-secondary: #1a1a2e;
    --bg-card: #1e1e3a;
    --text-primary: #e8e8f0;
    --text-secondary: #b0b0c0;
    --text-muted: #707088;
    --border-color: #2d2d50;
    --primary: #818cf8;
    --primary-dark: #6366f1;
    --shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
    --shadow-lg: 0 8px 30px rgba(0, 0, 0, 0.4);
    --header-bg: #0a0a15;
    --header-text: #e8e8f0;
    --code-bg: #2d2d50;
    --toggle-bg: #4a4a6a;
    --toggle-circle: #818cf8;
}


/* ==========================================
   ALL STYLES USE VARIABLES
   ========================================== */

body {
    background-color: var(--bg-primary);
    color: var(--text-primary);
    transition: background-color 0.4s ease, color 0.4s ease;
    font-family: 'Inter', sans-serif;
    line-height: 1.6;
}

.header {
    background-color: var(--header-bg);
    color: var(--header-text);
    padding: 15px 30px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    transition: background-color 0.4s ease;
}

.card {
    background-color: var(--bg-card);
    border: 1px solid var(--border-color);
    border-radius: 12px;
    padding: 25px;
    box-shadow: var(--shadow);
    transition: background-color 0.4s ease, border-color 0.4s ease, box-shadow 0.4s ease;
}

.card:hover {
    box-shadow: var(--shadow-lg);
}

h1, h2, h3 {
    color: var(--text-primary);
    transition: color 0.4s ease;
}

p {
    color: var(--text-secondary);
    transition: color 0.4s ease;
}

a {
    color: var(--primary);
}


/* ==========================================
   THEME TOGGLE SWITCH
   ========================================== */

.theme-toggle {
    display: flex;
    align-items: center;
    gap: 10px;
    cursor: pointer;
}

.theme-toggle-label {
    font-size: 0.85rem;
    color: var(--header-text);
}

.toggle-switch {
    position: relative;
    width: 52px;
    height: 28px;
    background-color: var(--toggle-bg);
    border-radius: 14px;
    border: none;
    cursor: pointer;
    transition: background-color 0.3s ease;
    padding: 0;
}

.toggle-circle {
    position: absolute;
    top: 3px;
    left: 3px;
    width: 22px;
    height: 22px;
    background-color: var(--toggle-circle);
    border-radius: 50%;
    transition: transform 0.3s ease, background-color 0.3s ease;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2);
}

html.dark-theme .toggle-circle {
    transform: translateX(24px);
}

.sun-icon, .moon-icon {
    font-size: 1rem;
}
```

### JavaScript Theme Logic:

```javascript
// ==========================================
// THEME SWITCHER — JavaScript
// ==========================================

const themeToggle = document.querySelector("#theme-toggle");
const htmlElement = document.documentElement;      // <html> element

// --- Check for saved preference or system preference ---
const getPreferredTheme = () => {
    // First: check localStorage
    const savedTheme = localStorage.getItem("theme");
    if (savedTheme) return savedTheme;

    // Second: check system preference
    const prefersDark = window.matchMedia("(prefers-color-scheme: dark)").matches;
    return prefersDark ? "dark" : "light";
};


// --- Apply theme ---
const applyTheme = (theme) => {
    if (theme === "dark") {
        htmlElement.classList.add("dark-theme");
    } else {
        htmlElement.classList.remove("dark-theme");
    }

    // Save preference
    localStorage.setItem("theme", theme);

    // Update toggle button aria-label for accessibility
    themeToggle.setAttribute("aria-label", 
        `Switch to ${theme === "dark" ? "light" : "dark"} theme`
    );
};


// --- Initialize theme on page load ---
const currentTheme = getPreferredTheme();
applyTheme(currentTheme);


// --- Toggle theme on click ---
themeToggle.addEventListener("click", () => {
    const isDark = htmlElement.classList.contains("dark-theme");
    applyTheme(isDark ? "light" : "dark");
});


// --- Listen for system theme changes ---
window.matchMedia("(prefers-color-scheme: dark)").addEventListener("change", (e) => {
    // Only auto-switch if user hasn't manually chosen a theme
    if (!localStorage.getItem("theme")) {
        applyTheme(e.matches ? "dark" : "light");
    }
});
```

### How the Theme System Works:

```
Page Load:
1. Check localStorage for saved theme preference
2. If no saved preference, check system preference (prefers-color-scheme)
3. Apply the theme by adding/removing "dark-theme" class on <html>
4. All CSS variables switch instantly because they're defined on :root and html.dark-theme

Toggle Click:
1. Check if dark-theme class is currently applied
2. Toggle to opposite theme
3. Save choice to localStorage
4. CSS transitions smoothly animate all color changes

Next Visit:
1. localStorage has the saved preference
2. Theme is applied immediately on page load
3. No flash of wrong theme!
```

---

## 4.9 Practical CSS + JS Interaction Patterns

### Pattern 1: Scroll Progress Bar

```html
<div class="progress-container">
    <div class="progress-bar" id="progress-bar"></div>
</div>
```

```css
.progress-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 4px;
    background: transparent;
    z-index: 9999;
}

.progress-bar {
    height: 100%;
    width: 0%;
    background: linear-gradient(90deg, #667eea, #764ba2);
    transition: width 0.1s ease;
}
```

```javascript
const progressBar = document.querySelector("#progress-bar");

window.addEventListener("scroll", () => {
    const scrollTop = window.scrollY;
    const docHeight = document.documentElement.scrollHeight - window.innerHeight;
    const scrollPercent = (scrollTop / docHeight) * 100;

    progressBar.style.width = `${scrollPercent}%`;
});
```

### Pattern 2: Dynamic Tooltip

```css
.tooltip {
    position: fixed;
    padding: 8px 16px;
    background: #1a1a2e;
    color: white;
    border-radius: 6px;
    font-size: 0.85rem;
    pointer-events: none;
    opacity: 0;
    transition: opacity 0.2s ease;
    z-index: 10000;
    white-space: nowrap;
}

.tooltip.visible {
    opacity: 1;
}
```

```javascript
// Create tooltip element once
const tooltip = document.createElement("div");
tooltip.classList.add("tooltip");
document.body.appendChild(tooltip);

// Add tooltips to any element with data-tooltip attribute
document.addEventListener("mouseenter", (e) => {
    const target = e.target.closest("[data-tooltip]");
    if (!target) return;

    tooltip.textContent = target.dataset.tooltip;
    tooltip.classList.add("visible");
}, true);

document.addEventListener("mousemove", (e) => {
    if (tooltip.classList.contains("visible")) {
        tooltip.style.top = `${e.clientY - 40}px`;
        tooltip.style.left = `${e.clientX}px`;
    }
});

document.addEventListener("mouseleave", (e) => {
    const target = e.target.closest("[data-tooltip]");
    if (!target) return;

    tooltip.classList.remove("visible");
}, true);
```

```html
<!-- Usage — just add data-tooltip to any element -->
<button data-tooltip="Click to save your work">Save</button>
<a href="#" data-tooltip="Visit our homepage">Home</a>
<span data-tooltip="This is helpful info">ℹ️</span>
```

### Pattern 3: Animated Counter

```javascript
const animateCounter = (element, target, duration = 2000) => {
    let start = 0;
    const startTime = performance.now();

    const update = (currentTime) => {
        const elapsed = currentTime - startTime;
        const progress = Math.min(elapsed / duration, 1);

        // Ease-out function for smooth deceleration
        const easedProgress = 1 - Math.pow(1 - progress, 3);

        const current = Math.floor(easedProgress * target);
        element.textContent = current.toLocaleString();

        if (progress < 1) {
            requestAnimationFrame(update);
        } else {
            element.textContent = target.toLocaleString();
        }
    };

    requestAnimationFrame(update);
};

// Trigger when elements enter viewport
const counters = document.querySelectorAll("[data-count]");

const counterObserver = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            const target = parseInt(entry.target.dataset.count);
            animateCounter(entry.target, target);
            counterObserver.unobserve(entry.target);   // Only animate once
        }
    });
}, { threshold: 0.5 });

counters.forEach(counter => counterObserver.observe(counter));
```

```html
<div class="stats">
    <div class="stat">
        <span class="stat-number" data-count="1500">0</span>
        <span class="stat-label">Projects</span>
    </div>
    <div class="stat">
        <span class="stat-number" data-count="800">0</span>
        <span class="stat-label">Clients</span>
    </div>
    <div class="stat">
        <span class="stat-number" data-count="50">0</span>
        <span class="stat-label">Awards</span>
    </div>
</div>
```

### Pattern 4: Typing Effect

```javascript
const typeWriter = (element, text, speed = 50) => {
    let index = 0;
    element.textContent = "";

    const type = () => {
        if (index < text.length) {
            element.textContent += text[index];
            index++;
            setTimeout(type, speed);
        }
    };

    type();
};

// Usage
const heading = document.querySelector(".hero-title");
typeWriter(heading, "We Build Digital Experiences", 60);
```

---

## 4.10 Mini-Project: Theme Toggler + Animated Menu 🏆

Let's build a complete project combining theme switching, animated hamburger menu, and CSS control patterns.

### File Structure:

```
theme-menu-project/
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
    <title>DevStudio — Theme & Menu Project</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="css/style.css">
</head>
<body>

    <!-- ===== SCROLL PROGRESS BAR ===== -->
    <div class="scroll-progress">
        <div class="scroll-progress-bar" id="scroll-bar"></div>
    </div>

    <!-- ===== HEADER ===== -->
    <header class="header">
        <div class="container header-content">
            <a href="#" class="logo">Dev<span class="logo-accent">Studio</span></a>

            <nav class="nav" id="nav">
                <a href="#home" class="nav-link">Home</a>
                <a href="#features" class="nav-link">Features</a>
                <a href="#stats" class="nav-link">Stats</a>
                <a href="#contact" class="nav-link">Contact</a>
            </nav>

            <div class="header-actions">
                <!-- Theme Toggle -->
                <button class="theme-toggle" id="theme-toggle" aria-label="Toggle theme">
                    <span class="sun-icon">☀️</span>
                    <div class="toggle-track">
                        <div class="toggle-thumb"></div>
                    </div>
                    <span class="moon-icon">🌙</span>
                </button>

                <!-- Hamburger Menu -->
                <button class="hamburger" id="hamburger" aria-label="Toggle menu">
                    <span class="hamburger-line"></span>
                    <span class="hamburger-line"></span>
                    <span class="hamburger-line"></span>
                </button>
            </div>
        </div>
    </header>

    <!-- ===== OVERLAY ===== -->
    <div class="overlay" id="overlay"></div>

    <!-- ===== HERO ===== -->
    <section id="home" class="hero">
        <div class="container hero-content">
            <h1 class="hero-title" id="hero-title"></h1>
            <p class="hero-subtitle">A creative agency crafting beautiful websites and powerful brands.</p>
            <div class="hero-buttons">
                <a href="#features" class="btn btn-primary">Explore Features</a>
                <a href="#contact" class="btn btn-outline">Get In Touch</a>
            </div>
        </div>
    </section>

    <!-- ===== FEATURES ===== -->
    <section id="features" class="section">
        <div class="container">
            <div class="section-header">
                <h2>Our Features</h2>
                <p>Everything you need to build amazing digital experiences.</p>
            </div>

            <div class="features-grid">
                <article class="feature-card fade-in" data-tooltip="Core web technology">
                    <div class="feature-icon">🎨</div>
                    <h3>Beautiful Design</h3>
                    <p>Stunning interfaces that capture attention and drive engagement.</p>
                </article>

                <article class="feature-card fade-in" data-tooltip="Blazing fast performance">
                    <div class="feature-icon">⚡</div>
                    <h3>Lightning Fast</h3>
                    <p>Optimized performance for instant loading and smooth interactions.</p>
                </article>

                <article class="feature-card fade-in" data-tooltip="Works on all devices">
                    <div class="feature-icon">📱</div>
                    <h3>Fully Responsive</h3>
                    <p>Perfect experience on every device from phones to ultrawide monitors.</p>
                </article>

                <article class="feature-card fade-in" data-tooltip="Theme customization">
                    <div class="feature-icon">🌓</div>
                    <h3>Dark Mode</h3>
                    <p>Beautiful light and dark themes with smooth transitions.</p>
                </article>
            </div>
        </div>
    </section>

    <!-- ===== STATS ===== -->
    <section id="stats" class="section stats-section">
        <div class="container stats-grid">
            <div class="stat">
                <span class="stat-number" data-count="150">0</span>
                <span class="stat-label">Projects</span>
            </div>
            <div class="stat">
                <span class="stat-number" data-count="80">0</span>
                <span class="stat-label">Clients</span>
            </div>
            <div class="stat">
                <span class="stat-number" data-count="12">0</span>
                <span class="stat-label">Awards</span>
            </div>
            <div class="stat">
                <span class="stat-number" data-count="7">0</span>
                <span class="stat-label">Years</span>
            </div>
        </div>
    </section>

    <!-- ===== CONTACT ===== -->
    <section id="contact" class="section">
        <div class="container">
            <div class="section-header">
                <h2>Get In Touch</h2>
                <p>Ready to start your next project? Let's talk.</p>
            </div>

            <form class="contact-form" id="contact-form">
                <div class="form-row">
                    <div class="form-group">
                        <label for="name">Name</label>
                        <input type="text" id="name" placeholder="Your name" required>
                    </div>
                    <div class="form-group">
                        <label for="email">Email</label>
                        <input type="email" id="email" placeholder="you@example.com" required>
                    </div>
                </div>
                <div class="form-group">
                    <label for="message">Message</label>
                    <textarea id="message" placeholder="Tell us about your project..." required></textarea>
                </div>
                <button type="submit" class="btn btn-primary btn-full">Send Message</button>
            </form>
        </div>
    </section>

    <!-- ===== FOOTER ===== -->
    <footer class="footer">
        <div class="container">
            <p>&copy; 2025 DevStudio. Built with HTML, CSS & JavaScript.</p>
        </div>
    </footer>

    <!-- ===== NOTIFICATION CONTAINER ===== -->
    <div id="notification-container"></div>

    <script src="js/script.js"></script>
</body>
</html>
```

### `css/style.css`:

```css
/* ==========================================
   RESET & VARIABLES
   ========================================== */

*, *::before, *::after {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

:root {
    --bg: #ffffff;
    --bg-secondary: #f4f6f9;
    --bg-card: #ffffff;
    --text: #1a1a2e;
    --text-secondary: #555;
    --text-muted: #999;
    --border: #e2e8f0;
    --primary: #667eea;
    --primary-dark: #5a6fd6;
    --secondary: #764ba2;
    --header-bg: #1a1a2e;
    --shadow: 0 2px 10px rgba(0, 0, 0, 0.06);
    --shadow-lg: 0 10px 35px rgba(0, 0, 0, 0.1);
    --font: 'Inter', sans-serif;
    --radius: 10px;
    --transition: all 0.3s ease;
    --toggle-bg: #dde1e7;
}

html.dark-theme {
    --bg: #0f0f1a;
    --bg-secondary: #1a1a2e;
    --bg-card: #1e1e3a;
    --text: #e8e8f0;
    --text-secondary: #b0b0c0;
    --text-muted: #707088;
    --border: #2d2d50;
    --primary: #818cf8;
    --primary-dark: #6366f1;
    --secondary: #a78bfa;
    --header-bg: #0a0a15;
    --shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
    --shadow-lg: 0 10px 35px rgba(0, 0, 0, 0.4);
    --toggle-bg: #4a4a6a;
}

html {
    scroll-behavior: smooth;
}

body {
    font-family: var(--font);
    font-size: 16px;
    line-height: 1.7;
    color: var(--text);
    background-color: var(--bg);
    transition: background-color 0.4s ease, color 0.4s ease;
}

img { max-width: 100%; height: auto; display: block; }
a { text-decoration: none; color: inherit; }
ul { list-style: none; }

.container {
    max-width: 1100px;
    margin: 0 auto;
    padding: 0 20px;
}


/* ==========================================
   SCROLL PROGRESS
   ========================================== */

.scroll-progress {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 3px;
    z-index: 10001;
    background: transparent;
}

.scroll-progress-bar {
    height: 100%;
    width: 0;
    background: linear-gradient(90deg, var(--primary), var(--secondary));
    transition: width 0.05s linear;
}


/* ==========================================
   HEADER
   ========================================== */

.header {
    position: sticky;
    top: 0;
    background-color: var(--header-bg);
    z-index: 1000;
    padding: 0;
    box-shadow: var(--shadow);
    transition: background-color 0.4s ease;
}

.header-content {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 14px 0;
}

.logo {
    font-size: 1.4rem;
    font-weight: 700;
    color: white;
}

.logo-accent {
    color: var(--primary);
}

.nav {
    display: flex;
    gap: 30px;
}

.nav-link {
    color: rgba(255, 255, 255, 0.7);
    font-size: 0.9rem;
    font-weight: 500;
    transition: var(--transition);
}

.nav-link:hover {
    color: white;
}

.header-actions {
    display: flex;
    align-items: center;
    gap: 15px;
}


/* ==========================================
   THEME TOGGLE
   ========================================== */

.theme-toggle {
    display: flex;
    align-items: center;
    gap: 6px;
    background: none;
    border: none;
    cursor: pointer;
    padding: 4px;
}

.sun-icon, .moon-icon {
    font-size: 0.9rem;
    transition: var(--transition);
}

.toggle-track {
    width: 44px;
    height: 24px;
    background-color: var(--toggle-bg);
    border-radius: 12px;
    position: relative;
    transition: background-color 0.3s ease;
}

.toggle-thumb {
    width: 18px;
    height: 18px;
    background: white;
    border-radius: 50%;
    position: absolute;
    top: 3px;
    left: 3px;
    transition: transform 0.3s ease;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2);
}

html.dark-theme .toggle-thumb {
    transform: translateX(20px);
    background: var(--primary);
}


/* ==========================================
   HAMBURGER
   ========================================== */

.hamburger {
    display: none;
    flex-direction: column;
    gap: 5px;
    background: none;
    border: none;
    cursor: pointer;
    padding: 5px;
    z-index: 1002;
}

.hamburger-line {
    display: block;
    width: 24px;
    height: 2px;
    background: white;
    transition: var(--transition);
    transform-origin: center;
}

.hamburger.active .hamburger-line:nth-child(1) {
    transform: rotate(45deg) translate(5px, 5px);
}

.hamburger.active .hamburger-line:nth-child(2) {
    opacity: 0;
    transform: scaleX(0);
}

.hamburger.active .hamburger-line:nth-child(3) {
    transform: rotate(-45deg) translate(5px, -5px);
}


/* ==========================================
   OVERLAY
   ========================================== */

.overlay {
    position: fixed;
    inset: 0;
    background: rgba(0, 0, 0, 0.5);
    opacity: 0;
    visibility: hidden;
    transition: var(--transition);
    z-index: 998;
}

.overlay.active {
    opacity: 1;
    visibility: visible;
}


/* ==========================================
   HERO
   ========================================== */

.hero {
    background: linear-gradient(135deg, var(--header-bg), #16213e);
    color: white;
    padding: 100px 0 80px;
    text-align: center;
    min-height: 70vh;
    display: flex;
    align-items: center;
    transition: background-color 0.4s ease;
}

html.dark-theme .hero {
    background: linear-gradient(135deg, #0a0a15, #1a1a2e);
}

.hero-title {
    font-size: clamp(2rem, 5vw, 3.2rem);
    font-weight: 700;
    color: white;
    margin-bottom: 15px;
    min-height: 50px;
}

.hero-subtitle {
    font-size: clamp(1rem, 2vw, 1.15rem);
    color: rgba(255, 255, 255, 0.7);
    max-width: 550px;
    margin: 0 auto 35px;
    font-weight: 300;
}

.hero-buttons {
    display: flex;
    justify-content: center;
    gap: 15px;
    flex-wrap: wrap;
}


/* ==========================================
   BUTTONS
   ========================================== */

.btn {
    display: inline-block;
    padding: 13px 32px;
    font-size: 0.95rem;
    font-weight: 600;
    font-family: var(--font);
    border-radius: 30px;
    cursor: pointer;
    transition: var(--transition);
    border: 2px solid transparent;
    text-align: center;
}

.btn-primary {
    background: linear-gradient(135deg, var(--primary), var(--secondary));
    color: white;
}

.btn-primary:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 25px rgba(102, 126, 234, 0.3);
}

.btn-outline {
    background: transparent;
    color: white;
    border-color: rgba(255, 255, 255, 0.4);
}

.btn-outline:hover {
    background: white;
    color: var(--text);
    border-color: white;
}

.btn-full { width: 100%; }


/* ==========================================
   SECTIONS
   ========================================== */

.section {
    padding: 80px 0;
}

.section-header {
    text-align: center;
    max-width: 550px;
    margin: 0 auto 50px;
}

.section-header h2 {
    font-size: clamp(1.5rem, 3vw, 2.2rem);
    margin-bottom: 10px;
    color: var(--text);
    transition: color 0.4s ease;
}

.section-header p {
    color: var(--text-secondary);
    transition: color 0.4s ease;
}


/* ==========================================
   FEATURE CARDS
   ========================================== */

.features-grid {
    display: flex;
    flex-wrap: wrap;
    gap: 25px;
}

.feature-card {
    flex: 1;
    min-width: 230px;
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 30px 25px;
    text-align: center;
    box-shadow: var(--shadow);
    transition: transform 0.3s ease, box-shadow 0.3s ease, 
                background-color 0.4s ease, border-color 0.4s ease;
}

.feature-card:hover {
    transform: translateY(-8px);
    box-shadow: var(--shadow-lg);
}

.feature-icon {
    font-size: 2.5rem;
    margin-bottom: 15px;
}

.feature-card h3 {
    margin-bottom: 8px;
    color: var(--text);
    transition: color 0.4s ease;
}

.feature-card p {
    color: var(--text-secondary);
    font-size: 0.92rem;
    transition: color 0.4s ease;
}


/* ==========================================
   FADE IN ANIMATION
   ========================================== */

.fade-in {
    opacity: 0;
    transform: translateY(25px);
    transition: opacity 0.6s ease, transform 0.6s ease;
}

.fade-in.visible {
    opacity: 1;
    transform: translateY(0);
}


/* ==========================================
   STATS
   ========================================== */

.stats-section {
    background: linear-gradient(135deg, var(--primary), var(--secondary));
    color: white;
}

.stats-grid {
    display: flex;
    justify-content: space-around;
    flex-wrap: wrap;
    gap: 30px;
    text-align: center;
}

.stat-number {
    display: block;
    font-size: clamp(2rem, 4vw, 3rem);
    font-weight: 700;
}

.stat-label {
    font-size: 0.9rem;
    text-transform: uppercase;
    letter-spacing: 1px;
    opacity: 0.85;
}


/* ==========================================
   CONTACT FORM
   ========================================== */

.contact-form {
    max-width: 650px;
    margin: 0 auto;
    background: var(--bg-card);
    padding: 40px;
    border-radius: 16px;
    border: 1px solid var(--border);
    box-shadow: var(--shadow);
    transition: background-color 0.4s ease, border-color 0.4s ease;
}

.form-row {
    display: flex;
    gap: 20px;
}

.form-group {
    flex: 1;
    margin-bottom: 20px;
}

.form-group label {
    display: block;
    font-size: 0.9rem;
    font-weight: 600;
    color: var(--text);
    margin-bottom: 6px;
    transition: color 0.4s ease;
}

.form-group input,
.form-group textarea {
    width: 100%;
    padding: 12px 16px;
    font-size: 0.95rem;
    font-family: var(--font);
    color: var(--text);
    background: var(--bg-secondary);
    border: 2px solid var(--border);
    border-radius: 8px;
    outline: none;
    transition: var(--transition);
}

.form-group input:focus,
.form-group textarea:focus {
    border-color: var(--primary);
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.12);
    background: var(--bg);
}

.form-group input::placeholder,
.form-group textarea::placeholder {
    color: var(--text-muted);
}

.form-group textarea {
    min-height: 130px;
    resize: vertical;
}


/* ==========================================
   NOTIFICATION
   ========================================== */

#notification-container {
    position: fixed;
    top: 20px;
    right: 20px;
    z-index: 10000;
    display: flex;
    flex-direction: column;
    gap: 10px;
}

.notification {
    padding: 14px 24px;
    border-radius: 8px;
    color: white;
    font-weight: 500;
    font-size: 0.9rem;
    box-shadow: 0 4px 15px rgba(0, 0, 0, 0.15);
    transform: translateX(120%);
    transition: transform 0.4s ease;
    max-width: 350px;
}

.notification.show {
    transform: translateX(0);
}

.notification.success { background: #2ecc71; }
.notification.error { background: #e74c3c; }
.notification.info { background: var(--primary); }


/* ==========================================
   FOOTER
   ========================================== */

.footer {
    background: var(--header-bg);
    color: rgba(255, 255, 255, 0.5);
    text-align: center;
    padding: 25px;
    font-size: 0.85rem;
    transition: background-color 0.4s ease;
}


/* ==========================================
   RESPONSIVE
   ========================================== */

@media (max-width: 768px) {
    .hamburger { display: flex; }

    .nav {
        position: fixed;
        top: 0;
        right: -100%;
        width: 280px;
        height: 100vh;
        background: var(--header-bg);
        flex-direction: column;
        padding: 80px 30px;
        gap: 20px;
        transition: right 0.4s ease;
        z-index: 999;
    }

    .nav.open { right: 0; }

    .nav-link { font-size: 1.1rem; color: rgba(255, 255, 255, 0.8); }
    .nav-link:hover { color: white; }

    .hero { padding: 70px 0 50px; min-height: auto; }

    .feature-card { min-width: 100%; }

    .form-row { flex-direction: column; gap: 0; }

    .contact-form { padding: 25px 20px; }

    .stats-grid { flex-direction: column; gap: 20px; }
}

@media (max-width: 480px) {
    .hero-buttons {
        flex-direction: column;
        align-items: center;
    }

    .btn { width: 100%; }
}
```

### `js/script.js`:

```javascript
// ==========================================
// THEME SWITCHER + ANIMATED MENU
// ==========================================

const html = document.documentElement;
const themeToggle = document.querySelector("#theme-toggle");
const hamburger = document.querySelector("#hamburger");
const nav = document.querySelector("#nav");
const overlay = document.querySelector("#overlay");
const scrollBar = document.querySelector("#scroll-bar");
const contactForm = document.querySelector("#contact-form");
const heroTitle = document.querySelector("#hero-title");
const notificationContainer = document.querySelector("#notification-container");


// ==========================================
// 1. THEME SYSTEM
// ==========================================

const getPreferredTheme = () => {
    const saved = localStorage.getItem("theme");
    if (saved) return saved;
    return window.matchMedia("(prefers-color-scheme: dark)").matches ? "dark" : "light";
};

const applyTheme = (theme) => {
    if (theme === "dark") {
        html.classList.add("dark-theme");
    } else {
        html.classList.remove("dark-theme");
    }
    localStorage.setItem("theme", theme);
};

// Initialize theme
applyTheme(getPreferredTheme());

// Toggle theme
themeToggle.addEventListener("click", () => {
    const isDark = html.classList.contains("dark-theme");
    applyTheme(isDark ? "light" : "dark");
    showNotification(`Switched to ${isDark ? "light" : "dark"} mode`, "info");
});

// Listen for system theme changes
window.matchMedia("(prefers-color-scheme: dark)").addEventListener("change", (e) => {
    if (!localStorage.getItem("theme")) {
        applyTheme(e.matches ? "dark" : "light");
    }
});


// ==========================================
// 2. MOBILE MENU
// ==========================================

const toggleMenu = () => {
    const isOpen = nav.classList.contains("open");

    hamburger.classList.toggle("active");
    nav.classList.toggle("open");
    overlay.classList.toggle("active");
    document.body.style.overflow = isOpen ? "" : "hidden";
};

hamburger.addEventListener("click", toggleMenu);
overlay.addEventListener("click", toggleMenu);

// Close menu on link click
nav.querySelectorAll(".nav-link").forEach(link => {
    link.addEventListener("click", () => {
        if (nav.classList.contains("open")) toggleMenu();
    });
});

// Close menu on Escape
document.addEventListener("keydown", (e) => {
    if (e.key === "Escape" && nav.classList.contains("open")) toggleMenu();
});

// Close menu if resized to desktop
window.matchMedia("(max-width: 768px)").addEventListener("change", (e) => {
    if (!e.matches && nav.classList.contains("open")) toggleMenu();
});


// ==========================================
// 3. SCROLL PROGRESS BAR
// ==========================================

window.addEventListener("scroll", () => {
    const scrollTop = window.scrollY;
    const docHeight = document.documentElement.scrollHeight - window.innerHeight;
    const percent = docHeight > 0 ? (scrollTop / docHeight) * 100 : 0;
    scrollBar.style.width = `${percent}%`;
});


// ==========================================
// 4. SCROLL FADE-IN ANIMATIONS
// ==========================================

const fadeElements = document.querySelectorAll(".fade-in");

const fadeObserver = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            entry.target.classList.add("visible");
            fadeObserver.unobserve(entry.target);
        }
    });
}, { threshold: 0.15 });

fadeElements.forEach(el => fadeObserver.observe(el));


// ==========================================
// 5. ANIMATED COUNTER
// ==========================================

const animateCounter = (element, target, duration = 2000) => {
    const startTime = performance.now();

    const update = (currentTime) => {
        const elapsed = currentTime - startTime;
        const progress = Math.min(elapsed / duration, 1);
        const eased = 1 - Math.pow(1 - progress, 3);

        element.textContent = Math.floor(eased * target);

        if (progress < 1) {
            requestAnimationFrame(update);
        } else {
            element.textContent = target;
        }
    };

    requestAnimationFrame(update);
};

const counters = document.querySelectorAll("[data-count]");
const counterObserver = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            const target = parseInt(entry.target.dataset.count);
            animateCounter(entry.target, target);
            counterObserver.unobserve(entry.target);
        }
    });
}, { threshold: 0.5 });

counters.forEach(c => counterObserver.observe(c));


// ==========================================
// 6. TYPING EFFECT
// ==========================================

const typeWriter = (element, text, speed = 55) => {
    let index = 0;
    element.textContent = "";

    const type = () => {
        if (index < text.length) {
            element.textContent += text[index];
            index++;
            setTimeout(type, speed);
        }
    };

    type();
};

// Start typing after a short delay
setTimeout(() => {
    typeWriter(heroTitle, "We Build Digital Experiences");
}, 500);


// ==========================================
// 7. NOTIFICATION SYSTEM
// ==========================================

const showNotification = (message, type = "info", duration = 3000) => {
    const notification = document.createElement("div");
    notification.classList.add("notification", type);
    notification.textContent = message;
    notificationContainer.appendChild(notification);

    // Trigger enter animation
    requestAnimationFrame(() => {
        notification.classList.add("show");
    });

    // Auto-dismiss
    setTimeout(() => {
        notification.classList.remove("show");
        setTimeout(() => notification.remove(), 400);
    }, duration);
};


// ==========================================
// 8. CONTACT FORM
// ==========================================

contactForm.addEventListener("submit", (e) => {
    e.preventDefault();

    const name = document.querySelector("#name").value.trim();
    const email = document.querySelector("#email").value.trim();
    const message = document.querySelector("#message").value.trim();

    if (!name || !email || !message) {
        showNotification("Please fill in all fields.", "error");
        return;
    }

    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!emailRegex.test(email)) {
        showNotification("Please enter a valid email.", "error");
        return;
    }

    // Simulate form submission
    console.log("Form Data:", { name, email, message });
    showNotification("Message sent successfully! 🎉", "success", 4000);
    contactForm.reset();
});
```

---

## 4.11 Summary & Key Takeaways

| Concept | Key Point |
|---------|-----------|
| CSS + JS Separation | CSS defines styles; JS decides when to apply them |
| Class Toggling ✅ | Best way to change appearance — `classList.add/remove/toggle` |
| Inline Styles | Only for dynamic/calculated values — `element.style.property` |
| CSS Variables | Change theme-wide with `setProperty()` — most powerful approach |
| `animationend` / `transitionend` | Run JS after CSS animation completes |
| `IntersectionObserver` | Detect when elements enter viewport (scroll animations) |
| `matchMedia()` | JavaScript equivalent of CSS media queries |
| Theme Switching | CSS variables + class on `<html>` + `localStorage` |
| `localStorage` | Persist user preferences (theme, settings) across sessions |
| `prefers-color-scheme` | Detect system dark/light mode preference |
| Scroll Progress | `window.scrollY` / `scrollHeight` for percentage |
| Notifications | Dynamic element creation + CSS animations |
| Typing Effect | `setTimeout` recursion for character-by-character display |
| Animated Counters | `requestAnimationFrame` for smooth number animations |
| Hamburger Menu | Class toggling + CSS transitions for mobile navigation |

---

> 🎉 **Congratulations!** You've completed Chapter 4. You now understand how to control CSS with JavaScript — toggling classes, managing animations, using CSS variables, building theme switchers, handling responsive behavior, and creating polished UI interactions!

---