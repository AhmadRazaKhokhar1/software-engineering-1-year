# Chapter 3: Data Structures & Collections

## 🎯 Learning Objectives
By the end of this chapter you will be able to:
1. Choose the right data structure for any task
2. Master lists, tuples, sets, and dictionaries
3. Access and modify data efficiently
4. Use built-in methods to manipulate collections
5. Iterate through data structures
6. Solve real-world data organization problems

---

> 💡 **Why This Matters**: Up until now, you've been working with single values. Real programs work with collections of data: user lists, product inventories, configuration settings. This chapter teaches you how to organize and manipulate data like a professional.

---

## 3.1 What are Data Structures?

Data structures are containers that hold multiple values. Python has four main built-in data structures:

| Structure | Ordered | Mutable | Duplicates | Use Case |
|-----------|---------|---------|------------|----------|
| **List** | ✅ Yes | ✅ Yes | ✅ Yes | General-purpose collection |
| **Tuple** | ✅ Yes | ❌ No | ✅ Yes | Fixed data that shouldn't change |
| **Set** | ❌ No | ✅ Yes | ❌ No | Unique values, fast lookups |
| **Dictionary** | ✅ Yes* | ✅ Yes | ❌ No (keys) | Key-value pairs |

*Dictionaries maintain insertion order as of Python 3.7+

---

## 3.2 Lists: The Workhorse of Python

Lists are ordered, mutable collections. They are the most commonly used data structure in Python.

### Creating Lists
```python
# Empty list
empty_list = []

# List with values
fruits = ["apple", "banana", "cherry"]

# Mixed types (not recommended but possible)
mixed = [1, "hello", True, 3.14]

# List from range
numbers = list(range(5))  # [0, 1, 2, 3, 4]
```

### Accessing Elements (Indexing)
```python
fruits = ["apple", "banana", "cherry", "date"]

# Positive indexing (starts at 0)
print(fruits[0])   # apple
print(fruits[2])   # cherry

# Negative indexing (starts from end)
print(fruits[-1])  # date (last item)
print(fruits[-2])  # cherry (second from end)
```

> ✅ **Rule**: Index 0 is the first element. Index -1 is the last element.

### Slicing: Getting Subsets
```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Syntax: list[start:stop:step]
print(numbers[2:5])    # [2, 3, 4] (stop is excluded)
print(numbers[:3])     # [0, 1, 2] (from beginning)
print(numbers[7:])     # [7, 8, 9] (to end)
print(numbers[::2])    # [0, 2, 4, 6, 8] (every 2nd item)
print(numbers[::-1])   # [9, 8, 7...0] (reverse)
```

### Modifying Lists
```python
fruits = ["apple", "banana", "cherry"]

# Change an item
fruits[1] = "blueberry"
print(fruits)  # ['apple', 'blueberry', 'cherry']

# Add items
fruits.append("date")        # Add to end
fruits.insert(1, "apricot")  # Insert at index 1

# Remove items
fruits.remove("cherry")      # Remove by value
last = fruits.pop()          # Remove and return last item
del fruits[0]                # Delete by index

# Clear all
fruits.clear()
```

### Essential List Methods
```python
numbers = [3, 1, 4, 1, 5, 9, 2, 6]

# Sorting
numbers.sort()              # Sort in place
sorted_nums = sorted(numbers)  # Return new sorted list

# Reversing
numbers.reverse()           # Reverse in place

# Finding
numbers.count(1)            # How many times 1 appears
numbers.index(5)            # Index of first occurrence of 5

# Length
len(numbers)                # Number of items
```

### List Iteration
```python
fruits = ["apple", "banana", "cherry"]

# Basic iteration
for fruit in fruits:
    print(fruit)

# With index
for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")

# Output:
# 0: apple
# 1: banana
# 2: cherry
```

---

## 3.3 Tuples: Immutable Lists

Tuples are like lists, but once created, they cannot be changed. Use them for data that should never be modified.

### Creating Tuples
```python
# Empty tuple
empty = ()

# Single item (note the comma!)
single = (5,)

# Multiple items
coordinates = (10, 20)
person = ("Alice", 25, "Engineer")
```

### Why Use Tuples?
1. **Protection**: Data cannot be accidentally modified
2. **Performance**: Slightly faster than lists
3. **Dictionary keys**: Tuples can be dictionary keys, lists cannot
4. **Unpacking**: Clean way to return multiple values from functions

```python
# Tuple unpacking
coordinates = (10, 20)
x, y = coordinates
print(x)  # 10
print(y)  # 20

# Swapping variables
a, b = 5, 10
a, b = b, a  # Swap using tuple unpacking
```

### Accessing Tuples
```python
person = ("Alice", 25, "Engineer")

# Same indexing as lists
print(person[0])   # Alice
print(person[-1])  # Engineer

# Slicing works too
print(person[:2])  # ('Alice', 25)

# But this will error:
# person[0] = "Bob"  # TypeError: 'tuple' object does not support item assignment
```

---

## 3.4 Sets: Unique Collections

Sets are unordered collections of unique values. They are extremely fast for checking membership.

### Creating Sets
```python
# Empty set (must use set(), not {})
empty = set()

# Set with values
numbers = {1, 2, 3, 4, 5}

# From a list (duplicates removed)
numbers = set([1, 2, 2, 3, 3, 3])
print(numbers)  # {1, 2, 3}
```

### Set Operations
```python
# Adding items
numbers = {1, 2, 3}
numbers.add(4)
numbers.update([5, 6, 7])  # Add multiple

# Removing items
numbers.remove(3)    # Error if not found
numbers.discard(10)  # No error if not found
numbers.pop()        # Remove arbitrary item

# Membership testing (VERY FAST)
if 5 in numbers:
    print("Found!")
```

### Mathematical Set Operations
```python
set_a = {1, 2, 3, 4, 5}
set_b = {4, 5, 6, 7, 8}

# Union (all items from both)
print(set_a | set_b)  # {1, 2, 3, 4, 5, 6, 7, 8}

# Intersection (items in both)
print(set_a & set_b)  # {4, 5}

# Difference (in A but not B)
print(set_a - set_b)  # {1, 2, 3}

# Symmetric difference (in A or B but not both)
print(set_a ^ set_b)  # {1, 2, 3, 6, 7, 8}
```

### Common Use Case: Removing Duplicates
```python
# Remove duplicates from a list
items = [1, 2, 2, 3, 4, 4, 5]
unique_items = list(set(items))
print(unique_items)  # [1, 2, 3, 4, 5] (order may vary)
```

---

## 3.5 Dictionaries: Key-Value Pairs

Dictionaries store data as key-value pairs. They are Python's most powerful data structure.

### Creating Dictionaries
```python
# Empty dictionary
empty = {}

# Dictionary with values
person = {
    "name": "Alice",
    "age": 25,
    "city": "New York"
}

# Using dict()
person = dict(name="Alice", age=25, city="New York")
```

### Accessing Values
```python
person = {"name": "Alice", "age": 25, "city": "New York"}

# Access by key
print(person["name"])  # Alice

# Safe access with get() (doesn't error if key missing)
print(person.get("age"))       # 25
print(person.get("country"))   # None
print(person.get("country", "USA"))  # USA (default value)
```

### Modifying Dictionaries
```python
person = {"name": "Alice", "age": 25}

# Add or update
person["age"] = 26         # Update existing
person["city"] = "Boston"  # Add new

# Remove items
del person["city"]         # Delete by key
age = person.pop("age")    # Remove and return value
person.clear()             # Remove all
```

### Dictionary Methods
```python
person = {"name": "Alice", "age": 25, "city": "New York"}

# Get all keys
print(person.keys())    # dict_keys(['name', 'age', 'city'])

# Get all values
print(person.values())  # dict_values(['Alice', 25, 'New York'])

# Get key-value pairs
print(person.items())   # dict_items([('name', 'Alice'), ('age', 25), ('city', 'New York')])

# Check if key exists
if "name" in person:
    print("Name found")
```

### Iterating Through Dictionaries
```python
person = {"name": "Alice", "age": 25, "city": "New York"}

# Iterate over keys
for key in person:
    print(key)

# Iterate over values
for value in person.values():
    print(value)

# Iterate over both
for key, value in person.items():
    print(f"{key}: {value}")

# Output:
# name: Alice
# age: 25
# city: New York
```

---

## 3.6 Choosing the Right Data Structure

Use this decision tree:

```
Need key-value pairs? → Dictionary
Need only unique values? → Set
Data should never change? → Tuple
Everything else → List
```

### Real-World Examples
```python
# User profile (key-value) → Dictionary
user = {"username": "john_doe", "email": "john@example.com", "age": 30}

# Shopping cart items (order matters, can change) → List
cart = ["apple", "banana", "milk"]

# GPS coordinates (fixed, shouldn't change) → Tuple
location = (40.7128, -74.0060)

# Unique tags (no duplicates) → Set
tags = {"python", "programming", "tutorial"}
```

---

## 3.7 Built-in Functions for Collections

### Common to All
```python
data = [1, 2, 3, 4, 5]

len(data)      # Length: 5
min(data)      # Minimum: 1
max(data)      # Maximum: 5
sum(data)      # Sum: 15
sorted(data)   # New sorted list
```

### Type Conversions
```python
# List to set
my_list = [1, 2, 2, 3]
my_set = set(my_list)  # {1, 2, 3}

# Set to list
my_list = list(my_set)  # [1, 2, 3]

# String to list
letters = list("hello")  # ['h', 'e', 'l', 'l', 'o']

# List to tuple
my_tuple = tuple(my_list)
```

---

## 3.8 Common Data Structure Patterns

### Pattern 1: Counting Items
```python
# Count occurrences
fruits = ["apple", "banana", "apple", "cherry", "banana", "apple"]

fruit_count = {}
for fruit in fruits:
    if fruit in fruit_count:
        fruit_count[fruit] += 1
    else:
        fruit_count[fruit] = 1

print(fruit_count)  # {'apple': 3, 'banana': 2, 'cherry': 1}
```

### Pattern 2: Grouping Data
```python
# Group students by grade
students = [
    {"name": "Alice", "grade": "A"},
    {"name": "Bob", "grade": "B"},
    {"name": "Charlie", "grade": "A"}
]

by_grade = {}
for student in students:
    grade = student["grade"]
    if grade not in by_grade:
        by_grade[grade] = []
    by_grade[grade].append(student["name"])

print(by_grade)  # {'A': ['Alice', 'Charlie'], 'B': ['Bob']}
```

### Pattern 3: Finding Duplicates
```python
numbers = [1, 2, 3, 2, 4, 5, 3]

seen = set()
duplicates = set()

for num in numbers:
    if num in seen:
        duplicates.add(num)
    else:
        seen.add(num)

print(duplicates)  # {2, 3}
```

---

## ✅ Chapter 3 Summary

You've now mastered Python's four core data structures:

**Lists**: Ordered, mutable, allow duplicates. Your default choice.
**Tuples**: Ordered, immutable. Use for fixed data.
**Sets**: Unordered, unique values. Use for membership testing.
**Dictionaries**: Key-value pairs. Use for structured data.

You can now:
- Choose the right structure for any task
- Access and modify data efficiently
- Iterate through collections
- Apply common data manipulation patterns

This is the foundation for all real-world Python programming. Everything from web apps to data science builds on these structures.

---