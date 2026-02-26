

# Chapter 6 — Forms, Validation & UX Patterns

> **Goal:** Professional front-end behavior — Learn how to handle forms properly, validate user input, show meaningful errors, and create accessible, user-friendly experiences.

---

## 1 — Form Events & Submission

### How Forms Work in the Browser

By default, when a form is submitted, the browser **reloads the page** and sends data to a server. In modern front-end development, we **prevent that** and handle everything with JavaScript.

```html
<form id="myForm">
  <input type="text" id="name" placeholder="Your name" />
  <button type="submit">Submit</button>
</form>
<p id="output"></p>

<script>
  const form = document.getElementById("myForm");
  const output = document.getElementById("output");

  form.addEventListener("submit", function (e) {
    e.preventDefault(); // ⛔ Stop page reload

    const name = document.getElementById("name").value;
    output.textContent = `Hello, ${name}!`;
  });
</script>
```

> 🧠 `e.preventDefault()` is **the most important line** in form handling. Without it, the page refreshes and your JS work is lost.

### Important Form Events

| Event | Fires When |
|-------|-----------|
| `submit` | Form is submitted (click button or press Enter) |
| `input` | User types anything in a field (fires on every keystroke) |
| `change` | User changes value and leaves the field |
| `focus` | User clicks into a field |
| `blur` | User clicks away from a field |
| `reset` | Form is reset |

### Accessing Form Data

```html
<form id="contactForm">
  <input type="text" name="fullName" />
  <input type="email" name="email" />
  <select name="subject">
    <option value="general">General</option>
    <option value="support">Support</option>
    <option value="feedback">Feedback</option>
  </select>
  <textarea name="message"></textarea>
  <button type="submit">Send</button>
</form>

<script>
  const form = document.getElementById("contactForm");

  form.addEventListener("submit", (e) => {
    e.preventDefault();

    // ✅ Method 1: Access individually
    const name = form.fullName.value;
    const email = form.email.value;

    // ✅ Method 2: FormData API (cleaner for many fields)
    const formData = new FormData(form);
    const data = Object.fromEntries(formData);

    console.log(data);
    // { fullName: "Ali", email: "ali@test.com", subject: "general", message: "Hi" }
  });
</script>
```

> 💡 `Object.fromEntries(new FormData(form))` is a clean one-liner to get all form values as an object.

---

## 2 — Client-Side Validation

### Why Validate on the Client?

- **Instant feedback** — no waiting for server response
- **Better UX** — users fix mistakes immediately
- **Reduces server load** — bad data never gets sent

> ⚠️ Client-side validation is for **UX only**. Never trust it for security — always validate on the server too.

### HTML Built-in Validation

HTML gives you free validation with attributes:

```html
<form id="builtInForm">
  <!-- Required field -->
  <input type="text" required placeholder="Name (required)" />

  <!-- Email format -->
  <input type="email" required placeholder="Email" />

  <!-- Minimum length -->
  <input type="password" minlength="8" required placeholder="Password (8+ chars)" />

  <!-- Number range -->
  <input type="number" min="1" max="100" placeholder="Age (1-100)" />

  <!-- Pattern (regex) -->
  <input type="text" pattern="[0-9]{5}" placeholder="ZIP Code (5 digits)" />

  <button type="submit">Submit</button>
</form>
```

> The browser shows its own error messages automatically. But they're ugly and inconsistent across browsers.

### Custom JavaScript Validation

For full control, write your own:

```html
<form id="signupForm" novalidate>
  <!-- novalidate disables browser's built-in messages -->
  <div class="field">
    <label for="username">Username</label>
    <input type="text" id="username" />
    <span class="error" id="usernameError"></span>
  </div>

  <div class="field">
    <label for="email">Email</label>
    <input type="email" id="email" />
    <span class="error" id="emailError"></span>
  </div>

  <div class="field">
    <label for="password">Password</label>
    <input type="password" id="password" />
    <span class="error" id="passwordError"></span>
  </div>

  <button type="submit">Sign Up</button>
</form>

<script>
  const form = document.getElementById("signupForm");

  form.addEventListener("submit", (e) => {
    e.preventDefault();

    // Clear previous errors
    document.querySelectorAll(".error").forEach((el) => (el.textContent = ""));

    const username = document.getElementById("username").value.trim();
    const email = document.getElementById("email").value.trim();
    const password = document.getElementById("password").value;

    let isValid = true;

    // Username validation
    if (username === "") {
      document.getElementById("usernameError").textContent = "Username is required.";
      isValid = false;
    } else if (username.length < 3) {
      document.getElementById("usernameError").textContent =
        "Username must be at least 3 characters.";
      isValid = false;
    }

    // Email validation
    const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (email === "") {
      document.getElementById("emailError").textContent = "Email is required.";
      isValid = false;
    } else if (!emailPattern.test(email)) {
      document.getElementById("emailError").textContent = "Enter a valid email.";
      isValid = false;
    }

    // Password validation
    if (password === "") {
      document.getElementById("passwordError").textContent = "Password is required.";
      isValid = false;
    } else if (password.length < 8) {
      document.getElementById("passwordError").textContent =
        "Password must be at least 8 characters.";
      isValid = false;
    }

    // If all valid
    if (isValid) {
      console.log("Form submitted!", { username, email, password });
      alert("Account created successfully! ✅");
      form.reset();
    }
  });
</script>
```

### Validation Helper Function (Reusable)

```javascript
function validateField(value, rules) {
  if (rules.required && value.trim() === "") {
    return `${rules.name} is required.`;
  }
  if (rules.minLength && value.length < rules.minLength) {
    return `${rules.name} must be at least ${rules.minLength} characters.`;
  }
  if (rules.pattern && !rules.pattern.test(value)) {
    return `${rules.name} format is invalid.`;
  }
  return ""; // No error
}

// Usage
const error = validateField("ab", {
  name: "Username",
  required: true,
  minLength: 3,
});
console.log(error); // "Username must be at least 3 characters."
```

---

## 3 — Error Handling & Messages

### Showing Errors Properly

Bad error UX vs Good error UX:

```
❌ BAD:  alert("Error!")           → Annoying, blocks everything
❌ BAD:  console.log("Error")      → User never sees it
✅ GOOD: Show error next to the field, in red, with clear message
```

### Styling Errors

```css
.field {
  margin-bottom: 15px;
}
.field label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}
.field input {
  width: 100%;
  padding: 10px;
  border: 2px solid #ddd;
  border-radius: 6px;
  font-size: 14px;
  outline: none;
  transition: border-color 0.3s;
}
.field input:focus {
  border-color: #4a90d9;
}

/* Error state */
.field input.invalid {
  border-color: #e74c3c;
  background: #ffeaea;
}
.field input.valid {
  border-color: #27ae60;
}
.error {
  color: #e74c3c;
  font-size: 13px;
  margin-top: 4px;
  display: block;
  min-height: 18px; /* Prevents layout jump */
}

/* Success message */
.success-message {
  background: #d4edda;
  color: #155724;
  padding: 15px;
  border-radius: 8px;
  text-align: center;
  display: none;
}
```

### Real-Time Validation (On Blur)

Instead of waiting for submit, validate when the user **leaves a field**:

```javascript
const usernameInput = document.getElementById("username");
const usernameError = document.getElementById("usernameError");

usernameInput.addEventListener("blur", () => {
  const value = usernameInput.value.trim();

  if (value === "") {
    usernameInput.classList.add("invalid");
    usernameInput.classList.remove("valid");
    usernameError.textContent = "Username is required.";
  } else if (value.length < 3) {
    usernameInput.classList.add("invalid");
    usernameInput.classList.remove("valid");
    usernameError.textContent = "Must be at least 3 characters.";
  } else {
    usernameInput.classList.remove("invalid");
    usernameInput.classList.add("valid");
    usernameError.textContent = "";
  }
});

// Clear error when user starts typing again
usernameInput.addEventListener("input", () => {
  usernameInput.classList.remove("invalid");
  usernameError.textContent = "";
});
```

### Password Strength Indicator

```html
<div class="field">
  <label for="pwd">Password</label>
  <input type="password" id="pwd" />
  <div id="strengthBar" style="height:4px; margin-top:5px; border-radius:2px; transition: all 0.3s;"></div>
  <span class="error" id="strengthText"></span>
</div>

<script>
  const pwdInput = document.getElementById("pwd");
  const bar = document.getElementById("strengthBar");
  const strengthText = document.getElementById("strengthText");

  pwdInput.addEventListener("input", () => {
    const val = pwdInput.value;
    let strength = 0;

    if (val.length >= 8) strength++;
    if (/[A-Z]/.test(val)) strength++;
    if (/[0-9]/.test(val)) strength++;
    if (/[^A-Za-z0-9]/.test(val)) strength++;

    const levels = [
      { color: "#e74c3c", width: "25%", text: "Weak" },
      { color: "#e67e22", width: "50%", text: "Fair" },
      { color: "#f1c40f", width: "75%", text: "Good" },
      { color: "#27ae60", width: "100%", text: "Strong" },
    ];

    if (val.length === 0) {
      bar.style.width = "0%";
      strengthText.textContent = "";
    } else {
      const level = levels[strength - 1] || levels[0];
      bar.style.width = level.width;
      bar.style.background = level.color;
      strengthText.textContent = level.text;
      strengthText.style.color = level.color;
    }
  });
</script>
```

---

## 4 — Accessibility Basics (ARIA Intro)

### Why Accessibility Matters

Accessibility (a11y) means your website works for **everyone**, including people who:
- Use **screen readers** (visually impaired)
- Navigate with **keyboard only**
- Have **motor disabilities**

> 🌍 ~15% of the world's population has some form of disability. Accessible websites are also **legally required** in many countries.

### Simple Rules That Make a Big Difference

**Rule 1: Always use `<label>` with inputs**

```html
<!-- ❌ BAD — Screen reader doesn't know what this input is for -->
<input type="text" placeholder="Name" />

<!-- ✅ GOOD — Label is linked to input -->
<label for="name">Name</label>
<input type="text" id="name" />
```

**Rule 2: Use semantic HTML**

```html
<!-- ❌ BAD -->
<div class="button" onclick="submit()">Submit</div>

<!-- ✅ GOOD — Keyboard accessible, screen reader friendly -->
<button type="submit">Submit</button>
```

**Rule 3: Error messages should be announced**

```html
<!-- aria-live tells screen readers to announce changes -->
<span class="error" id="emailError" role="alert" aria-live="polite"></span>
```

When JavaScript updates this element's text, the screen reader will **read it out loud**.

**Rule 4: Use `aria-invalid` on error fields**

```javascript
// When field has error
input.setAttribute("aria-invalid", "true");
input.setAttribute("aria-describedby", "emailError");

// When error is fixed
input.setAttribute("aria-invalid", "false");
```

**Rule 5: Keyboard navigation must work**

```javascript
// Make sure custom elements are focusable
// All <button> and <input> are focusable by default
// If using <div> as button (don't!), add:
// tabindex="0" and keypress handler
```

### Accessible Form Example

```html
<form id="accessibleForm" novalidate>
  <div class="field">
    <label for="email">Email Address</label>
    <input
      type="email"
      id="email"
      aria-required="true"
      aria-describedby="emailError"
    />
    <span class="error" id="emailError" role="alert" aria-live="polite"></span>
  </div>
  <button type="submit">Submit</button>
</form>

<script>
  const form = document.getElementById("accessibleForm");
  const emailInput = document.getElementById("email");
  const emailError = document.getElementById("emailError");

  form.addEventListener("submit", (e) => {
    e.preventDefault();
    const email = emailInput.value.trim();

    if (email === "" || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)) {
      emailInput.setAttribute("aria-invalid", "true");
      emailError.textContent = "Please enter a valid email address.";
      emailInput.focus(); // Move focus to the error field
    } else {
      emailInput.setAttribute("aria-invalid", "false");
      emailError.textContent = "";
      alert("Submitted!");
    }
  });
</script>
```

---

## 5 — UX Patterns for Forms

### Pattern 1: Disable Submit Until Valid

```html
<form id="form1">
  <input type="text" id="nameField" placeholder="Name" />
  <input type="email" id="emailField" placeholder="Email" />
  <button type="submit" id="submitBtn" disabled>Submit</button>
</form>

<style>
  button:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
</style>

<script>
  const nameField = document.getElementById("nameField");
  const emailField = document.getElementById("emailField");
  const submitBtn = document.getElementById("submitBtn");

  function checkForm() {
    const nameOk = nameField.value.trim().length >= 2;
    const emailOk = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(emailField.value);
    submitBtn.disabled = !(nameOk && emailOk);
  }

  nameField.addEventListener("input", checkForm);
  emailField.addEventListener("input", checkForm);
</script>
```

### Pattern 2: Show/Hide Password

```html
<div class="field" style="position: relative;">
  <label for="pwd">Password</label>
  <input type="password" id="pwd" />
  <button type="button" id="togglePwd"
    style="position:absolute; right:10px; top:35px; background:none; border:none; cursor:pointer; font-size:16px;">
    👁️
  </button>
</div>

<script>
  const pwdField = document.getElementById("pwd");
  const toggleBtn = document.getElementById("togglePwd");

  toggleBtn.addEventListener("click", () => {
    if (pwdField.type === "password") {
      pwdField.type = "text";
      toggleBtn.textContent = "🙈";
    } else {
      pwdField.type = "password";
      toggleBtn.textContent = "👁️";
    }
  });
</script>
```

### Pattern 3: Character Counter

```html
<div class="field">
  <label for="bio">Bio</label>
  <textarea id="bio" maxlength="150" rows="3"></textarea>
  <small id="charCount">0 / 150</small>
</div>

<script>
  const bio = document.getElementById("bio");
  const charCount = document.getElementById("charCount");

  bio.addEventListener("input", () => {
    const len = bio.value.length;
    charCount.textContent = `${len} / 150`;
    charCount.style.color = len > 140 ? "#e74c3c" : "#888";
  });
</script>
```

### Pattern 4: Submit Button Loading State

```javascript
const submitBtn = document.getElementById("submitBtn");

form.addEventListener("submit", async (e) => {
  e.preventDefault();

  // Show loading
  submitBtn.disabled = true;
  submitBtn.textContent = "Submitting...";

  // Simulate API call
  await new Promise((resolve) => setTimeout(resolve, 2000));

  // Done
  submitBtn.disabled = false;
  submitBtn.textContent = "Submit";
  alert("Done!");
});
```

---

## 6 — Mini-Project: Signup Form with Validations

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Signup Form</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      font-family: 'Segoe UI', sans-serif;
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 20px;
    }
    .form-container {
      background: white;
      padding: 40px;
      border-radius: 16px;
      box-shadow: 0 10px 40px rgba(0,0,0,0.2);
      width: 100%;
      max-width: 450px;
    }
    h1 {
      text-align: center;
      margin-bottom: 25px;
      color: #333;
    }
    .field {
      margin-bottom: 18px;
      position: relative;
    }
    .field label {
      display: block;
      margin-bottom: 5px;
      font-weight: 600;
      color: #444;
      font-size: 14px;
    }
    .field input, .field select {
      width: 100%;
      padding: 12px 15px;
      border: 2px solid #e0e0e0;
      border-radius: 8px;
      font-size: 15px;
      outline: none;
      transition: border-color 0.3s, background 0.3s;
    }
    .field input:focus, .field select:focus {
      border-color: #667eea;
    }
    .field input.valid { border-color: #27ae60; }
    .field input.invalid { border-color: #e74c3c; background: #fff5f5; }
    .error {
      color: #e74c3c;
      font-size: 12px;
      margin-top: 4px;
      display: block;
      min-height: 16px;
    }
    .password-toggle {
      position: absolute;
      right: 12px;
      top: 38px;
      background: none;
      border: none;
      cursor: pointer;
      font-size: 16px;
    }
    .strength-bar {
      height: 4px;
      border-radius: 2px;
      margin-top: 6px;
      transition: all 0.3s;
    }
    .strength-text {
      font-size: 12px;
      margin-top: 2px;
    }
    .terms {
      display: flex;
      align-items: center;
      gap: 8px;
      margin: 15px 0;
      font-size: 14px;
      color: #555;
    }
    .terms input { width: auto; }
    .terms a { color: #667eea; }
    .submit-btn {
      width: 100%;
      padding: 14px;
      background: #667eea;
      color: white;
      border: none;
      border-radius: 8px;
      font-size: 16px;
      font-weight: 600;
      cursor: pointer;
      transition: all 0.3s;
    }
    .submit-btn:hover:not(:disabled) { background: #5a6fd6; }
    .submit-btn:disabled { opacity: 0.5; cursor: not-allowed; }
    .success-box {
      display: none;
      background: #d4edda;
      color: #155724;
      padding: 20px;
      border-radius: 10px;
      text-align: center;
    }
    .success-box h2 { margin-bottom: 10px; }
    .char-count {
      text-align: right;
      font-size: 12px;
      color: #999;
    }
  </style>
</head>
<body>

  <div class="form-container">
    <!-- The Form -->
    <form id="signupForm" novalidate>
      <h1>📋 Create Account</h1>

      <!-- Full Name -->
      <div class="field">
        <label for="fullName">Full Name</label>
        <input type="text" id="fullName" placeholder="John Doe"
          aria-required="true" aria-describedby="fullNameError" />
        <span class="error" id="fullNameError" role="alert" aria-live="polite"></span>
      </div>

      <!-- Email -->
      <div class="field">
        <label for="email">Email Address</label>
        <input type="email" id="email" placeholder="john@example.com"
          aria-required="true" aria-describedby="emailError" />
        <span class="error" id="emailError" role="alert" aria-live="polite"></span>
      </div>

      <!-- Password -->
      <div class="field">
        <label for="password">Password</label>
        <input type="password" id="password" placeholder="Minimum 8 characters"
          aria-required="true" aria-describedby="passwordError" />
        <button type="button" class="password-toggle" id="togglePassword">👁️</button>
        <div class="strength-bar" id="strengthBar"></div>
        <span class="strength-text" id="strengthText"></span>
        <span class="error" id="passwordError" role="alert" aria-live="polite"></span>
      </div>

      <!-- Confirm Password -->
      <div class="field">
        <label for="confirmPassword">Confirm Password</label>
        <input type="password" id="confirmPassword" placeholder="Re-enter password"
          aria-required="true" aria-describedby="confirmError" />
        <span class="error" id="confirmError" role="alert" aria-live="polite"></span>
      </div>

      <!-- Age -->
      <div class="field">
        <label for="age">Age</label>
        <input type="number" id="age" placeholder="18" min="13" max="120"
          aria-describedby="ageError" />
        <span class="error" id="ageError" role="alert" aria-live="polite"></span>
      </div>

      <!-- Terms -->
      <div class="terms">
        <input type="checkbox" id="terms" />
        <label for="terms">I agree to the <a href="#">Terms & Conditions</a></label>
      </div>
      <span class="error" id="termsError" role="alert" aria-live="polite"></span>

      <!-- Submit -->
      <button type="submit" class="submit-btn" id="submitBtn">Create Account</button>
    </form>

    <!-- Success Message (hidden by default) -->
    <div class="success-box" id="successBox">
      <h2>🎉 Account Created!</h2>
      <p>Welcome, <span id="welcomeName"></span>!</p>
      <p>Check your email for verification.</p>
    </div>
  </div>

  <script>
    // ========== DOM Elements ==========
    const form = document.getElementById("signupForm");
    const submitBtn = document.getElementById("submitBtn");
    const successBox = document.getElementById("successBox");

    const fields = {
      fullName: {
        input: document.getElementById("fullName"),
        error: document.getElementById("fullNameError"),
      },
      email: {
        input: document.getElementById("email"),
        error: document.getElementById("emailError"),
      },
      password: {
        input: document.getElementById("password"),
        error: document.getElementById("passwordError"),
      },
      confirmPassword: {
        input: document.getElementById("confirmPassword"),
        error: document.getElementById("confirmError"),
      },
      age: {
        input: document.getElementById("age"),
        error: document.getElementById("ageError"),
      },
    };

    const termsCheckbox = document.getElementById("terms");
    const termsError = document.getElementById("termsError");

    // ========== Validation Functions ==========
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;

    function validateFullName() {
      const val = fields.fullName.input.value.trim();
      if (val === "") return setError("fullName", "Full name is required.");
      if (val.length < 2) return setError("fullName", "Must be at least 2 characters.");
      return setValid("fullName");
    }

    function validateEmail() {
      const val = fields.email.input.value.trim();
      if (val === "") return setError("email", "Email is required.");
      if (!emailRegex.test(val)) return setError("email", "Enter a valid email.");
      return setValid("email");
    }

    function validatePassword() {
      const val = fields.password.input.value;
      if (val === "") return setError("password", "Password is required.");
      if (val.length < 8) return setError("password", "Must be at least 8 characters.");
      updateStrengthBar(val);
      return setValid("password");
    }

    function validateConfirmPassword() {
      const val = fields.confirmPassword.input.value;
      const pwd = fields.password.input.value;
      if (val === "") return setError("confirmPassword", "Please confirm your password.");
      if (val !== pwd) return setError("confirmPassword", "Passwords do not match.");
      return setValid("confirmPassword");
    }

    function validateAge() {
      const val = fields.age.input.value;
      if (val === "") return setError("age", "Age is required.");
      if (val < 13 || val > 120) return setError("age", "Age must be between 13 and 120.");
      return setValid("age");
    }

    // ========== Helper Functions ==========
    function setError(fieldName, message) {
      fields[fieldName].input.classList.add("invalid");
      fields[fieldName].input.classList.remove("valid");
      fields[fieldName].input.setAttribute("aria-invalid", "true");
      fields[fieldName].error.textContent = message;
      return false;
    }

    function setValid(fieldName) {
      fields[fieldName].input.classList.remove("invalid");
      fields[fieldName].input.classList.add("valid");
      fields[fieldName].input.setAttribute("aria-invalid", "false");
      fields[fieldName].error.textContent = "";
      return true;
    }

    // ========== Password Strength Bar ==========
    const strengthBar = document.getElementById("strengthBar");
    const strengthText = document.getElementById("strengthText");

    function updateStrengthBar(password) {
      let score = 0;
      if (password.length >= 8) score++;
      if (/[A-Z]/.test(password)) score++;
      if (/[0-9]/.test(password)) score++;
      if (/[^A-Za-z0-9]/.test(password)) score++;

      const levels = [
        { color: "#e74c3c", width: "25%", label: "Weak" },
        { color: "#e67e22", width: "50%", label: "Fair" },
        { color: "#f1c40f", width: "75%", label: "Good" },
        { color: "#27ae60", width: "100%", label: "Strong 💪" },
      ];

      const level = levels[score - 1] || levels[0];
      strengthBar.style.width = level.width;
      strengthBar.style.background = level.color;
      strengthText.textContent = level.label;
      strengthText.style.color = level.color;
    }

    // ========== Password Toggle ==========
    const toggleBtn = document.getElementById("togglePassword");
    toggleBtn.addEventListener("click", () => {
      const pwd = fields.password.input;
      pwd.type = pwd.type === "password" ? "text" : "password";
      toggleBtn.textContent = pwd.type === "password" ? "👁️" : "🙈";
    });

    // ========== Real-Time Validation (on blur) ==========
    fields.fullName.input.addEventListener("blur", validateFullName);
    fields.email.input.addEventListener("blur", validateEmail);
    fields.password.input.addEventListener("blur", validatePassword);
    fields.confirmPassword.input.addEventListener("blur", validateConfirmPassword);
    fields.age.input.addEventListener("blur", validateAge);

    // Password strength on input
    fields.password.input.addEventListener("input", () => {
      const val = fields.password.input.value;
      if (val.length > 0) updateStrengthBar(val);
      else {
        strengthBar.style.width = "0";
        strengthText.textContent = "";
      }
    });

    // Clear errors on input
    Object.values(fields).forEach(({ input, error }) => {
      input.addEventListener("input", () => {
        input.classList.remove("invalid");
        error.textContent = "";
      });
    });

    // ========== Form Submission ==========
    form.addEventListener("submit", (e) => {
      e.preventDefault();

      const results = [
        validateFullName(),
        validateEmail(),
        validatePassword(),
        validateConfirmPassword(),
        validateAge(),
      ];

      // Terms check
      if (!termsCheckbox.checked) {
        termsError.textContent = "You must accept the terms.";
        results.push(false);
      } else {
        termsError.textContent = "";
      }

      const allValid = results.every((r) => r === true);

      if (allValid) {
        // Simulate submission
        submitBtn.disabled = true;
        submitBtn.textContent = "Creating Account...";

        setTimeout(() => {
          form.style.display = "none";
          successBox.style.display = "block";
          document.getElementById("welcomeName").textContent =
            fields.fullName.input.value.trim();
        }, 1500);
      } else {
        // Focus first invalid field
        const firstInvalid = document.querySelector(".invalid");
        if (firstInvalid) firstInvalid.focus();
      }
    });
  </script>

</body>
</html>
```

### What This Project Covers

```
✅ Form events         → submit with preventDefault
✅ Client validation    → Custom JS validation for every field
✅ Error handling       → Inline errors, red borders, clear messages
✅ Accessibility        → Labels, aria-invalid, aria-live, role="alert"
✅ UX Patterns          → Password toggle, strength bar, disabled button,
                          loading state, focus first error, success screen
```

---

## 📌 Chapter Summary

| Concept | What You Learned |
|---------|-----------------|
| Form Events | `submit`, `input`, `change`, `blur`, `focus` |
| preventDefault() | Stop page reload on form submit |
| Client Validation | Check fields with JS before sending data |
| Error Display | Inline errors, red borders, `.invalid` class |
| Accessibility | Labels, `aria-invalid`, `aria-live`, `role="alert"` |
| UX Patterns | Password toggle, strength bar, disabled button, loading state |
| FormData API | `new FormData(form)` + `Object.fromEntries()` |

---