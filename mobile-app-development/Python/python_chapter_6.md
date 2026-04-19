# Chapter 6: Advanced Python Concepts & Best Practices

## 🎯 Learning Objectives
By the end of this chapter you will be able to:
1. Understand iterators and generators for memory-efficient processing
2. Write and use decorators to modify function behavior
3. Utilize comprehensions for cleaner syntax
4. Implement context managers for resource management
5. Set up virtual environments for dependency isolation
6. Package basic Python projects
7. Apply PEP 8 standards and best practices
8. Profile code for performance optimization
9. Build a mini-project integrating all learned skills

---

> 💡 **Why This Matters**: These concepts distinguish beginners from professionals. They enable you to write scalable, maintainable, and efficient code. Understanding how Python works under the hood (generators, decorators) gives you superpowers for solving complex problems elegantly.

---

## 6.1 Iterators & Generators

### What is an Iterator?
An iterator is an object containing a finite sequence of data that can be traversed using `for` loops. It implements two methods:
- `__iter__()`: Returns the iterator object itself.
- `__next__()`: Returns the next item from the sequence.

```python
my_list = [1, 2, 3]
iterator = iter(my_list)

print(next(iterator))  # 1
print(next(iterator))  # 2
print(next(iterator))  # 3
# print(next(iterator))  # Raises StopIteration
```

### Generators: Creating Iterators Easily
Generators are functions that return an iterator using `yield` instead of `return`. They pause execution and resume later, saving memory for large datasets.

#### Generator Function
```python
def count_up_to(n):
    count = 1
    while count <= n:
        yield count
        count += 1

for num in count_up_to(5):
    print(num)
# Output: 1, 2, 3, 4, 5
```

#### Benefits of Generators
1.  **Memory Efficiency:** Does not store all values in memory (lazy evaluation).
2.  **Infinite Sequences:** Can generate infinite data streams.

```python
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

fib = fibonacci()
print(next(fib))  # 0
print(next(fib))  # 1
print(next(fib))  # 1
```

---

## 6.2 Decorators

A decorator is a higher-order function that takes another function and extends its behavior without explicitly modifying it. It uses the `@syntax`.

### Basic Syntax
```python
def my_decorator(func):
    def wrapper():
        print("Something before the function runs.")
        func()
        print("Something after the function runs.")
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")

say_hello()
```
**Output:**
```
Something before the function runs.
Hello!
Something after the function runs.
```

### Passing Arguments to Decorated Functions
You need to preserve function arguments and return values using `*args` and `**kwargs`.

```python
def logging_decorator(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__} with args={args}, kwargs={kwargs}")
        result = func(*args, **kwargs)
        return result
    return wrapper

@logging_decorator
def greet(name, greeting="Hi"):
    return f"{greeting}, {name}!"

print(greet("Alice"))
# Calling greet with args=(), kwargs={'name': 'Alice'}
# Result: Hi, Alice!
```

### Built-in Decorators
| Decorator | Purpose | Example |
|-----------|---------|---------|
| `@staticmethod` | Method belongs to class, not instance | `Math.sqrt()` |
| `@classmethod` | Method receives class as first arg | `create_from_dict()` |
| `@property` | Turns method into attribute | `@area.setter` |

---

## 6.3 Comprehensions

Comprehensions provide a concise way to create lists, dictionaries, and sets.

### List Comprehension
Instead of a loop to build a list:
```python
# Standard loop
squares = []
for x in range(5):
    squares.append(x**2)

# Comprehension (Same result)
squares = [x**2 for x in range(5)]
# Output: [0, 1, 4, 9, 16]
```

### Filter Condition
Add `if` condition inside comprehension:
```python
# Even numbers only
evens = [x for x in range(10) if x % 2 == 0]
# Output: [0, 2, 4, 6, 8]
```

### Dictionary Comprehension
Creates key-value pairs:
```python
squares_dict = {x: x**2 for x in range(5)}
# Output: {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

### Set Comprehension
Creates unique values:
```python
unique_nums = {x for x in [1, 2, 2, 3, 3]}
# Output: {1, 2, 3}
```

---

## 6.4 Context Managers

Context managers allow you to allocate and release resources exactly when needed (like file handling).

### Using `with` Statement
Standard practice for files, locks, connections.

```python
# File example (automatic close)
with open("data.txt", "r") as file:
    content = file.read()
# File is automatically closed here, even if error occurs
```

### Creating Custom Context Managers
Using `class`:
```python
class Timer:
    def __enter__(self):
        import time
        self.start = time.time()
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        import time
        duration = time.time() - self.start
        print(f"Elapsed: {duration:.2f} seconds")
    
    def __repr__(self):
        return "Timer()"

# Usage
with Timer():
    import time
    time.sleep(2)
# Output: Elapsed: ~2.00 seconds
```

### Using `contextlib` Module
For function-based context managers:
```python
from contextlib import contextmanager

@contextmanager
def open_file(name):
    f = open(name, 'w')
    try:
        yield f
    finally:
        f.close()

with open_file('test.txt') as f:
    f.write("Hello")
# File automatically closed
```

---

## 6.5 Virtual Environments

Python projects often require different package versions. **Virtual environments** isolate dependencies so they don't conflict.

### Creating a Virtual Environment
1.  **Project Folder:** Create your project folder.
2.  **Activate venv:**
    ```bash
    python -m venv venv  # Windows/macOS/Linux
    ```
3.  **Activate:**
    *   **Windows:** `venv\Scripts\activate.bat`
    *   **macOS/Linux:** `source venv/bin/activate`
4.  **Check:** Terminal prefix should show `(venv)`.

### Installing Packages
With environment active:
```bash
pip install requests pandas
```
Packages are installed *only* in this environment.

### Deactivating
```bash
deactivate
```
The `(venv)` prefix disappears, returning to global Python.

### `.gitignore`
Always ignore the `venv` folder in Git repositories:
```
venv/
.idea/
*.pyc
```

---

## 6.6 Python Packaging Basics

To share your Python code as a library or installable package.

### `pyproject.toml` (Modern Standard)
Declares dependencies and build system.
```toml
[build-system]
requires = ["setuptools>=45", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "my-awesome-package"
version = "0.1.0"
dependencies = ["requests>=2.25.0"]
description = "My first package"
```

### Setup Script (Legacy Alternative)
Traditional `setup.py`:
```python
from setuptools import setup, find_packages

setup(
    name="my_package",
    version="0.1",
    packages=find_packages(),
    install_requires=["requests"],
)
```

### Installing Locally
```bash
pip install .
```

---

## 6.7 Code Style & Best Practices

Consistency makes code readable and collaborative.

### PEP 8 Guidelines
1.  **Indentation:** 4 spaces per level.
2.  **Line Length:** Max 79 characters (88 allowed with newer tools).
3.  **Imports:** Separate sections for standard, third-party, local.
4.  **Names:**
    *   Variables/Functions: `snake_case`
    *   Classes: `PascalCase`
    *   Constants: `UPPER_CASE`
5.  **Whitespace:** Spaces around operators (`x = y + z`), not parentheses (`func( )`).

### Tools for Formatting
-   **Black:** Auto-formatter.
    ```bash
    pip install black
    black file.py
    ```
-   **Flake8 / PyLint:** Linting tools for finding errors and warnings.
    ```bash
    pip install flake8
    flake8 file.py
    ```
-   **Isort:** Organizes imports alphabetically.
    ```bash
    pip install isort
    isort file.py
    ```

---

## 6.8 Performance Basics

Writing fast code requires understanding bottlenecks.

### Big O Notation
Describes algorithm efficiency.
-   `O(1)`: Constant time (dictionary lookup).
-   `O(log n)`: Logarithmic time (binary search).
-   `O(n)`: Linear time (single loop).
-   `O(n^2)`: Quadratic time (nested loops) -> Slow.

### Profiling
Find slow parts of code using `cProfile`.
```bash
python -m cProfile script.py
```

### Simple Optimization Tips
1.  **Use built-in functions:** `sum(list)` is faster than a manual loop.
2.  **List Comprehensions:** Faster than appending in a loop.
3.  **Avoid Global Lookups:** Local variables are faster to access.
4.  **Generators over Lists:** For large data, use generators to save memory.

```python
import timeit

def test_sum():
    nums = list(range(1000000))
    return sum(nums)

def test_loop():
    total = 0
    for i in range(1000000):
        total += i
    return total

print(timeit.timeit(test_sum, number=100))  # Faster
print(timeit.timeit(test_loop, number=100)) # Slower
```

---

## 6.9 Mini Project: Expense Tracker CLI

Let's combine everything we've learned into a functional CLI (Command Line Interface) application.

### Project Structure
```
expense_tracker/
├── main.py
├── transactions.py
├── utils.py
└── requirements.txt
```

### 1. Utility Functions (utils.py)
```python
import json

def load_data(filename='transactions.json'):
    try:
        with open(filename, 'r') as file:
            return json.load(file)
    except FileNotFoundError:
        return []

def save_data(data, filename='transactions.json'):
    with open(filename, 'w') as file:
        json.dump(data, file, indent=4)
```

### 2. Transaction Logic (transactions.py)
```python
import datetime

class Transaction:
    def __init__(self, description, amount, category):
        self.id = datetime.datetime.now().strftime("%Y%m%d%H%M%S")
        self.description = description
        self.amount = float(amount)
        self.category = category
        self.date = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")

    def to_dict(self):
        return {
            "id": self.id,
            "description": self.description,
            "amount": self.amount,
            "category": self.category,
            "date": self.date
        }
```

### 3. Main Application (main.py)
```python
import sys
from transactions import Transaction
import utils

def main():
    transactions = utils.load_data()
    
    while True:
        print("\n=== Expense Tracker ===")
        print("1. Add Expense")
        print("2. View Summary")
        print("3. Exit")
        
        choice = input("Choose option: ")
        
        if choice == '1':
            desc = input("Description: ")
            amt = input("Amount: ")
            cat = input("Category: ")
            
            tx = Transaction(desc, amt, cat)
            transactions.append(tx.to_dict())
            utils.save_data(transactions)
            print("Saved!")
            
        elif choice == '2':
            total = sum(t['amount'] for t in transactions)
            print(f"\nTotal Spent: ${total:.2f}")
            print(f"Transaction Count: {len(transactions)}")
            
        elif choice == '3':
            print("Goodbye!")
            break
        
        else:
            print("Invalid option")

if __name__ == "__main__":
    main()
```

### How to Run
1.  Save all files in a folder.
2.  Activate virtual environment: `source venv/bin/activate`.
3.  Run: `python main.py`.

---

## ✅ Chapter 6 Summary

Congratulations! You have reached the end of the Python Core Programming course.

In this chapter, you mastered:
*   **Advanced Features:** Generators, decorators, and comprehensions.
*   **Professional Tools:** Virtual environments and packaging.
*   **Best Practices:** PEP 8, linting, and performance optimization.
*   **Real-World Application:** Built a functional CLI tool.

You are now ready to tackle real-world software development, backend engineering, data automation, or web frameworks.

---

## 🎉 Course Completion

You have successfully completed all 6 chapters of the **Python Core Programming Course**!

You now have the foundation to:
-   Build automation scripts
-   Develop backend APIs
-   Analyze data effectively
-   Write professional, maintainable code

Keep practicing, building projects, and exploring new libraries. Happy coding!