# Chapter 2: Control Flow & Logical Thinking

## 🎯 Learning Objectives
By the end of this chapter you will be able to:
1.  Implement decision making logic that branches based on conditions
2.  Correctly use comparison and logical operators
3.  Choose the right loop for any task
4.  Control loop behaviour with break, continue and pass
5.  Build and debug nested logic
6.  Avoid the most common logic bugs


---

> 💡 This is the single most important chapter in the entire course. Everything else you will ever write in Python builds on these concepts. Control flow is what turns a static list of instructions into an actual program that can make decisions and repeat work.


---

## 2.1 What is Control Flow?
Up until now all your programs ran line by line from top to bottom. Control flow allows you to change the order that code runs. You can choose to run some code only if something is true, and run other code over and over again.

This is the foundation of all programming.


---

## 2.2 Conditional Statements: Decision Making

This is how your program makes choices. There are only three rules you need to remember:
1.  `if` = run this block only if condition is True
2.  `elif` = only check this if all previous conditions were False
3.  `else` = run this if nothing else matched

> ✅ **Most Important Rule**: Python checks conditions from top to bottom, and runs only the first block that matches. It will ignore everything after that.

### The if statement
```python
age = 22

# This block will only run if age >= 18 is True
if age >= 18:
    print("You are an adult")
```

### if / else
Run one of two options, never both.
```python
temperature = 19

if temperature > 22:
    print("It is warm")
else:
    print("It is cool")
```

### if / elif / else Chain
This is the structure you will use 99% of the time for multiple outcomes.
```python
score = 87

if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
elif score >= 60:
    grade = "D"
else:
    grade = "F"

print(grade) # Output: B
```

> ❗ **Common Mistake**: If you use multiple separate `if` statements instead of `elif`, Python will check all of them, and multiple blocks can run. Only use `elif` when you want exactly one outcome.


---

## 2.3 Operators

### Comparison Operators
These always return either True or False. They are the building blocks of all logic.

| Operator | Meaning | Example | Result
|---|---|---|---|
| `==` | Is equal to | `5 == 5` | True
| `!=` | Is not equal to | `5 != 3` | True
| `>` | Greater than | `10 > 5` | True
| `<` | Less than | `3 < 7` | True
| `>=` | Greater or equal | `5 >= 5` | True
| `<=` | Less or equal | `2 <= 5` | True

> ✅ **Pro Tip**: Python is one of the only languages that allows chained comparisons. You can write `18 <= age < 65` instead of `age >= 18 and age < 65`. This is one of Python's best features, always use it.


---

### Logical Operators
Used to combine multiple conditions together. There are only three, and you will use them every day.

| Operator | Behaviour |
|---|---|
| `and` | True only if **both** sides are True |
| `or` | True if **at least one** side is True |
| `not` | Flips the result |

```python
# You can drive only if you are 18+ AND you have a license
age = 24
has_license = True

if age >= 18 and has_license:
    print("You can drive")
```

```python
# It is the weekend if it is Saturday OR Sunday
day = "Saturday"

if day == "Saturday" or day == "Sunday":
    print("Sleep in")
```

```python
# If you are NOT logged in
logged_in = False

if not logged_in:
    print("Please login")
```

> ❗ **Common Mistake**: Never write `if x == True`. Just write `if x:`. It is cleaner, more readable, and the official Python style.


---

## 2.4 Loops: Repetition
Loops let you run the same code many times, without copying and pasting. This is the single most powerful thing in programming.

✅ **Absolute Rule for choosing loops**:
*   Use `for` when you know **how many times** you want to loop
*   Use `while` when you don't know how many times you want to loop


---

### For Loop
The for loop iterates over a sequence. It will run once for every item in the sequence.

```python
# Iterate over a list
fruits = ["apple", "banana", "cherry"]

for fruit in fruits:
    print(fruit)
```

#### The range() function
This is the most common thing you will use with for loops. It generates a sequence of numbers.
*   `range(5)` = 0, 1, 2, 3, 4 ✅ runs exactly 5 times
*   `range(1, 5)` = 1, 2, 3, 4
*   `range(0, 10, 2)` = 0, 2, 4, 6, 8

> ✅ **Rule**: range always includes the start, and always excludes the end.

```python
# Count from 1 to 10
for number in range(1, 11):
    print(number)
```


---

### While Loop
The while loop runs as long as its condition remains True.
```python
# Count from 1 to 5
count = 1

while count <= 5:
    print(count)
    count = count + 1
```

> ❗ **Critical Warning**: You must change the variable inside the while loop. If you forget, you will create an infinite loop that will run forever. This is the most common bug with while loops.


---

## 2.5 Loop Control Statements
These three keywords let you override the normal behaviour of a loop.

| Keyword | What it does |
|---|---|
| `break` | Stop the loop completely right now, exit immediately |
| `continue` | Skip the rest of this iteration, go straight to the next one |
| `pass` | Do nothing. This is a placeholder for future code. |

### break example
```python
# Stop when we hit 5
for i in range(10):
    if i == 5:
        break
    print(i)

# Output: 0 1 2 3 4
```

### continue example
```python
# Skip number 3
for i in range(6):
    if i == 3:
        continue
    print(i)

# Output: 0 1 2 4 5
```

### pass example
```python
# I will write this code later
for i in range(10):
    pass
```


---

## 2.6 Nested Logic
You can put any control flow statement inside another one. This is how you build complex logic.

```python
# Check if someone can rent a car
age = 23
has_license = True

if age >= 21:
    if has_license:
        print("You can rent a car")
    else:
        print("You need a license")
else:
    print("You are too young")
```


---

## ✅ Chapter 2 Summary
This is the most important chapter in the course. You have now learned every single tool you need to implement any logic. Everything else in programming is just combinations of these concepts.

You now know:
*   How to make decisions with if/elif/else
*   How to combine conditions with operators
*   When to use for loops and when to use while loops
*   How to control loops with break and continue
*   How to build nested logic

If you master this chapter, everything else will be easy. If you don't master this chapter, everything else will be impossible.


---