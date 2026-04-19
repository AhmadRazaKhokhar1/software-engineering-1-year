# Chapter 5: Object-Oriented Programming & Error Handling

## 🎯 Learning Objectives
By the end of this chapter you will be able to:
1. Understand the principles of Object-Oriented Programming (OOP)
2. Create classes and instantiate objects
3. Use constructors, instance variables, and class variables
4. Implement inheritance to create class hierarchies
5. Apply encapsulation to protect data
6. Utilize polymorphism for flexible code
7. Handle exceptions gracefully
8. Create custom exception classes

---

> 💡 **Why This Matters**: OOP is not just a programming style; it's a way of thinking that models real-world entities and their relationships. Combined with proper error handling, it allows you to build robust, scalable, and maintainable applications that can handle real-world complexity.

---

## 5.1 What is Object-Oriented Programming?

OOP is a programming paradigm that organizes code around **objects** rather than functions and logic. An object is a self-contained unit that contains **data** (attributes) and **behavior** (methods).

### The Four Pillars of OOP:
1. **Encapsulation**: Bundling data and methods that operate on that data
2. **Inheritance**: Creating new classes based on existing ones
3. **Polymorphism**: Objects of different classes responding to the same method call
4. **Abstraction**: Hiding complex implementation details

### Why Use OOP?
- **Modularity**: Easier to maintain and debug
- **Reusability**: Classes can be reused across projects
- **Scalability**: Easier to extend functionality
- **Real-world modeling**: Objects correspond to real entities

---

## 5.2 Classes and Objects

### Classes: Blueprints for Objects
A class is a blueprint or template for creating objects.

```python
# Defining a simple class
class Dog:
    # Class attribute (shared by all instances)
    species = "Canis familiaris"
    
    # Initializer (constructor)
    def __init__(self, name, age):
        # Instance attributes (unique to each instance)
        self.name = name
        self.age = age
    
    # Instance method
    def bark(self):
        return f"{self.name} says woof!"
    
    def get_description(self):
        return f"{self.name} is {self.age} years old"
```

### Objects: Instances of Classes
An object is a specific instance created from a class.

```python
# Creating objects (instances)
dog1 = Dog("Buddy", 3)
dog2 = Dog("Molly", 5)

# Accessing attributes
print(dog1.name)        # Buddy
print(dog2.age)         # 5

# Calling methods
print(dog1.bark())      # Buddy says woof!
print(dog2.get_description())  # Molly is 5 years old

# Accessing class attribute
print(Dog.species)      # Canis familiaris
print(dog1.species)     # Canis familiaris (accessed via instance)
```

---

## 5.3 The `__init__` Method (Constructor)

The `__init__` method is called automatically when a new object is created. It initializes the object's attributes.

```python
class Student:
    def __init__(self, name, student_id, major):
        self.name = name
        self.student_id = student_id
        self.major = major
        self.grades = []  # Initialize empty list
    
    def add_grade(self, grade):
        self.grades.append(grade)
    
    def calculate_gpa(self):
        if not self.grades:
            return 0.0
        return sum(self.grades) / len(self.grades)

# Create student
student1 = Student("Alice", "S001", "Computer Science")
student1.add_grade(3.8)
student1.add_grade(4.0)

print(student1.calculate_gpa())  # 3.9
```

---

## 5.4 Instance vs Class Variables

| Feature | Instance Variables | Class Variables |
|---------|-------------------|-----------------|
| **Definition** | Inside `__init__` or methods | Inside class, outside methods |
| **Belongs to** | Each instance (object) | Class itself |
| **Access via** | `self.variable_name` | `self.variable_name` or `ClassName.variable_name` |
| **Purpose** | Store data unique to each instance | Store data shared by all instances |

```python
class BankAccount:
    # Class variable
    interest_rate = 0.03  # Shared by all accounts
    total_accounts = 0
    
    def __init__(self, account_holder, balance=0):
        # Instance variables
        self.account_holder = account_holder
        self.balance = balance
        self.account_number = BankAccount.total_accounts + 1
        
        # Update class variable
        BankAccount.total_accounts += 1
    
    def deposit(self, amount):
        self.balance += amount
        return self.balance
    
    def withdraw(self, amount):
        if amount <= self.balance:
            self.balance -= amount
            return self.balance
        else:
            return "Insufficient funds"
    
    @classmethod
    def update_interest_rate(cls, new_rate):
        cls.interest_rate = new_rate

# Create accounts
account1 = BankAccount("Alice", 1000)
account2 = BankAccount("Bob", 500)

print(f"Total accounts: {BankAccount.total_accounts}")  # 2
print(f"Interest rate: {BankAccount.interest_rate}")    # 0.03

# Change interest rate for all accounts
BankAccount.update_interest_rate(0.035)
print(f"New interest rate: {account1.interest_rate}")   # 0.035
```

---

## 5.5 Inheritance: Creating Class Hierarchies

Inheritance allows a class to inherit attributes and methods from another class.

### Base Class (Parent)
```python
class Vehicle:
    def __init__(self, make, model, year):
        self.make = make
        self.model = model
        self.year = year
        self.mileage = 0
    
    def drive(self, miles):
        self.mileage += miles
        print(f"Driving {miles} miles. Total mileage: {self.mileage}")
    
    def get_info(self):
        return f"{self.year} {self.make} {self.model}"
```

### Derived Class (Child)
```python
class Car(Vehicle):  # Inherit from Vehicle
    def __init__(self, make, model, year, doors):
        # Call parent class constructor
        super().__init__(make, model, year)
        # Add new attribute
        self.doors = doors
    
    # Override parent method
    def get_info(self):
        return f"{super().get_info()} with {self.doors} doors"
    
    # Add new method
    def honk(self):
        return "Beep beep!"

class Motorcycle(Vehicle):
    def __init__(self, make, model, year, has_sidecar):
        super().__init__(make, model, year)
        self.has_sidecar = has_sidecar
    
    def wheelie(self):
        return "Doing a wheelie!"

# Using the classes
my_car = Car("Toyota", "Camry", 2022, 4)
my_motorcycle = Motorcycle("Harley", "Davidson", 2021, False)

print(my_car.get_info())       # 2022 Toyota Camry with 4 doors
print(my_motorcycle.get_info()) # 2021 Harley Davidson

my_car.drive(50)               # Driving 50 miles. Total mileage: 50
print(my_car.honk())           # Beep beep!
print(my_motorcycle.wheelie()) # Doing a wheelie!
```

---

## 5.6 Encapsulation: Protecting Data

Encapsulation is about restricting direct access to an object's data and providing controlled access through methods.

### Using Private Attributes
```python
class BankAccount:
    def __init__(self, account_holder, balance):
        self.account_holder = account_holder  # Public
        self.__balance = balance              # Private (double underscore)
    
    # Getter method
    def get_balance(self):
        return self.__balance
    
    # Setter method with validation
    def deposit(self, amount):
        if amount > 0:
            self.__balance += amount
            return True
        return False
    
    def withdraw(self, amount):
        if 0 < amount <= self.__balance:
            self.__balance -= amount
            return True
        return False

# Usage
account = BankAccount("Alice", 1000)

# This works
print(account.get_balance())  # 1000
account.deposit(500)

# This doesn't work (Python still allows access but with name mangling)
# print(account.__balance)  # AttributeError
# But this works (not recommended):
# print(account._BankAccount__balance)  # Name mangling
```

### Property Decorators (Pythonic Encapsulation)
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self._age = age  # Protected attribute
    
    @property
    def age(self):
        return self._age
    
    @age.setter
    def age(self, value):
        if 0 <= value <= 120:
            self._age = value
        else:
            raise ValueError("Age must be between 0 and 120")
    
    @age.deleter
    def age(self):
        print("Deleting age")
        del self._age

# Usage
person = Person("Alice", 25)
print(person.age)      # 25 (calls getter)

person.age = 30        # Calls setter
print(person.age)      # 30

person.age = 150       # Raises ValueError: Age must be between 0 and 120
```

---

## 5.7 Polymorphism: Many Forms

Polymorphism allows objects of different classes to be treated as objects of a common superclass.

### Method Overriding
```python
class Shape:
    def area(self):
        raise NotImplementedError("Subclass must implement this method")
    
    def perimeter(self):
        raise NotImplementedError("Subclass must implement this method")

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    def area(self):
        return self.width * self.height
    
    def perimeter(self):
        return 2 * (self.width + self.height)

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius
    
    def area(self):
        return 3.14159 * self.radius ** 2
    
    def perimeter(self):
        return 2 * 3.14159 * self.radius

# Using polymorphism
shapes = [Rectangle(4, 5), Circle(3)]

for shape in shapes:
    print(f"Area: {shape.area():.2f}")
    print(f"Perimeter: {shape.perimeter():.2f}")
```

### Duck Typing (Python's Approach)
"In Python, if it walks like a duck and quacks like a duck, it's a duck."

```python
class Duck:
    def quack(self):
        return "Quack!"

class Person:
    def quack(self):
        return "I'm pretending to be a duck!"

def make_it_quack(thing):
    print(thing.quack())

# Both work because they have a quack() method
make_it_quack(Duck())    # Quack!
make_it_quack(Person())  # I'm pretending to be a duck!
```

---

## 5.8 Exception Handling: Graceful Error Management

### Basic Try-Except
```python
try:
    # Code that might raise an exception
    result = 10 / 0
except ZeroDivisionError:
    # What to do if the exception occurs
    print("Cannot divide by zero!")
```

### Multiple Exceptions
```python
try:
    num = int(input("Enter a number: "))
    result = 100 / num
    print(f"Result: {result}")
except ValueError:
    print("That's not a valid number!")
except ZeroDivisionError:
    print("Cannot divide by zero!")
except Exception as e:
    print(f"An unexpected error occurred: {e}")
```

### Else and Finally
```python
try:
    file = open("data.txt", "r")
    content = file.read()
except FileNotFoundError:
    print("File not found")
except PermissionError:
    print("No permission to read file")
else:
    # Runs only if no exception occurred
    print(f"File content: {content}")
    file.close()
finally:
    # Always runs, regardless of exceptions
    print("Cleanup complete")
```

### Raising Exceptions
```python
def validate_age(age):
    if age < 0:
        raise ValueError("Age cannot be negative")
    elif age > 120:
        raise ValueError("Age seems unrealistic")
    return True

try:
    validate_age(-5)
except ValueError as e:
    print(f"Invalid age: {e}")
```

---

## 5.9 Custom Exception Classes

Create your own exception types for specific error conditions.

```python
class InsufficientFundsError(Exception):
    """Raised when there's not enough money in the account"""
    def __init__(self, balance, amount):
        self.balance = balance
        self.amount = amount
        message = f"Insufficient funds: {balance} available, {amount} requested"
        super().__init__(message)

class BankAccount:
    def __init__(self, balance):
        self.balance = balance
    
    def withdraw(self, amount):
        if amount > self.balance:
            raise InsufficientFundsError(self.balance, amount)
        self.balance -= amount
        return self.balance

# Usage
account = BankAccount(100)

try:
    account.withdraw(150)
except InsufficientFundsError as e:
    print(f"Error: {e}")
    print(f"Balance: {e.balance}, Requested: {e.amount}")
```

---

## 5.10 Special Methods (Magic/Dunder Methods)

Special methods allow you to define how objects behave with built-in operations.

```python
class Book:
    def __init__(self, title, author, pages):
        self.title = title
        self.author = author
        self.pages = pages
    
    # String representation for users
    def __str__(self):
        return f"'{self.title}' by {self.author}"
    
    # Detailed representation for developers
    def __repr__(self):
        return f"Book('{self.title}', '{self.author}', {self.pages})"
    
    # Compare books by pages
    def __lt__(self, other):
        return self.pages < other.pages
    
    # Add two books (creates a collection)
    def __add__(self, other):
        return BookCollection([self, other])
    
    # Get length (number of pages)
    def __len__(self):
        return self.pages

class BookCollection:
    def __init__(self, books):
        self.books = books
    
    def __str__(self):
        return f"Collection of {len(self.books)} books"

# Usage
book1 = Book("Python Basics", "John Doe", 300)
book2 = Book("Advanced Python", "Jane Smith", 500)

print(str(book1))        # 'Python Basics' by John Doe
print(repr(book1))       # Book('Python Basics', 'John Doe', 300)
print(book1 < book2)     # True (300 < 500)
print(len(book1))        # 300

collection = book1 + book2
print(collection)        # Collection of 2 books
```

---

## 5.11 Best Practices in OOP

### 1. Follow the Single Responsibility Principle
Each class should have one job.

```python
# GOOD: Separate classes for separate responsibilities
class Customer:
    def __init__(self, name, email):
        self.name = name
        self.email = email

class Order:
    def __init__(self, customer, items):
        self.customer = customer
        self.items = items
    
    def calculate_total(self):
        return sum(item.price for item in self.items)

# BAD: One class doing too much
class CustomerOrder:
    def __init__(self, name, email, items):
        self.name = name
        self.email = email
        self.items = items
    # Too many responsibilities...
```

### 2. Use Composition Over Inheritance When Possible
```python
# Composition: Has-a relationship
class Engine:
    def start(self):
        return "Engine started"

class Car:
    def __init__(self):
        self.engine = Engine()  # Car has an engine
    
    def start(self):
        return self.engine.start()

# Inheritance: Is-a relationship (use when appropriate)
class ElectricCar(Car):
    def __init__(self):
        super().__init__()
        self.battery_level = 100
```

### 3. Keep Classes Small and Focused
- If a class has more than 5-7 methods, consider splitting it
- If a method is longer than 20 lines, consider breaking it up

### 4. Document Your Classes
```python
class BankAccount:
    """
    A class representing a bank account.
    
    Attributes:
        account_holder (str): Name of the account holder
        balance (float): Current account balance
        account_number (int): Unique account identifier
    """
    
    def __init__(self, account_holder, balance=0):
        """Initialize a bank account with holder name and optional balance."""
        self.account_holder = account_holder
        self.balance = balance
```

---

## ✅ Chapter 5 Summary

You've now mastered the core concepts of Object-Oriented Programming and Error Handling:

**OOP Concepts**:
- Classes and objects as blueprints and instances
- Constructors with `__init__`
- Instance vs class variables
- Inheritance for code reuse
- Encapsulation for data protection
- Polymorphism for flexible interfaces
- Special methods for custom behavior

**Error Handling**:
- Try-except blocks for graceful error recovery
- Multiple exception types
- Else and finally clauses
- Raising exceptions
- Creating custom exception classes

These skills allow you to build professional, maintainable, and robust applications that can handle complexity and errors gracefully.

---