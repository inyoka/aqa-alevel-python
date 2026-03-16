# Week 2: Advanced Functions

## Core Focus
Going significantly further with functions: default parameters, `*args`, `**kwargs`, lambda expressions, `map()`, `filter()`, and the idea of functions as first-class objects.

## Prior Knowledge Assumed
- Defining functions with parameters and return values (GCSE Week 6)
- Basic default parameters (introduced at GCSE)
- Scope: local and global variables
- Lists, loops, and comprehensions

## Learning Objectives
- Use default parameter values confidently
- Write functions that accept variable numbers of arguments with `*args` and `**kwargs`
- Write and use lambda (anonymous) functions
- Use `map()` and `filter()` with functions or lambdas
- Use `sorted()` with a custom `key` function
- Understand functions as first-class objects: passed as arguments and returned from functions
- Recognise and write simple closures

---

## Lesson Content

### 1. Default Parameters — Revisited and Extended

GCSE introduced default parameters briefly. At A-Level, they are used more deliberately.

```python
def greet(name, greeting="Hello", punctuation="!"):
    """Return a greeting string."""
    return f"{greeting}, {name}{punctuation}"

print(greet("Alice"))                   # Hello, Alice!
print(greet("Bob", "Good morning"))     # Good morning, Bob!
print(greet("Charlie", punctuation="."))# Hello, Charlie.
```

**Important rule:** parameters with defaults must come *after* parameters without defaults.

```python
# This raises a SyntaxError
def bad(x=1, y):   # y has no default but comes after x which does
    pass

# This is fine
def good(x, y=1):
    pass
```

---

### 2. `*args` — Variable Positional Arguments

Use `*args` when you do not know in advance how many positional arguments will be passed.

```python
def total(*numbers):
    """Return the sum of any number of arguments."""
    result = 0
    for n in numbers:
        result += n
    return result

print(total(1, 2, 3))           # 6
print(total(10, 20))            # 30
print(total(5, 5, 5, 5, 5))    # 25
```

Inside the function, `numbers` is an ordinary **tuple**.

```python
def describe(*items):
    print(f"Received {len(items)} items:")
    for item in items:
        print(f"  - {item}")

describe("apple", "banana", "cherry")
```

You can mix regular parameters with `*args`, but `*args` must come after positional parameters:

```python
def multiply(factor, *numbers):
    """Multiply each number by factor."""
    return [factor * n for n in numbers]

print(multiply(3, 1, 2, 3, 4))  # [3, 6, 9, 12]
```

---

### 3. `**kwargs` — Variable Keyword Arguments

Use `**kwargs` when you want to accept any number of named (keyword) arguments.

```python
def display_info(**details):
    """Display any number of key-value details."""
    for key, value in details.items():
        print(f"{key}: {value}")

display_info(name="Alice", age=17, city="Leeds")
```

Inside the function, `details` is an ordinary **dict**.

```python
def create_profile(name, **attributes):
    """Create a profile dict for a named person."""
    profile = {"name": name}
    profile.update(attributes)
    return profile

p = create_profile("Bob", age=16, grade="A", sport="football")
print(p)
# {'name': 'Bob', 'age': 16, 'grade': 'A', 'sport': 'football'}
```

**Order of parameters:** regular → `*args` → keyword-only → `**kwargs`

```python
def example(a, b, *args, key="default", **kwargs):
    print(a, b, args, key, kwargs)

example(1, 2, 3, 4, key="special", x=10, y=20)
# 1 2 (3, 4) special {'x': 10, 'y': 20}
```

---

### 4. Lambda Functions

A **lambda** is a small, anonymous function defined in a single expression.

```python
# Regular function
def square(x):
    return x ** 2

# Equivalent lambda
square = lambda x: x ** 2

print(square(5))  # 25
```

Lambdas are most useful when you need a short function as an argument to another function.

```python
double = lambda x: x * 2
add = lambda x, y: x + y

print(double(7))     # 14
print(add(3, 4))     # 7
```

**When to use lambda vs def:**
- Use `lambda` for short, one-expression functions passed as arguments
- Use `def` when the function has a name, is reused, or has more than one expression

---

### 5. `map()` and `filter()`

#### `map(function, iterable)`
Apply a function to every element of an iterable. Returns a map object (convert to list to see values).

```python
numbers = [1, 2, 3, 4, 5]

# Square every number
squares = list(map(lambda x: x ** 2, numbers))
print(squares)  # [1, 4, 9, 16, 25]

# Convert strings to integers
strings = ["10", "20", "30"]
integers = list(map(int, strings))
print(integers)  # [10, 20, 30]
```

#### `filter(function, iterable)`
Keep only the elements for which the function returns `True`.

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Keep only even numbers
evens = list(filter(lambda x: x % 2 == 0, numbers))
print(evens)  # [2, 4, 6, 8, 10]

# Keep only strings longer than 4 characters
words = ["cat", "elephant", "dog", "rhinoceros", "bee"]
long_words = list(filter(lambda w: len(w) > 4, words))
print(long_words)  # ['elephant', 'rhinoceros']
```

**Note:** list comprehensions can often do the same job and are frequently preferred:
```python
evens = [x for x in numbers if x % 2 == 0]         # filter equivalent
squares = [x ** 2 for x in numbers]                 # map equivalent
```

---

### 6. `sorted()` with a Key Function

`sorted()` returns a new sorted list. The `key` parameter accepts a function that is applied to each element before comparing.

```python
words = ["banana", "apple", "cherry", "date"]

# Sort alphabetically (default)
print(sorted(words))
# ['apple', 'banana', 'cherry', 'date']

# Sort by length
print(sorted(words, key=len))
# ['date', 'apple', 'banana', 'cherry']

# Sort by last character
print(sorted(words, key=lambda w: w[-1]))
# ['banana', 'apple', 'date', 'cherry']
```

Sorting a list of dictionaries:
```python
students = [
    {"name": "Alice", "score": 85},
    {"name": "Bob", "score": 92},
    {"name": "Charlie", "score": 78},
]

# Sort by score, highest first
ranked = sorted(students, key=lambda s: s["score"], reverse=True)
for s in ranked:
    print(f"{s['name']}: {s['score']}")
```

---

### 7. Functions as First-Class Objects

In Python, functions are objects. They can be:
- Assigned to variables
- Passed as arguments
- Returned from other functions

```python
def apply(func, value):
    """Apply func to value and return the result."""
    return func(value)

def double(x):
    return x * 2

def square(x):
    return x ** 2

print(apply(double, 5))   # 10
print(apply(square, 5))   # 25
print(apply(abs, -7))     # 7
```

**Choosing a function at runtime:**
```python
def add(a, b): return a + b
def subtract(a, b): return a - b
def multiply(a, b): return a * b

operations = {
    "add": add,
    "subtract": subtract,
    "multiply": multiply,
}

choice = input("Operation (add/subtract/multiply): ")
a = float(input("First number: "))
b = float(input("Second number: "))

if choice in operations:
    result = operations[choice](a, b)
    print(f"Result: {result}")
else:
    print("Unknown operation.")
```

---

### 8. Closures

A **closure** is a function that captures and remembers variables from the enclosing scope, even after that scope has finished executing.

```python
def make_multiplier(factor):
    """Return a function that multiplies its argument by factor."""
    def multiplier(x):
        return x * factor   # factor is 'closed over'
    return multiplier

double = make_multiplier(2)
triple = make_multiplier(3)

print(double(10))  # 20
print(triple(10))  # 30
```

Closures are useful for creating specialised versions of functions without classes.

```python
def make_validator(min_val, max_val):
    """Return a function that checks a value is in [min_val, max_val]."""
    def validate(value):
        return min_val <= value <= max_val
    return validate

is_valid_score = make_validator(0, 100)
is_valid_age = make_validator(0, 120)

print(is_valid_score(85))   # True
print(is_valid_score(105))  # False
print(is_valid_age(17))     # True
```

---

## Example Activities

### Activity 1: `*args` Calculator
Write a function `stats(*numbers)` that accepts any number of integers and returns a dictionary containing the total, count, minimum, maximum, and average. Test it with different numbers of arguments.

### Activity 2: Sorting Challenge
Given a list of student dictionaries (name, score, year group), write three separate `sorted()` calls:
1. Sorted by score descending
2. Sorted alphabetically by name
3. Sorted by year group, then by score descending within each year group

### Activity 3: Build a Mini Pipeline
Using `map()` and `filter()`:
1. Start with a list of strings representing numbers with possible spaces, e.g. `[" 10 ", "abc", " 25 ", "7", "not a number"]`
2. Strip whitespace from each string
3. Filter to keep only those that contain digits only
4. Convert the remaining strings to integers
5. Return the sum

### Activity 4: Closure Factory
Create a function `make_discount(percentage)` that returns a function `apply_discount(price)`. Test that `make_discount(10)` gives a function that applies a 10% discount to any price.

---

## Misconceptions / Likely Errors

| Misconception | Reality |
|---|---|
| `*args` receives a list | `*args` receives a **tuple** — it is immutable |
| `**kwargs` must be used to accept keyword args | Regular named parameters also accept keyword arguments; `**kwargs` is for *arbitrary* named arguments |
| `lambda` can contain multiple statements | A lambda is a single **expression** only — use `def` for anything more complex |
| `map()` returns a list | `map()` returns a lazy iterator; wrap with `list()` to get a list |
| Lambdas are faster than `def` functions | There is no meaningful performance difference |
| A closure copies the value of the outer variable | A closure holds a **reference** to the variable, which matters in loops |

**Loop closure trap (common bug):**
```python
# Bug: all functions capture the SAME variable i
funcs = [lambda: i for i in range(3)]
print([f() for f in funcs])  # [2, 2, 2]  -- NOT [0, 1, 2]

# Fix: use a default argument to capture the value at definition time
funcs = [lambda i=i: i for i in range(3)]
print([f() for f in funcs])  # [0, 1, 2]
```

---

## Assessment Opportunities

- **Exam-style question:** "Write a function `apply_to_all(func, data)` that applies `func` to every item in `data` and returns the transformed list. Do not use `map()`."
- **Code reading:** Given a function using `*args` and `**kwargs`, trace through an example call and predict the output.
- **Spot the error:** Identify why `sorted(students, key=students["score"])` fails and correct it.
- **Short task:** Write a function that accepts any number of keyword arguments and returns them formatted as `"key=value"` strings in a list.

---

## Homework / Independent Study

1. **Practice task:** Write a `make_greeter(greeting)` closure factory. `make_greeter("Hello")` should return a function that, when called with a name, returns `"Hello, <name>!"`.

2. **Extend the pipeline:** Revisit Activity 3. Now also use `sorted()` with a key to sort the valid integers in descending order before returning them.

3. **Reading:** Look up Python's `functools.reduce()`. Write a short explanation of what it does and provide one example of using it to calculate the product of a list of numbers.

4. **Exam preparation:** Write three functions from the AQA specification that use `*args`: one that finds the maximum of any number of values, one that concatenates any number of strings, and one that returns only the positive values from any number of arguments.
