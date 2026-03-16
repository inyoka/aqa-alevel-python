# Week 4: Exception Handling and Robust Programs

## Core Focus
Understanding how Python errors arise, how to handle them gracefully using `try`/`except`/`else`/`finally`, how to raise exceptions intentionally, and how to write programs that do not crash unexpectedly.

## Prior Knowledge Assumed
- Basic `try`/`except FileNotFoundError` (GCSE Week 7)
- Input validation using `while` loops
- Functions and return values
- An understanding that programs can fail at runtime

## Learning Objectives
- Distinguish between syntax errors, runtime errors, and logical errors
- Use `try`, `except`, `else`, and `finally` correctly
- Handle multiple exception types in a single block
- Raise exceptions deliberately with `raise`
- Write robust input-validation functions using exception handling
- Recognise logical errors that do not crash but produce wrong output
- Explain the purpose of exception handling in robust programs

---

## Lesson Content

### 1. Types of Python Error

#### Syntax Errors
Caught before the program runs. The code cannot be parsed.
```python
# SyntaxError: missing colon
if x > 5
    print(x)

# SyntaxError: invalid syntax
print("Hello"
```

#### Runtime Errors (Exceptions)
Occur while the program is running, even if the syntax is correct.

```python
# ValueError: invalid literal for int()
age = int("seventeen")

# TypeError: unsupported operand type(s)
result = "10" + 5

# ZeroDivisionError
average = 100 / 0

# IndexError: list index out of range
items = [1, 2, 3]
print(items[10])

# KeyError: key not found in dictionary
student = {"name": "Alice"}
print(student["score"])

# NameError: name not defined
print(undefined_variable)

# FileNotFoundError
with open("missing.txt") as f:
    data = f.read()
```

#### Logical Errors
The code runs without crashing but produces incorrect output. These are the hardest errors to find.

```python
# Logical error: off-by-one in average calculation
def average(numbers):
    return sum(numbers) / len(numbers) - 1   # -1 is wrong but won't crash

# Logical error: wrong operator
def is_even(n):
    return n % 2 == 1   # Should be == 0
```

---

### 2. `try` and `except`

Basic structure:
```python
try:
    # Code that might raise an exception
    value = int(input("Enter a number: "))
    result = 100 / value
    print(f"Result: {result}")
except ValueError:
    print("That was not a valid integer.")
except ZeroDivisionError:
    print("Cannot divide by zero.")
```

**Catching multiple exceptions:**
```python
try:
    data = int(input("Enter a number: "))
    result = 10 / data
except (ValueError, ZeroDivisionError) as e:
    print(f"Input error: {e}")
```

**Catching any exception (use sparingly):**
```python
try:
    risky_operation()
except Exception as e:
    print(f"An unexpected error occurred: {e}")
```

> **Best practice:** Always catch the most specific exception you can. Catching bare `Exception` hides bugs.

---

### 3. `else` and `finally`

- `else` runs if the `try` block **did not** raise an exception
- `finally` runs **always**, whether or not an exception occurred

```python
try:
    value = int(input("Enter a number: "))
    result = 10 / value
except ValueError:
    print("Not a valid integer.")
except ZeroDivisionError:
    print("Cannot divide by zero.")
else:
    # Only runs if no exception occurred
    print(f"Result: {result}")
finally:
    # Always runs — e.g. for cleanup
    print("Calculation attempt complete.")
```

**`finally` for resource cleanup:**
```python
file_handle = None
try:
    file_handle = open("data.txt", "r")
    content = file_handle.read()
    print(content)
except FileNotFoundError:
    print("File not found.")
finally:
    if file_handle:
        file_handle.close()   # Always close the file
```

In practice, the `with` statement handles this more cleanly:
```python
try:
    with open("data.txt", "r") as f:
        content = f.read()
    print(content)
except FileNotFoundError:
    print("File not found.")
```

---

### 4. Raising Exceptions

You can raise exceptions deliberately when input or state is invalid.

```python
def calculate_square_root(n):
    """Return the square root of n. Raises ValueError if n is negative."""
    if n < 0:
        raise ValueError(f"Cannot take square root of a negative number: {n}")
    return n ** 0.5

try:
    print(calculate_square_root(25))    # 5.0
    print(calculate_square_root(-4))    # Raises ValueError
except ValueError as e:
    print(f"Error: {e}")
```

Raising exceptions makes your functions self-defending: callers know immediately when they have passed invalid arguments.

```python
def set_speed(speed):
    """Set speed in km/h. Must be between 0 and 200."""
    if not isinstance(speed, (int, float)):
        raise TypeError(f"Speed must be a number, got {type(speed).__name__}")
    if speed < 0 or speed > 200:
        raise ValueError(f"Speed {speed} is out of range [0, 200]")
    return speed
```

---

### 5. Robust Input Validation

Combining loops and exception handling to guarantee valid input:

```python
def get_integer(prompt, min_val=None, max_val=None):
    """
    Prompt the user for an integer, repeating until valid.
    Optionally enforce a minimum and maximum value.
    """
    while True:
        try:
            value = int(input(prompt))
            if min_val is not None and value < min_val:
                print(f"Value must be at least {min_val}.")
                continue
            if max_val is not None and value > max_val:
                print(f"Value must be at most {max_val}.")
                continue
            return value
        except ValueError:
            print("Please enter a whole number.")

age = get_integer("Enter your age: ", min_val=0, max_val=120)
score = get_integer("Enter a score (0-100): ", min_val=0, max_val=100)
print(f"Age: {age}, Score: {score}")
```

```python
def get_float(prompt):
    """Prompt the user for a float, repeating until valid."""
    while True:
        try:
            return float(input(prompt))
        except ValueError:
            print("Please enter a valid decimal number.")

def get_choice(prompt, valid_options):
    """Prompt the user to choose from a set of options."""
    while True:
        choice = input(prompt).strip().lower()
        if choice in valid_options:
            return choice
        print(f"Please enter one of: {', '.join(valid_options)}")
```

---

### 6. Practical Error Handling Patterns

#### Safe Dictionary Lookup
```python
# Fragile
grade = student["grade"]   # KeyError if "grade" missing

# Robust using .get()
grade = student.get("grade", "Unknown")

# Or with try/except
try:
    grade = student["grade"]
except KeyError:
    grade = "Unknown"
```

#### Safe Type Conversion
```python
def safe_int(value, default=0):
    """Convert value to int, returning default on failure."""
    try:
        return int(value)
    except (ValueError, TypeError):
        return default

print(safe_int("42"))       # 42
print(safe_int("hello"))    # 0
print(safe_int(None, -1))   # -1
```

#### Safe File Reading
```python
def read_file_lines(filename):
    """
    Read all lines from filename.
    Returns a list of stripped lines, or an empty list on error.
    """
    try:
        with open(filename, "r") as f:
            return [line.strip() for line in f if line.strip()]
    except FileNotFoundError:
        print(f"Warning: {filename} not found. Starting with empty data.")
        return []
    except PermissionError:
        print(f"Error: No permission to read {filename}.")
        return []
```

---

### 7. Logical Errors: Finding Bugs That Don't Crash

These require careful testing, not exception handling.

```python
# Bug: calculates median incorrectly
def median(numbers):
    sorted_list = sorted(numbers)
    mid = len(sorted_list) // 2
    return sorted_list[mid]   # Wrong for even-length lists

# Correct version
def median(numbers):
    sorted_list = sorted(numbers)
    n = len(sorted_list)
    mid = n // 2
    if n % 2 == 0:
        return (sorted_list[mid - 1] + sorted_list[mid]) / 2
    return sorted_list[mid]

# Always test with both odd and even length inputs!
print(median([1, 2, 3]))       # 2
print(median([1, 2, 3, 4]))    # 2.5
```

**Debugging strategies for logical errors:**
1. Add `print()` statements to inspect variable values at key points
2. Test with the simplest possible input first
3. Test boundary values and edge cases
4. Trace through the code manually on paper
5. Isolate the faulty function with targeted test inputs

---

## Example Activities

### Activity 1: Error Identification Drill
Show students the following code snippets. For each one, identify the type of error (syntax, runtime, or logical) and explain what causes it:

```python
# Snippet 1
for i in range(5)
    print(i)

# Snippet 2
numbers = [1, 2, 3]
print(numbers[3])

# Snippet 3
def is_adult(age):
    return age > 18   # Should be >= 18

# Snippet 4
total = 0
for n in [1, 2, 3, 4]:
    total = n   # Should be += n
```

### Activity 2: Wrap and Protect
Take the following functions and add appropriate exception handling:

```python
def divide(a, b):
    return a / b

def get_item(lst, index):
    return lst[index]

def read_value(filename, key):
    with open(filename) as f:
        data = {}
        for line in f:
            k, v = line.strip().split("=")
            data[k] = v
    return data[key]
```

### Activity 3: Validation Function Library
Write a module of reusable validation functions:
- `get_integer(prompt, min_val, max_val)`
- `get_positive_float(prompt)`
- `get_non_empty_string(prompt)`
- `get_yes_no(prompt)` — returns `True` for "yes", `False` for "no"

### Activity 4: Logical Error Hunt
Students are given a working but buggy program that calculates a student's grade average and letter grade. The program runs without error but produces wrong answers for certain inputs. Students must write test cases to reveal the bugs and then fix them.

---

## Misconceptions / Likely Errors

| Misconception | Reality |
|---|---|
| `except:` with no type catches everything safely | Bare `except` also catches `KeyboardInterrupt` and `SystemExit` — use `except Exception` at minimum |
| `finally` only runs if there is an exception | `finally` runs **always**, with or without an exception |
| `raise ValueError` and `raise ValueError()` are the same | Both work, but `raise ValueError("message")` is better practice as it includes a helpful message |
| Exception handling replaces the need for validation logic | Exception handling is for *unexpected* errors; predictable invalid inputs should also be validated before they arise |
| "If it doesn't crash, it's correct" | Logical errors produce wrong answers silently — always test with known correct outputs |

---

## Assessment Opportunities

- **Trace:** "Step through the following `try`/`except`/`else`/`finally` block and state what is printed for each of these inputs: `5`, `0`, `"hello"`."
- **Exam-style:** "Write a function `safe_divide(a, b)` that returns `a / b`, or the string `"Error"` if `b` is zero."
- **Code correction:** Provide a program that uses `try`/`except` but catches the wrong exception type, causing a real error to go unhandled.
- **Short answer:** "State one advantage of using exception handling over simply checking for errors with `if` statements."

---

## Homework / Independent Study

1. **Practice task:** Write a menu-driven program (at least three options) where every user input is validated using exception handling. The program must never crash regardless of what the user types.

2. **Bug hunt:** Write (or find online) a small Python program that contains exactly three logical errors — none of which cause a crash. Swap with a classmate and see if they can find your bugs using test cases.

3. **Extend your validation library:** Add `get_date(prompt)` — accepts a string in DD/MM/YYYY format and returns a tuple `(day, month, year)` of integers, raising a `ValueError` with a clear message if the format is wrong or the date is invalid.

4. **Research:** Look up Python's `assert` statement. Write three examples of using `assert` to check preconditions at the start of a function. What happens when an assertion fails?
