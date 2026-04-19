# Chapter 4: Functions, Modules & File Handling

## 🎯 Learning Objectives
By the end of this chapter you will be able to:
1. Write reusable functions with clear purpose
2. Use parameters, return values, and default arguments
3. Understand variable scope and avoid common pitfalls
4. Organize code using modules and packages
5. Read from and write to files
6. Work with JSON and CSV data formats
7. Handle file errors gracefully

---

> 💡 **Why This Matters**: Functions are the fundamental building blocks of professional code. They prevent repetition, make code testable, and allow you to think at a higher level of abstraction. File handling lets your programs persist data and interact with the real world.

---

## 4.1 What are Functions?

A function is a reusable block of code that performs a specific task. Instead of writing the same code multiple times, you write it once in a function and call it whenever needed.

### Benefits of Functions:
1. **DRY Principle** (Don't Repeat Yourself): Write once, use many times
2. **Organization**: Break complex problems into smaller pieces
3. **Testing**: Easy to test individual functions
4. **Readability**: Good function names make code self-documenting
5. **Maintenance**: Fix bugs in one place

---

## 4.2 Defining and Calling Functions

### Basic Syntax
```python
def function_name():
    # Code to execute
    pass
```

### Simple Example
```python
def greet():
    print("Hello, World!")

# Call the function
greet()  # Output: Hello, World!
```

### Function Naming Rules:
- Use lowercase with underscores (snake_case)
- Start with a letter or underscore
- Be descriptive: `calculate_total()` not `calc()`
- Use verbs: `get_user()`, `send_email()`, `validate_input()`

---

## 4.3 Parameters and Arguments

Parameters let you pass data into functions.

### Positional Parameters
```python
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")  # Hello, Alice!
greet("Bob")    # Hello, Bob!
```

### Multiple Parameters
```python
def add(a, b):
    result = a + b
    print(f"{a} + {b} = {result}")

add(5, 3)  # 5 + 3 = 8
```

> ✅ **Important**: The order matters with positional arguments. `add(5, 3)` is not the same as `add(3, 5)` if the function cares about order.

---

## 4.4 Return Values

Functions can send data back to the caller using `return`.

### Basic Return
```python
def add(a, b):
    return a + b

result = add(5, 3)
print(result)  # 8
```

### Multiple Return Values (using tuples)
```python
def get_user_info():
    name = "Alice"
    age = 25
    city = "New York"
    return name, age, city  # Returns a tuple

# Unpack the return values
name, age, city = get_user_info()
print(name)  # Alice
```

### Early Return
```python
def is_adult(age):
    if age >= 18:
        return True
    return False

# Even better (more Pythonic):
def is_adult(age):
    return age >= 18
```

> ✅ **Rule**: A function without an explicit `return` statement returns `None`.

---

## 4.5 Default Arguments

You can provide default values for parameters.

```python
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Alice")              # Hello, Alice!
greet("Bob", "Hi")          # Hi, Bob!
greet("Charlie", "Hey")     # Hey, Charlie!
```

> ❗ **Critical Rule**: Default parameters must come AFTER non-default parameters.

```python
# WRONG - SyntaxError
def greet(greeting="Hello", name):
    pass

# CORRECT
def greet(name, greeting="Hello"):
    pass
```

---

## 4.6 Keyword Arguments

You can specify arguments by name, which makes calls clearer and order-independent.

```python
def create_user(name, age, city):
    print(f"User: {name}, {age}, {city}")

# Positional
create_user("Alice", 25, "NYC")

# Keyword (any order)
create_user(age=25, city="NYC", name="Alice")

# Mixed (positional first)
create_user("Alice", city="NYC", age=25)
```

---

## 4.7 Lambda Functions

Lambda functions are small, anonymous functions defined in a single line.

### Syntax
```python
lambda parameters: expression
```

### Examples
```python
# Regular function
def double(x):
    return x * 2

# Lambda equivalent
double = lambda x: x * 2

print(double(5))  # 10
```

### Common Use Cases
```python
# Sorting by a specific key
users = [
    {"name": "Alice", "age": 25},
    {"name": "Bob", "age": 30},
    {"name": "Charlie", "age": 20}
]

# Sort by age
users.sort(key=lambda user: user["age"])
print(users)  # Charlie(20), Alice(25), Bob(30)

# Filter even numbers
numbers = [1, 2, 3, 4, 5, 6]
evens = list(filter(lambda x: x % 2 == 0, numbers))
print(evens)  # [2, 4, 6]

# Map: double all numbers
doubled = list(map(lambda x: x * 2, numbers))
print(doubled)  # [2, 4, 6, 8, 10, 12]
```

> ✅ **Best Practice**: Use lambdas for simple operations. For complex logic, use regular functions.

---

## 4.8 Variable Scope

Scope determines where a variable can be accessed.

### Local Scope
Variables created inside a function exist only in that function.

```python
def my_function():
    x = 10  # Local variable
    print(x)

my_function()  # 10
print(x)       # NameError: x is not defined
```

### Global Scope
Variables created outside functions are global.

```python
x = 10  # Global variable

def my_function():
    print(x)  # Can read global

my_function()  # 10
```

### Modifying Global Variables
```python
count = 0

def increment():
    global count  # Declare we're using the global variable
    count += 1

increment()
increment()
print(count)  # 2
```

> ❗ **Warning**: Avoid global variables when possible. They make code harder to test and debug. Pass values as parameters instead.

---

## 4.9 Modules and Packages

Modules are Python files containing functions, classes, and variables. They help organize code.

### Built-in Modules
Python comes with many useful modules.

```python
import math

print(math.pi)           # 3.141592653589793
print(math.sqrt(16))     # 4.0
print(math.ceil(4.3))    # 5
```

### Import Styles
```python
# Import entire module
import math
math.sqrt(16)

# Import specific function
from math import sqrt
sqrt(16)

# Import with alias
import math as m
m.sqrt(16)

# Import everything (NOT RECOMMENDED)
from math import *
sqrt(16)
```

> ✅ **Best Practice**: Use `import module` or `from module import specific_function`. Avoid `import *`.

### Creating Your Own Module

**File: helpers.py**
```python
def greet(name):
    return f"Hello, {name}!"

def add(a, b):
    return a + b

PI = 3.14159
```

**File: main.py**
```python
import helpers

print(helpers.greet("Alice"))  # Hello, Alice!
print(helpers.add(5, 3))       # 8
print(helpers.PI)              # 3.14159
```

### Common Built-in Modules
| Module | Purpose | Example |
|--------|---------|---------|
| `math` | Mathematical functions | `math.sqrt(16)` |
| `random` | Random number generation | `random.randint(1, 10)` |
| `datetime` | Date and time | `datetime.datetime.now()` |
| `os` | Operating system interface | `os.listdir()` |
| `json` | JSON encoding/decoding | `json.loads(data)` |
| `csv` | CSV file handling | `csv.reader(file)` |

---

## 4.10 File Handling

Files let your programs persist data beyond program execution.

### Reading Files

**Basic Read**
```python
# Open and read entire file
file = open("data.txt", "r")
content = file.read()
print(content)
file.close()  # ALWAYS close files
```

**Better Approach: Using `with`**
```python
# Automatically closes file
with open("data.txt", "r") as file:
    content = file.read()
    print(content)
# File is automatically closed here
```

### File Reading Methods
```python
with open("data.txt", "r") as file:
    # Read entire file
    content = file.read()
    
    # Read one line
    line = file.readline()
    
    # Read all lines into a list
    lines = file.readlines()
    
    # Iterate line by line (BEST for large files)
    for line in file:
        print(line.strip())  # strip() removes newline
```

### Writing Files

```python
# Write mode (overwrites existing file)
with open("output.txt", "w") as file:
    file.write("Hello, World!\n")
    file.write("Second line\n")

# Append mode (adds to existing file)
with open("output.txt", "a") as file:
    file.write("Added line\n")
```

### File Modes
| Mode | Description |
|------|-------------|
| `"r"` | Read (default) |
| `"w"` | Write (overwrites) |
| `"a"` | Append (adds to end) |
| `"r+"` | Read and write |
| `"rb"` | Read binary |
| `"wb"` | Write binary |

---

## 4.11 Working with JSON

JSON (JavaScript Object Notation) is the standard format for data exchange.

### Writing JSON
```python
import json

# Python dictionary
data = {
    "name": "Alice",
    "age": 25,
    "skills": ["Python", "JavaScript", "SQL"]
}

# Write to file
with open("data.json", "w") as file:
    json.dump(data, file, indent=4)  # indent makes it readable
```

**Result (data.json):**
```json
{
    "name": "Alice",
    "age": 25,
    "skills": [
        "Python",
        "JavaScript",
        "SQL"
    ]
}
```

### Reading JSON
```python
import json

# Read from file
with open("data.json", "r") as file:
    data = json.load(file)
    print(data["name"])      # Alice
    print(data["skills"][0]) # Python
```

### JSON String Conversion
```python
import json

# Dictionary to JSON string
data = {"name": "Alice", "age": 25}
json_string = json.dumps(data)
print(json_string)  # {"name": "Alice", "age": 25}

# JSON string to dictionary
json_string = '{"name": "Bob", "age": 30}'
data = json.loads(json_string)
print(data["name"])  # Bob
```

---

## 4.12 Working with CSV

CSV (Comma-Separated Values) is common for tabular data.

### Reading CSV
```python
import csv

with open("data.csv", "r") as file:
    reader = csv.reader(file)
    
    # Skip header row
    next(reader)
    
    for row in reader:
        print(row)  # Each row is a list
```

### Reading CSV as Dictionary
```python
import csv

with open("users.csv", "r") as file:
    reader = csv.DictReader(file)
    
    for row in reader:
        print(row["name"], row["age"])
```

**Sample CSV (users.csv):**
```
name,age,city
Alice,25,NYC
Bob,30,LA
Charlie,35,Chicago
```

### Writing CSV
```python
import csv

data = [
    ["name", "age", "city"],
    ["Alice", 25, "NYC"],
    ["Bob", 30, "LA"]
]

with open("output.csv", "w", newline="") as file:
    writer = csv.writer(file)
    writer.writerows(data)
```

---

## 4.13 Error Handling with Files

Always handle file errors to prevent crashes.

```python
try:
    with open("data.txt", "r") as file:
        content = file.read()
        print(content)
except FileNotFoundError:
    print("File does not exist")
except PermissionError:
    print("No permission to read file")
except Exception as e:
    print(f"An error occurred: {e}")
```

---

## 4.14 Best Practices

### Function Design
1. **Single Responsibility**: Each function should do ONE thing
2. **Meaningful Names**: `calculate_tax()` not `calc()`
3. **Keep it Short**: If a function is over 20 lines, consider breaking it up
4. **Document**: Use docstrings

```python
def calculate_tax(amount, rate):
    """
    Calculate tax on a given amount.
    
    Parameters:
        amount (float): The base amount
        rate (float): Tax rate as decimal (0.1 for 10%)
    
    Returns:
        float: The tax amount
    """
    return amount * rate
```

### File Handling
1. **Always use `with`**: Ensures files are closed
2. **Handle errors**: Don't assume files exist
3. **Use appropriate modes**: Don't use `"w"` if you mean `"a"`
4. **Close resources**: Even if using `with`, be conscious of resource usage

---

## ✅ Chapter 4 Summary

You've now mastered:

**Functions**:
- Defining and calling functions
- Parameters and return values
- Default and keyword arguments
- Lambda functions
- Variable scope

**Modules**:
- Importing built-in modules
- Creating custom modules
- Import styles and best practices

**File Handling**:
- Reading and writing text files
- Working with JSON data
- Processing CSV files
- Error handling

You can now write organized, reusable code and persist data to files. These are essential skills for building real applications.

---