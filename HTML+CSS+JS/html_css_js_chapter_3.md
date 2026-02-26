

# 📘 Chapter 3: Events & User Interaction

> **🟡 React to user behavior — Mastering event listeners, common events, the event object, event bubbling & delegation, preventing default behavior, and building an interactive form with validation.**

---

## 3.1 What Are Events?

**Events** are things that **happen** on a web page — a user clicks a button, types in an input, scrolls the page, submits a form, hovers over an element, or presses a key. JavaScript can **listen** for these events and **react** to them.

### The Event Flow:

```
SOMETHING HAPPENS (event occurs)
        ↓
JAVASCRIPT LISTENS (event listener detects it)
        ↓
CODE RUNS (callback function executes)
```

### Real-World Event Examples:

| User Action | Event Name | Example Response |
|-------------|-----------|------------------|
| Clicks a button | `click` | Show a popup, submit form |
| Types in an input | `input` | Filter search results, character count |
| Submits a form | `submit` | Validate data, send to server |
| Hovers over element | `mouseenter` | Show tooltip, highlight |
| Presses a key | `keydown` | Keyboard shortcuts, game controls |
| Scrolls the page | `scroll` | Sticky nav, lazy load images |
| Page finishes loading | `DOMContentLoaded` | Initialize app, show content |
| Resizes the window | `resize` | Adjust layout, recalculate |

> 🧠 **Think of it this way:** Events are like doorbells. You don't stand at the door all day — you set up a doorbell (listener) that rings (triggers) when someone arrives (event), and then you react (callback function).

---

## 3.2 Adding Event Listeners — `addEventListener()`

The `addEventListener()` method is the **modern, professional way** to handle events in JavaScript.

### Syntax:

```javascript
element.addEventListener("eventType", callbackFunction);
```

| Part | Purpose | Example |
|------|---------|---------|
| `element` | The HTML element to listen on | `button`, `input`, `document` |
| `"eventType"` | Which event to listen for (string) | `"click"`, `"input"`, `"submit"` |
| `callbackFunction` | Function that runs when event happens | `() => { ... }` |

### Basic Click Event:

```javascript
const button = document.querySelector("#myButton");

button.addEventListener("click", () => {
    console.log("Button was clicked!");
});
```

### Named Function as Callback:

```javascript
// Define the function separately
const handleClick = () => {
    console.log("Button clicked!");
};

// Pass it as a callback
button.addEventListener("click", handleClick);
```

### Why Named Functions Are Better:

```javascript
// ✅ Named function — can be removed later
const handleClick = () => console.log("Clicked!");
button.addEventListener("click", handleClick);
button.removeEventListener("click", handleClick);   // ✅ Works!

// ❌ Anonymous function — CANNOT be removed
button.addEventListener("click", () => console.log("Clicked!"));
button.removeEventListener("click", () => console.log("Clicked!"));  // ❌ Doesn't work!
// Each arrow function creates a NEW reference — they're not the same function
```

### Multiple Listeners on the Same Element:

```javascript
const button = document.querySelector("#myButton");

// ✅ addEventListener allows MULTIPLE listeners on the same element
button.addEventListener("click", () => {
    console.log("First handler");
});

button.addEventListener("click", () => {
    console.log("Second handler");
});

// Both run when button is clicked!
// Output:
// First handler
// Second handler
```

### Old Way vs Modern Way:

```javascript
// ❌ OLD: onclick property (overwrites previous handler)
button.onclick = () => console.log("First");
button.onclick = () => console.log("Second");
// Only "Second" runs — first was overwritten!

// ✅ MODERN: addEventListener (multiple handlers allowed)
button.addEventListener("click", () => console.log("First"));
button.addEventListener("click", () => console.log("Second"));
// Both run!
```

> 📌 **Rule:** Always use `addEventListener()`. Never use `onclick`, `onsubmit`, or other inline `on*` attributes/properties.

---

## 3.3 Common Events — The Complete Guide

### Mouse Events:

```javascript
const box = document.querySelector(".box");

// Click — mouse press AND release on element
box.addEventListener("click", () => {
    console.log("Clicked!");
});

// Double click
box.addEventListener("dblclick", () => {
    console.log("Double clicked!");
});

// Mouse down — button pressed (before release)
box.addEventListener("mousedown", () => {
    console.log("Mouse button pressed down");
});

// Mouse up — button released
box.addEventListener("mouseup", () => {
    console.log("Mouse button released");
});

// Mouse enter — cursor enters the element (doesn't bubble)
box.addEventListener("mouseenter", () => {
    console.log("Mouse entered the box");
});

// Mouse leave — cursor leaves the element (doesn't bubble)
box.addEventListener("mouseleave", () => {
    console.log("Mouse left the box");
});

// Mouse move — cursor moves INSIDE the element
box.addEventListener("mousemove", (e) => {
    console.log(`Mouse at: ${e.clientX}, ${e.clientY}`);
});
```

### Keyboard Events:

```javascript
const input = document.querySelector("#search");

// Key down — key is pressed (fires BEFORE character appears)
input.addEventListener("keydown", (e) => {
    console.log(`Key pressed: ${e.key}`);
});

// Key up — key is released
input.addEventListener("keyup", (e) => {
    console.log(`Key released: ${e.key}`);
});

// Practical: Listen for Enter key
input.addEventListener("keydown", (e) => {
    if (e.key === "Enter") {
        console.log("Enter pressed! Submitting search...");
        console.log(`Search query: ${input.value}`);
    }
});

// Practical: Listen for Escape key
document.addEventListener("keydown", (e) => {
    if (e.key === "Escape") {
        console.log("Escape pressed! Closing modal...");
    }
});
```

### Common `e.key` Values:

| Key | `e.key` Value |
|-----|---------------|
| Enter | `"Enter"` |
| Escape | `"Escape"` |
| Space | `" "` |
| Tab | `"Tab"` |
| Backspace | `"Backspace"` |
| Arrow Up | `"ArrowUp"` |
| Arrow Down | `"ArrowDown"` |
| Arrow Left | `"ArrowLeft"` |
| Arrow Right | `"ArrowRight"` |
| Shift | `"Shift"` |
| Control | `"Control"` |
| a-z | `"a"`, `"b"`, ..., `"z"` |
| 0-9 | `"0"`, `"1"`, ..., `"9"` |

### Input / Form Events:

```javascript
const textInput = document.querySelector("#name");
const form = document.querySelector("#myForm");
const select = document.querySelector("#country");
const checkbox = document.querySelector("#agree");

// input — fires on EVERY change (real-time)
textInput.addEventListener("input", (e) => {
    console.log(`Current value: ${e.target.value}`);
});

// change — fires when value is COMMITTED (after blur for text, immediately for select/checkbox)
select.addEventListener("change", (e) => {
    console.log(`Selected: ${e.target.value}`);
});

checkbox.addEventListener("change", (e) => {
    console.log(`Checked: ${e.target.checked}`);
});

// focus — element receives focus (user clicks into it)
textInput.addEventListener("focus", () => {
    console.log("Input focused!");
});

// blur — element LOSES focus (user clicks away)
textInput.addEventListener("blur", () => {
    console.log("Input lost focus!");
});

// submit — form is submitted
form.addEventListener("submit", (e) => {
    e.preventDefault();     // Stop page reload!
    console.log("Form submitted!");
});
```

### `input` vs `change`:

```
input event:
User types: H → e → l → l → o
Fires:       ↑    ↑    ↑    ↑    ↑  (fires on EVERY keystroke)

change event:
User types: H-e-l-l-o → clicks away (blurs)
Fires:                                  ↑     (fires only when DONE)

For <select> and <input type="checkbox">:
Both fire immediately on change.
```

> 📌 **Use `input`** for real-time feedback (live search, character counter). **Use `change`** for final value (form submission, dropdown selection).

### Window / Document Events:

```javascript
// Page fully loaded (HTML parsed, DOM ready)
document.addEventListener("DOMContentLoaded", () => {
    console.log("DOM is ready!");
    // Safe to access all HTML elements here
});

// Everything loaded (images, CSS, scripts too)
window.addEventListener("load", () => {
    console.log("Everything loaded!");
});

// Window resized
window.addEventListener("resize", () => {
    console.log(`Window size: ${window.innerWidth}x${window.innerHeight}`);
});

// Page scrolled
window.addEventListener("scroll", () => {
    console.log(`Scroll position: ${window.scrollY}px`);
});
```

### Complete Events Reference Table:

| Category | Event | When It Fires |
|----------|-------|---------------|
| **Mouse** | `click` | Element is clicked |
| | `dblclick` | Element is double-clicked |
| | `mouseenter` | Cursor enters element |
| | `mouseleave` | Cursor leaves element |
| | `mousemove` | Cursor moves over element |
| **Keyboard** | `keydown` | Key is pressed |
| | `keyup` | Key is released |
| **Input** | `input` | Input value changes (real-time) |
| | `change` | Input value is committed |
| | `focus` | Element receives focus |
| | `blur` | Element loses focus |
| **Form** | `submit` | Form is submitted |
| | `reset` | Form is reset |
| **Window** | `DOMContentLoaded` | HTML parsed, DOM ready |
| | `load` | All resources loaded |
| | `resize` | Window size changes |
| | `scroll` | Page is scrolled |

---

## 3.4 The Event Object (`e`)

When an event fires, JavaScript automatically creates an **event object** containing **details about what happened**. You receive it as a parameter in your callback function.

### Accessing the Event Object:

```javascript
button.addEventListener("click", (e) => {
    console.log(e);          // The full event object
    console.log(e.type);     // "click"
    console.log(e.target);   // The element that was clicked
});

// The parameter name can be anything, but 'e' or 'event' is convention
button.addEventListener("click", (event) => {
    console.log(event.type);
});
```

### Key Event Object Properties:

#### `e.target` — The Element That Triggered the Event:

```javascript
document.addEventListener("click", (e) => {
    console.log("You clicked:", e.target);           // The exact element clicked
    console.log("Tag name:", e.target.tagName);      // "BUTTON", "A", "P", etc.
    console.log("Class list:", e.target.classList);   // DOMTokenList of classes
    console.log("Text:", e.target.textContent);      // Text content
    console.log("ID:", e.target.id);                 // ID attribute
});
```

### `e.target` vs `e.currentTarget`:

```html
<div id="parent">
    <button id="child">Click Me</button>
</div>
```

```javascript
const parent = document.querySelector("#parent");

parent.addEventListener("click", (e) => {
    console.log("target:", e.target);           // #child (what was ACTUALLY clicked)
    console.log("currentTarget:", e.currentTarget); // #parent (what the listener is ON)
});
```

```
e.target        → The element that TRIGGERED the event (what user actually clicked)
e.currentTarget → The element that the LISTENER is attached to
```

> 📌 **This difference is crucial for event delegation** (covered in section 3.6).

#### Mouse Event Properties:

```javascript
document.addEventListener("click", (e) => {
    // Coordinates
    console.log(e.clientX, e.clientY);   // Position relative to viewport
    console.log(e.pageX, e.pageY);       // Position relative to page (includes scroll)
    console.log(e.offsetX, e.offsetY);   // Position relative to the target element

    // Which mouse button
    console.log(e.button);
    // 0 = left, 1 = middle, 2 = right
});
```

#### Keyboard Event Properties:

```javascript
document.addEventListener("keydown", (e) => {
    console.log(e.key);         // The character: "a", "Enter", "Escape", "ArrowUp"
    console.log(e.code);        // Physical key: "KeyA", "Enter", "Escape", "ArrowUp"
    console.log(e.shiftKey);    // true if Shift was held
    console.log(e.ctrlKey);     // true if Ctrl/Cmd was held
    console.log(e.altKey);      // true if Alt was held
    console.log(e.metaKey);     // true if Meta (Windows/Cmd) was held

    // Detect keyboard shortcuts
    if (e.ctrlKey && e.key === "s") {
        e.preventDefault();     // Prevent browser's default save dialog
        console.log("Custom save triggered!");
    }

    if (e.ctrlKey && e.key === "k") {
        e.preventDefault();
        console.log("Search bar opened!");
    }
});
```

#### Input Event Properties:

```javascript
const input = document.querySelector("#search");

input.addEventListener("input", (e) => {
    console.log(e.target.value);     // Current text in the input
    console.log(e.target.value.length); // Character count
    console.log(e.inputType);        // "insertText", "deleteContentBackward", etc.
    console.log(e.data);             // The character that was inserted (or null)
});
```

### Event Object Summary:

| Property | Available On | Returns |
|----------|-------------|---------|
| `e.type` | All events | Event type string: `"click"`, `"keydown"` |
| `e.target` | All events | Element that triggered the event |
| `e.currentTarget` | All events | Element with the listener attached |
| `e.clientX` / `e.clientY` | Mouse events | Position relative to viewport |
| `e.pageX` / `e.pageY` | Mouse events | Position relative to page |
| `e.key` | Keyboard events | Key character: `"a"`, `"Enter"` |
| `e.code` | Keyboard events | Physical key code: `"KeyA"`, `"Enter"` |
| `e.shiftKey` | Keyboard/Mouse | `true` if Shift held |
| `e.ctrlKey` | Keyboard/Mouse | `true` if Ctrl/Cmd held |
| `e.altKey` | Keyboard/Mouse | `true` if Alt held |
| `e.target.value` | Input events | Current input field value |
| `e.target.checked` | Checkbox events | `true`/`false` checkbox state |
| `e.preventDefault()` | All events | Stops default browser behavior |
| `e.stopPropagation()` | All events | Stops event from bubbling |

---

## 3.5 `preventDefault()` — Stopping Default Behavior

Certain HTML elements have **built-in default behaviors**. `preventDefault()` stops those from happening.

### Default Behaviors:

| Element | Default Behavior |
|---------|-----------------|
| `<a href="...">` | Navigates to the URL |
| `<form>` submit | Reloads the page and sends data |
| `<input type="submit">` | Triggers form submission |
| Right-click | Shows context menu |
| `Ctrl+S` | Opens save dialog |
| Checkbox click | Toggles checked state |

### Preventing Form Submission Reload:

```javascript
// ❌ WITHOUT preventDefault — page reloads on submit
const form = document.querySelector("#myForm");

form.addEventListener("submit", () => {
    console.log("Form submitted!");
    // Page reloads immediately! This log may not even appear.
});


// ✅ WITH preventDefault — page stays, JS handles everything
form.addEventListener("submit", (e) => {
    e.preventDefault();     // STOP the page from reloading!
    console.log("Form submitted via JavaScript!");

    // Now we can process the form data with JS
    const name = document.querySelector("#name").value;
    const email = document.querySelector("#email").value;
    console.log(`Name: ${name}, Email: ${email}`);
});
```

> 📌 **This is the #1 most important use of `preventDefault()`** — handling form submissions with JavaScript instead of letting the browser reload the page.

### Preventing Link Navigation:

```javascript
const link = document.querySelector("a.custom-action");

link.addEventListener("click", (e) => {
    e.preventDefault();     // Don't navigate to href
    console.log("Custom action instead of navigation!");
});
```

### Preventing Right-Click Menu:

```javascript
document.addEventListener("contextmenu", (e) => {
    e.preventDefault();
    console.log("Custom right-click menu could appear here!");
});
```

### Practical: Confirm Before Leaving Page:

```javascript
// Prevent accidental navigation
const unsavedLinks = document.querySelectorAll("a.warn-unsaved");

unsavedLinks.forEach(link => {
    link.addEventListener("click", (e) => {
        const confirmed = confirm("You have unsaved changes. Leave anyway?");
        if (!confirmed) {
            e.preventDefault();     // Stay on the page
        }
    });
});
```

---

## 3.6 Event Bubbling & Event Delegation

This is one of the **most important and powerful concepts** in JavaScript event handling.

### What is Event Bubbling?

When an event occurs on an element, it doesn't just fire on that element — it **bubbles up** through ALL of its parent elements, all the way to the `document`.

```html
<div id="grandparent">
    <div id="parent">
        <button id="child">Click Me</button>
    </div>
</div>
```

```javascript
document.querySelector("#grandparent").addEventListener("click", () => {
    console.log("Grandparent clicked!");
});

document.querySelector("#parent").addEventListener("click", () => {
    console.log("Parent clicked!");
});

document.querySelector("#child").addEventListener("click", () => {
    console.log("Child clicked!");
});
```

### When You Click the Button:

```
User clicks the <button>

The event BUBBLES UP:
Step 1: button fires    → "Child clicked!"
Step 2: #parent fires   → "Parent clicked!"
Step 3: #grandparent    → "Grandparent clicked!"
Step 4: body, html, document (if they have listeners)

Output order:
Child clicked!
Parent clicked!
Grandparent clicked!
```

### Visual Representation:

```
Click on button:
                                    ↑ BUBBLING (event travels UP)
┌──────────────────────────────────┐
│  #grandparent                   3│  ← "Grandparent clicked!"
│  ┌───────────────────────────┐   │
│  │  #parent                 2│   │  ← "Parent clicked!"
│  │  ┌─────────────────────┐  │   │
│  │  │  #child (button)   1│  │   │  ← "Child clicked!" (STARTS HERE)
│  │  └─────────────────────┘  │   │
│  └───────────────────────────┘   │
└──────────────────────────────────┘
```

### Stopping Bubbling — `stopPropagation()`:

```javascript
document.querySelector("#child").addEventListener("click", (e) => {
    e.stopPropagation();    // STOP the event from bubbling up!
    console.log("Child clicked — bubbling stopped!");
});

document.querySelector("#parent").addEventListener("click", () => {
    console.log("This will NOT fire when child is clicked!");
});
```

> ⚠️ **Use `stopPropagation()` sparingly.** Stopping bubbling can break event delegation patterns and make debugging harder. Only use it when you have a specific reason.

---

### Event Delegation — The Power Pattern

**Event delegation** leverages bubbling to handle events for **many elements with a single listener** on their parent. This is **faster, cleaner, and works for dynamically created elements**.

### The Problem WITHOUT Delegation:

```javascript
// ❌ BAD: Adding a listener to EACH item (slow, doesn't work for new items)
const items = document.querySelectorAll(".list-item");

items.forEach(item => {
    item.addEventListener("click", () => {
        console.log(`Clicked: ${item.textContent}`);
    });
});

// If you add a NEW item later, it WON'T have a listener!
const newItem = document.createElement("li");
newItem.textContent = "New Item";
newItem.classList.add("list-item");
document.querySelector("ul").appendChild(newItem);
// Clicking "New Item" does NOTHING — no listener was added to it!
```

### The Solution WITH Delegation:

```javascript
// ✅ GOOD: ONE listener on the PARENT handles ALL children (even future ones)
const list = document.querySelector("ul");

list.addEventListener("click", (e) => {
    // Check if the clicked element is a list item
    if (e.target.tagName === "LI") {
        console.log(`Clicked: ${e.target.textContent}`);
    }
});

// Now if you add NEW items, they work automatically!
const newItem = document.createElement("li");
newItem.textContent = "New Item";
list.appendChild(newItem);
// Clicking "New Item" WORKS — the parent listener catches it via bubbling!
```

### How Delegation Works:

```
1. User clicks a <li> item
2. Event BUBBLES UP to the <ul> parent
3. The parent's listener fires
4. e.target tells us EXACTLY which <li> was clicked
5. We check if e.target is the type of element we care about
6. If yes, we handle it!

Advantages:
✅ ONE listener instead of N listeners (better performance)
✅ Works for elements added LATER (dynamically created)
✅ Less memory usage
✅ Cleaner code
```

### Delegation with `closest()`:

When elements are nested, `e.target` might be a child of the element you actually want. Use `closest()` to find the right ancestor.

```html
<ul class="task-list">
    <li class="task-item">
        <span class="task-text">Buy groceries</span>
        <button class="delete-btn">✕</button>
    </li>
    <li class="task-item">
        <span class="task-text">Clean house</span>
        <button class="delete-btn">✕</button>
    </li>
</ul>
```

```javascript
const taskList = document.querySelector(".task-list");

taskList.addEventListener("click", (e) => {
    // User might click the <span>, the <button>, or the <li> itself

    // Handle delete button clicks
    if (e.target.classList.contains("delete-btn")) {
        const taskItem = e.target.closest(".task-item");
        taskItem.remove();
        console.log("Task deleted!");
    }

    // Handle clicking on the task text
    if (e.target.classList.contains("task-text")) {
        e.target.classList.toggle("completed");
        console.log("Task toggled!");
    }
});
```

### Delegation Using `matches()`:

```javascript
taskList.addEventListener("click", (e) => {
    // matches() checks if the element matches a CSS selector
    if (e.target.matches(".delete-btn")) {
        e.target.closest(".task-item").remove();
    }

    if (e.target.matches(".task-text")) {
        e.target.classList.toggle("completed");
    }
});
```

### Complete Delegation Pattern:

```javascript
// THE DELEGATION PATTERN
parentElement.addEventListener("eventType", (e) => {
    // Step 1: Identify what was clicked
    const target = e.target.closest(".desired-element");

    // Step 2: If it's not what we want, exit early
    if (!target) return;

    // Step 3: Make sure it's INSIDE our parent (safety check)
    if (!parentElement.contains(target)) return;

    // Step 4: Handle the event
    console.log("Delegated event handled!", target);
});
```

---

## 3.7 Removing Event Listeners

Sometimes you need to **remove** an event listener (e.g., after a one-time action, or when a component is destroyed).

### Using Named Functions:

```javascript
const handleClick = () => {
    console.log("Clicked!");
};

// Add the listener
button.addEventListener("click", handleClick);

// Remove the listener
button.removeEventListener("click", handleClick);
```

### One-Time Event with `{ once: true }`:

```javascript
// This listener fires ONLY ONCE, then automatically removes itself
button.addEventListener("click", () => {
    console.log("This only fires once!");
}, { once: true });

// First click: "This only fires once!" ← fires
// Second click: nothing happens ← listener is gone
```

### Practical: Self-Removing Listener:

```javascript
const welcomePopup = document.querySelector(".welcome-popup");
const closeBtn = welcomePopup.querySelector(".close-btn");

const closePopup = () => {
    welcomePopup.remove();
    closeBtn.removeEventListener("click", closePopup);
    console.log("Popup closed and listener removed!");
};

closeBtn.addEventListener("click", closePopup);
```

---

## 3.8 Practical Event Patterns

### Pattern 1: Real-Time Character Counter

```html
<div class="form-group">
    <label for="bio">Bio</label>
    <textarea id="bio" maxlength="200" placeholder="Tell us about yourself..."></textarea>
    <p class="char-count"><span id="count">0</span> / 200</p>
</div>
```

```css
.char-count { font-size: 0.85rem; color: #888; text-align: right; }
.char-count.warning { color: #e74c3c; font-weight: 600; }
```

```javascript
const textarea = document.querySelector("#bio");
const countDisplay = document.querySelector("#count");
const charCountEl = document.querySelector(".char-count");
const maxLength = 200;

textarea.addEventListener("input", () => {
    const currentLength = textarea.value.length;
    countDisplay.textContent = currentLength;

    if (currentLength >= maxLength * 0.9) {
        charCountEl.classList.add("warning");
    } else {
        charCountEl.classList.remove("warning");
    }
});
```

### Pattern 2: Live Search Filter

```html
<input type="search" id="search" placeholder="Search users...">
<ul id="user-list">
    <li>Ali Khan</li>
    <li>Sara Ahmed</li>
    <li>Omar Farooq</li>
    <li>Fatima Noor</li>
    <li>Hassan Ali</li>
</ul>
```

```javascript
const searchInput = document.querySelector("#search");
const userItems = document.querySelectorAll("#user-list li");

searchInput.addEventListener("input", () => {
    const query = searchInput.value.toLowerCase().trim();

    userItems.forEach(item => {
        const name = item.textContent.toLowerCase();

        if (name.includes(query)) {
            item.style.display = "";        // Show
        } else {
            item.style.display = "none";    // Hide
        }
    });
});
```

### Pattern 3: Toggle Password Visibility

```html
<div class="password-field">
    <input type="password" id="password" placeholder="Enter password">
    <button type="button" id="toggle-password">Show</button>
</div>
```

```javascript
const passwordInput = document.querySelector("#password");
const toggleBtn = document.querySelector("#toggle-password");

toggleBtn.addEventListener("click", () => {
    const isPassword = passwordInput.type === "password";

    passwordInput.type = isPassword ? "text" : "password";
    toggleBtn.textContent = isPassword ? "Hide" : "Show";
});
```

### Pattern 4: Scroll-Based Navbar

```javascript
const navbar = document.querySelector(".navbar");
let lastScrollY = 0;

window.addEventListener("scroll", () => {
    const currentScrollY = window.scrollY;

    if (currentScrollY > 100) {
        navbar.classList.add("scrolled");
    } else {
        navbar.classList.remove("scrolled");
    }

    // Hide on scroll down, show on scroll up
    if (currentScrollY > lastScrollY && currentScrollY > 200) {
        navbar.classList.add("hidden");
    } else {
        navbar.classList.remove("hidden");
    }

    lastScrollY = currentScrollY;
});
```

```css
.navbar {
    position: sticky;
    top: 0;
    transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.navbar.scrolled {
    box-shadow: 0 2px 15px rgba(0, 0, 0, 0.1);
}

.navbar.hidden {
    transform: translateY(-100%);
}
```

### Pattern 5: Keyboard Navigation

```javascript
const items = document.querySelectorAll(".menu-item");
let currentIndex = 0;

const updateFocus = () => {
    items.forEach(item => item.classList.remove("focused"));
    items[currentIndex].classList.add("focused");
    items[currentIndex].focus();
};

document.addEventListener("keydown", (e) => {
    if (e.key === "ArrowDown") {
        e.preventDefault();
        currentIndex = (currentIndex + 1) % items.length;
        updateFocus();
    }

    if (e.key === "ArrowUp") {
        e.preventDefault();
        currentIndex = (currentIndex - 1 + items.length) % items.length;
        updateFocus();
    }

    if (e.key === "Enter") {
        items[currentIndex].click();
    }
});
```

### Pattern 6: Debouncing (Performance Optimization)

When events fire rapidly (scroll, resize, input), you don't want to run expensive code on EVERY event. **Debouncing** delays execution until the user stops.

```javascript
// Debounce function — waits until user stops for [delay] ms
const debounce = (func, delay) => {
    let timeoutId;
    return (...args) => {
        clearTimeout(timeoutId);
        timeoutId = setTimeout(() => func(...args), delay);
    };
};

// Usage: search only after user stops typing for 300ms
const searchInput = document.querySelector("#search");

const performSearch = (query) => {
    console.log(`Searching for: ${query}`);
    // Imagine this calls an API — you don't want it on EVERY keystroke
};

searchInput.addEventListener("input", debounce((e) => {
    performSearch(e.target.value);
}, 300));
```

```
Without debounce:
User types "hello"
→ Search "h"
→ Search "he"
→ Search "hel"
→ Search "hell"
→ Search "hello"     ← 5 searches! Wasteful!

With debounce (300ms):
User types "hello" (each keystroke within 300ms of the next)
→ ... waits ...
→ Search "hello"     ← 1 search! Efficient!
```

---

## 3.9 Mini-Project: Interactive Form with Validation 🏆

Let's build a **real signup form** with comprehensive JavaScript validation, real-time feedback, and professional UX patterns.

### File Structure:

```
form-validation/
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
    <title>Signup Form — Events Project</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="css/style.css">
</head>
<body>

    <main class="container">
        <div class="form-card">
            <header class="form-header">
                <h1>Create Account</h1>
                <p>Join us today — it's free and takes less than a minute.</p>
            </header>

            <form id="signup-form" novalidate>

                <!-- Name Field -->
                <div class="form-group">
                    <label for="fullname">Full Name</label>
                    <input type="text" id="fullname" name="fullname"
                           placeholder="Enter your full name" autocomplete="name">
                    <p class="error-message" id="fullname-error"></p>
                </div>

                <!-- Email Field -->
                <div class="form-group">
                    <label for="email">Email Address</label>
                    <input type="email" id="email" name="email"
                           placeholder="you@example.com" autocomplete="email">
                    <p class="error-message" id="email-error"></p>
                </div>

                <!-- Password Field -->
                <div class="form-group">
                    <label for="password">Password</label>
                    <div class="password-wrapper">
                        <input type="password" id="password" name="password"
                               placeholder="Create a password">
                        <button type="button" id="toggle-password" class="toggle-btn">Show</button>
                    </div>
                    <p class="error-message" id="password-error"></p>
                    <div class="password-strength" id="strength-bar">
                        <div class="strength-fill" id="strength-fill"></div>
                    </div>
                    <p class="strength-text" id="strength-text"></p>
                </div>

                <!-- Confirm Password Field -->
                <div class="form-group">
                    <label for="confirm-password">Confirm Password</label>
                    <input type="password" id="confirm-password" name="confirm-password"
                           placeholder="Confirm your password">
                    <p class="error-message" id="confirm-password-error"></p>
                </div>

                <!-- Terms Checkbox -->
                <div class="form-group checkbox-group">
                    <input type="checkbox" id="terms" name="terms">
                    <label for="terms">I agree to the <a href="#" class="link">Terms & Conditions</a></label>
                    <p class="error-message" id="terms-error"></p>
                </div>

                <!-- Submit Button -->
                <button type="submit" class="btn-submit" id="submit-btn">
                    Create Account
                </button>

            </form>

            <!-- Success Message (hidden initially) -->
            <div class="success-message hidden" id="success-message">
                <div class="success-icon">✓</div>
                <h2>Account Created!</h2>
                <p>Welcome aboard! Your account has been successfully created.</p>
                <button type="button" class="btn-reset" id="reset-btn">Create Another</button>
            </div>
        </div>
    </main>

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
    --primary: #667eea;
    --primary-dark: #5a6fd6;
    --success: #2ecc71;
    --error: #e74c3c;
    --warning: #f39c12;
    --dark: #1a1a2e;
    --text: #333;
    --text-light: #777;
    --bg: #f0f2f5;
    --white: #fff;
    --border: #dde1e7;
    --border-focus: #667eea;
    --radius: 8px;
    --shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
    --transition: all 0.3s ease;
    --font: 'Inter', sans-serif;
}

body {
    font-family: var(--font);
    font-size: 16px;
    line-height: 1.6;
    color: var(--text);
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 40px 20px;
}

/* ==========================================
   FORM CARD
   ========================================== */

.container {
    width: 100%;
    max-width: 480px;
}

.form-card {
    background: var(--white);
    border-radius: 16px;
    padding: 40px 35px;
    box-shadow: 0 15px 50px rgba(0, 0, 0, 0.15);
}

.form-header {
    text-align: center;
    margin-bottom: 30px;
}

.form-header h1 {
    font-size: 1.8rem;
    color: var(--dark);
    margin-bottom: 6px;
}

.form-header p {
    color: var(--text-light);
    font-size: 0.9rem;
}

/* ==========================================
   FORM FIELDS
   ========================================== */

.form-group {
    margin-bottom: 20px;
}

.form-group label {
    display: block;
    font-size: 0.9rem;
    font-weight: 600;
    color: var(--text);
    margin-bottom: 6px;
}

.form-group input[type="text"],
.form-group input[type="email"],
.form-group input[type="password"] {
    width: 100%;
    padding: 12px 16px;
    font-size: 0.95rem;
    font-family: var(--font);
    color: var(--text);
    background: var(--bg);
    border: 2px solid var(--border);
    border-radius: var(--radius);
    outline: none;
    transition: var(--transition);
}

.form-group input:focus {
    border-color: var(--border-focus);
    background: var(--white);
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.12);
}

.form-group input.valid {
    border-color: var(--success);
}

.form-group input.valid:focus {
    box-shadow: 0 0 0 3px rgba(46, 204, 113, 0.12);
}

.form-group input.invalid {
    border-color: var(--error);
}

.form-group input.invalid:focus {
    box-shadow: 0 0 0 3px rgba(231, 76, 60, 0.12);
}

input::placeholder {
    color: #aab;
}

/* ==========================================
   PASSWORD WRAPPER
   ========================================== */

.password-wrapper {
    position: relative;
    display: flex;
}

.password-wrapper input {
    padding-right: 70px;
}

.toggle-btn {
    position: absolute;
    right: 12px;
    top: 50%;
    transform: translateY(-50%);
    background: none;
    border: none;
    color: var(--primary);
    font-size: 0.85rem;
    font-weight: 600;
    font-family: var(--font);
    cursor: pointer;
    padding: 4px 8px;
}

.toggle-btn:hover {
    color: var(--primary-dark);
}

/* ==========================================
   PASSWORD STRENGTH BAR
   ========================================== */

.password-strength {
    height: 4px;
    background: var(--border);
    border-radius: 2px;
    margin-top: 8px;
    overflow: hidden;
}

.strength-fill {
    height: 100%;
    width: 0;
    border-radius: 2px;
    transition: width 0.4s ease, background-color 0.4s ease;
}

.strength-fill.weak { width: 25%; background-color: var(--error); }
.strength-fill.fair { width: 50%; background-color: var(--warning); }
.strength-fill.good { width: 75%; background-color: #3498db; }
.strength-fill.strong { width: 100%; background-color: var(--success); }

.strength-text {
    font-size: 0.8rem;
    margin-top: 4px;
    font-weight: 500;
    min-height: 18px;
}

.strength-text.weak { color: var(--error); }
.strength-text.fair { color: var(--warning); }
.strength-text.good { color: #3498db; }
.strength-text.strong { color: var(--success); }

/* ==========================================
   ERROR MESSAGES
   ========================================== */

.error-message {
    font-size: 0.8rem;
    color: var(--error);
    margin-top: 4px;
    min-height: 18px;
    font-weight: 500;
}

/* ==========================================
   CHECKBOX
   ========================================== */

.checkbox-group {
    display: flex;
    flex-wrap: wrap;
    align-items: flex-start;
    gap: 8px;
}

.checkbox-group input[type="checkbox"] {
    width: 18px;
    height: 18px;
    margin-top: 2px;
    cursor: pointer;
    accent-color: var(--primary);
}

.checkbox-group label {
    font-weight: 400;
    font-size: 0.9rem;
    cursor: pointer;
    margin-bottom: 0;
    flex: 1;
}

.checkbox-group .error-message {
    width: 100%;
}

.link {
    color: var(--primary);
    text-decoration: none;
    font-weight: 600;
}

.link:hover {
    text-decoration: underline;
}

/* ==========================================
   SUBMIT BUTTON
   ========================================== */

.btn-submit {
    width: 100%;
    padding: 14px;
    font-size: 1rem;
    font-family: var(--font);
    font-weight: 600;
    color: var(--white);
    background: linear-gradient(135deg, var(--primary), #764ba2);
    border: none;
    border-radius: var(--radius);
    cursor: pointer;
    transition: var(--transition);
    margin-top: 10px;
}

.btn-submit:hover {
    transform: translateY(-2px);
    box-shadow: 0 6px 20px rgba(102, 126, 234, 0.35);
}

.btn-submit:active {
    transform: translateY(0);
}

.btn-submit:disabled {
    opacity: 0.6;
    cursor: not-allowed;
    transform: none;
    box-shadow: none;
}

/* ==========================================
   SUCCESS MESSAGE
   ========================================== */

.hidden { display: none; }

.success-message {
    text-align: center;
    padding: 20px 0;
}

.success-icon {
    width: 70px;
    height: 70px;
    border-radius: 50%;
    background: var(--success);
    color: white;
    font-size: 2rem;
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 0 auto 20px;
}

.success-message h2 {
    color: var(--dark);
    margin-bottom: 8px;
}

.success-message p {
    color: var(--text-light);
    margin-bottom: 25px;
}

.btn-reset {
    padding: 12px 30px;
    font-size: 0.95rem;
    font-family: var(--font);
    font-weight: 600;
    color: var(--primary);
    background: transparent;
    border: 2px solid var(--primary);
    border-radius: var(--radius);
    cursor: pointer;
    transition: var(--transition);
}

.btn-reset:hover {
    background: var(--primary);
    color: var(--white);
}

/* ==========================================
   RESPONSIVE
   ========================================== */

@media (max-width: 480px) {
    .form-card {
        padding: 30px 20px;
    }

    .form-header h1 {
        font-size: 1.5rem;
    }
}
```

### `js/script.js`:

```javascript
// ==========================================
// SIGNUP FORM VALIDATION — JavaScript
// ==========================================

// --- Element References ---
const form = document.querySelector("#signup-form");
const fullnameInput = document.querySelector("#fullname");
const emailInput = document.querySelector("#email");
const passwordInput = document.querySelector("#password");
const confirmPasswordInput = document.querySelector("#confirm-password");
const termsCheckbox = document.querySelector("#terms");
const togglePasswordBtn = document.querySelector("#toggle-password");
const submitBtn = document.querySelector("#submit-btn");
const successMessage = document.querySelector("#success-message");
const resetBtn = document.querySelector("#reset-btn");

// Error message elements
const fullnameError = document.querySelector("#fullname-error");
const emailError = document.querySelector("#email-error");
const passwordError = document.querySelector("#password-error");
const confirmPasswordError = document.querySelector("#confirm-password-error");
const termsError = document.querySelector("#terms-error");

// Password strength elements
const strengthFill = document.querySelector("#strength-fill");
const strengthText = document.querySelector("#strength-text");


// ==========================================
// VALIDATION FUNCTIONS
// ==========================================

const validateName = () => {
    const value = fullnameInput.value.trim();

    if (value === "") {
        showError(fullnameInput, fullnameError, "Full name is required.");
        return false;
    }

    if (value.length < 2) {
        showError(fullnameInput, fullnameError, "Name must be at least 2 characters.");
        return false;
    }

    if (value.length > 50) {
        showError(fullnameInput, fullnameError, "Name must be under 50 characters.");
        return false;
    }

    showSuccess(fullnameInput, fullnameError);
    return true;
};


const validateEmail = () => {
    const value = emailInput.value.trim();
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;

    if (value === "") {
        showError(emailInput, emailError, "Email is required.");
        return false;
    }

    if (!emailRegex.test(value)) {
        showError(emailInput, emailError, "Please enter a valid email address.");
        return false;
    }

    showSuccess(emailInput, emailError);
    return true;
};


const validatePassword = () => {
    const value = passwordInput.value;

    if (value === "") {
        showError(passwordInput, passwordError, "Password is required.");
        resetStrengthBar();
        return false;
    }

    if (value.length < 8) {
        showError(passwordInput, passwordError, "Password must be at least 8 characters.");
        return false;
    }

    showSuccess(passwordInput, passwordError);

    // Also re-validate confirm password if it has a value
    if (confirmPasswordInput.value !== "") {
        validateConfirmPassword();
    }

    return true;
};


const validateConfirmPassword = () => {
    const value = confirmPasswordInput.value;

    if (value === "") {
        showError(confirmPasswordInput, confirmPasswordError, "Please confirm your password.");
        return false;
    }

    if (value !== passwordInput.value) {
        showError(confirmPasswordInput, confirmPasswordError, "Passwords do not match.");
        return false;
    }

    showSuccess(confirmPasswordInput, confirmPasswordError);
    return true;
};


const validateTerms = () => {
    if (!termsCheckbox.checked) {
        termsError.textContent = "You must agree to the terms.";
        return false;
    }

    termsError.textContent = "";
    return true;
};


// ==========================================
// HELPER FUNCTIONS
// ==========================================

const showError = (input, errorElement, message) => {
    input.classList.add("invalid");
    input.classList.remove("valid");
    errorElement.textContent = message;
};

const showSuccess = (input, errorElement) => {
    input.classList.remove("invalid");
    input.classList.add("valid");
    errorElement.textContent = "";
};


// ==========================================
// PASSWORD STRENGTH CHECKER
// ==========================================

const checkPasswordStrength = (password) => {
    let score = 0;

    if (password.length === 0) {
        resetStrengthBar();
        return;
    }

    // Length checks
    if (password.length >= 8) score++;
    if (password.length >= 12) score++;

    // Character variety checks
    if (/[a-z]/.test(password) && /[A-Z]/.test(password)) score++;
    if (/[0-9]/.test(password)) score++;
    if (/[^a-zA-Z0-9]/.test(password)) score++;

    // Update UI
    strengthFill.className = "strength-fill";
    strengthText.className = "strength-text";

    if (score <= 1) {
        strengthFill.classList.add("weak");
        strengthText.classList.add("weak");
        strengthText.textContent = "Weak password";
    } else if (score <= 2) {
        strengthFill.classList.add("fair");
        strengthText.classList.add("fair");
        strengthText.textContent = "Fair password";
    } else if (score <= 3) {
        strengthFill.classList.add("good");
        strengthText.classList.add("good");
        strengthText.textContent = "Good password";
    } else {
        strengthFill.classList.add("strong");
        strengthText.classList.add("strong");
        strengthText.textContent = "Strong password!";
    }
};

const resetStrengthBar = () => {
    strengthFill.className = "strength-fill";
    strengthText.className = "strength-text";
    strengthText.textContent = "";
};


// ==========================================
// EVENT LISTENERS — Real-time Validation
// ==========================================

// Validate on blur (when user leaves the field)
fullnameInput.addEventListener("blur", validateName);
emailInput.addEventListener("blur", validateEmail);
passwordInput.addEventListener("blur", validatePassword);
confirmPasswordInput.addEventListener("blur", validateConfirmPassword);
termsCheckbox.addEventListener("change", validateTerms);

// Real-time password strength check
passwordInput.addEventListener("input", () => {
    checkPasswordStrength(passwordInput.value);

    // Clear error while typing (only show errors on blur)
    if (passwordInput.classList.contains("invalid")) {
        validatePassword();
    }
});

// Real-time confirm password match
confirmPasswordInput.addEventListener("input", () => {
    if (confirmPasswordInput.classList.contains("invalid")) {
        validateConfirmPassword();
    }
});

// Toggle password visibility
togglePasswordBtn.addEventListener("click", () => {
    const isPassword = passwordInput.type === "password";
    passwordInput.type = isPassword ? "text" : "password";
    confirmPasswordInput.type = isPassword ? "text" : "password";
    togglePasswordBtn.textContent = isPassword ? "Hide" : "Show";
});


// ==========================================
// FORM SUBMISSION
// ==========================================

form.addEventListener("submit", (e) => {
    e.preventDefault();     // Prevent page reload

    // Run ALL validations
    const isNameValid = validateName();
    const isEmailValid = validateEmail();
    const isPasswordValid = validatePassword();
    const isConfirmValid = validateConfirmPassword();
    const isTermsValid = validateTerms();

    // If ALL pass, show success
    if (isNameValid && isEmailValid && isPasswordValid && isConfirmValid && isTermsValid) {

        // Collect form data
        const formData = {
            name: fullnameInput.value.trim(),
            email: emailInput.value.trim(),
            password: passwordInput.value
        };

        console.log("Form submitted successfully!");
        console.table(formData);

        // Show success message, hide form
        form.classList.add("hidden");
        successMessage.classList.remove("hidden");

    } else {
        console.log("Form has errors — please fix them.");

        // Focus the first invalid field
        const firstInvalid = form.querySelector(".invalid");
        if (firstInvalid) firstInvalid.focus();
    }
});


// ==========================================
// RESET — Create Another Account
// ==========================================

resetBtn.addEventListener("click", () => {
    // Reset the form
    form.reset();

    // Clear all validation states
    const inputs = form.querySelectorAll("input[type='text'], input[type='email'], input[type='password']");
    inputs.forEach(input => {
        input.classList.remove("valid", "invalid");
    });

    // Clear all error messages
    const errors = form.querySelectorAll(".error-message");
    errors.forEach(error => {
        error.textContent = "";
    });

    // Reset password strength
    resetStrengthBar();

    // Reset password visibility
    passwordInput.type = "password";
    confirmPasswordInput.type = "password";
    togglePasswordBtn.textContent = "Show";

    // Show form, hide success
    form.classList.remove("hidden");
    successMessage.classList.add("hidden");

    // Focus first field
    fullnameInput.focus();
});


// ==========================================
// KEYBOARD SHORTCUT — Ctrl+Enter to Submit
// ==========================================

form.addEventListener("keydown", (e) => {
    if (e.ctrlKey && e.key === "Enter") {
        e.preventDefault();
        submitBtn.click();
    }
});
```

### What This Project Demonstrates:

```
✅ addEventListener for click, blur, input, change, submit, keydown
✅ preventDefault() on form submission
✅ Event object (e.target, e.key, e.ctrlKey)
✅ Real-time validation on input and blur events
✅ Password visibility toggle
✅ Password strength meter with scoring
✅ Multiple validation functions
✅ Error message display and clearing
✅ CSS class toggling (valid, invalid, hidden)
✅ Form data collection
✅ console.table for debugging
✅ Focus management (focus first invalid field)
✅ Form reset functionality
✅ Keyboard shortcut (Ctrl+Enter)
✅ Separation of concerns (HTML structure, CSS style, JS behavior)
✅ Clean code organization with comments
```

---

## 3.10 Complete Events Reference

### Adding/Removing Listeners:

| Method | Purpose | Example |
|--------|---------|---------|
| `addEventListener(type, fn)` | Add event listener | `el.addEventListener("click", fn)` |
| `removeEventListener(type, fn)` | Remove event listener | `el.removeEventListener("click", fn)` |
| `{ once: true }` | Auto-remove after first fire | `el.addEventListener("click", fn, { once: true })` |

### Event Object Properties:

| Property | Purpose |
|----------|---------|
| `e.type` | Event type string |
| `e.target` | Element that triggered event |
| `e.currentTarget` | Element with the listener |
| `e.preventDefault()` | Stop default behavior |
| `e.stopPropagation()` | Stop event bubbling |
| `e.key` | Key pressed (keyboard) |
| `e.ctrlKey` / `e.shiftKey` | Modifier keys held |
| `e.clientX` / `e.clientY` | Mouse position (viewport) |
| `e.target.value` | Input field value |
| `e.target.checked` | Checkbox state |

### Event Delegation Pattern:

```javascript
parent.addEventListener("eventType", (e) => {
    const target = e.target.closest(".desired-element");
    if (!target) return;
    // Handle event for target
});
```

---

## 3.11 Summary & Key Takeaways

| Concept | Key Point |
|---------|-----------|
| Events | Things that happen on a page (clicks, typing, scrolling) |
| `addEventListener()` | Modern way to listen for events — always use this |
| Callback Function | The function that runs when an event fires |
| Event Object (`e`) | Contains details about the event (target, key, position) |
| `e.target` | The element that triggered the event |
| `e.preventDefault()` | Stops default browser behavior (form reload, link navigation) |
| `e.stopPropagation()` | Stops event from bubbling to parent elements |
| Event Bubbling | Events travel UP from child to parent to grandparent |
| Event Delegation | One listener on parent handles all children (even future ones) |
| `closest()` | Find nearest ancestor matching selector (essential for delegation) |
| `input` event | Fires on every change (real-time) |
| `change` event | Fires when value is committed |
| `blur` event | Fires when element loses focus |
| `submit` event | Fires when form is submitted |
| `keydown` event | Fires when key is pressed |
| `{ once: true }` | Listener fires only once then removes itself |
| Debouncing | Delay execution until user stops (performance) |
| Named functions | Can be removed with `removeEventListener` |

---

> 🎉 **Congratulations!** You've completed Chapter 3. You now understand event listeners, all common events, the event object, event bubbling and delegation, preventing default behavior, and you've built a fully interactive form with real-time validation!

---